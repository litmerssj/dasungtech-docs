# ANASA PostgreSQL 단일 데이터베이스·5개 스키마 전환 리스크 검토

| 항목 | 내용 |
| --- | --- |
| 작성일 | 2026-09-01 |
| 대상 | 다성테크 ANASA ERP의 SQL Server → PostgreSQL 전환안 |
| 검토안 | PostgreSQL 데이터베이스 1개 안에 `miscm`, `mispd`, `missa`, `misto`, `mistw` 5개 스키마 배치 |
| 결론 | 조건부 권장. 교차 DB 결합을 줄이는 과도기 목표로는 적합하지만, 그 자체로 고가용성·성능 격리·업무 모듈 분리를 보장하지 않음 |
| 기준일 | ANASA 라이브 SQL Server 스냅샷 2026-08-24, FE/BE 연결 산출물 기준 |
| 제외 | 실제 행 데이터, Query Store 호출량·실행계획, AWS 실시간 설정, 운영 RTO/RPO 승인값 |

## 1. 결론

ANASA에 대해 **PostgreSQL 한 데이터베이스와 5개 업무 스키마를 우선 목표로 삼는 것은 합리적**이다. 현재 SP가 `MISCM`, `MISPD`, `MISSA`, `MISTO`, `MISTW` 사이의 테이블과 함수를 직접 참조하므로, 이를 PostgreSQL의 서로 다른 데이터베이스로 나누면 `postgres_fdw`, 네트워크 장애, 분산 트랜잭션과 장애 복구를 추가로 해결해야 한다. 하나의 데이터베이스 안에 두면 스키마 간 조인과 한 트랜잭션의 원자성은 유지하기 쉽다.

다만 다음을 분명히 해야 한다.

- 스키마는 논리적 네임스페이스와 권한 경계이지, CPU·메모리·WAL·버퍼 캐시·VACUUM·장애의 물리적 격리 경계가 아니다.
- 한 데이터베이스, 한 클러스터의 장애·스토리지 부족·복구 작업은 5개 업무 영역에 동시에 영향을 준다.
- SQL Server SP를 PostgreSQL 함수/프로시저로 자동 번역할 수 있다는 뜻이 아니다. T-SQL, 타입, collation, 잠금, 결과셋, 인증 함수는 별도 재설계가 필요하다.
- 5개 스키마로 옮긴 뒤에도 교차 모듈 결합이 사라지는 것이 아니라 `miscm.x` 같은 명시적 스키마 참조로 바뀐다.

따라서 이 안은 **최종 격리 아키텍처가 아니라 1차 마이그레이션 토폴로지**로 승인하는 것이 안전하다. 운영 유일 원본으로 전환하기 전에는 복원 시험, 권한 시험, 동시성 시험과 핵심 업무 대사를 통과해야 한다.

## 2. ANASA에서 확인된 실제 결합도

2026-08-24 라이브 SQL Server 메타데이터와 SP 원문을 기준으로 다음을 확인했다.

| 항목 | 수량 | 해석 |
| --- | ---: | --- |
| 업무 데이터베이스 | 5 | `MISCM`, `MISPD`, `MISSA`, `MISTO`, `MISTW` |
| 테이블 | 606 | 5개 DB 합계 |
| 컬럼 | 11,208 | 실제 행 데이터는 스냅샷에 없음 |
| 라이브 SQL Server SP | 1,815 | 최신 BE manifest는 1,812개, 라이브에만 있는 SP 3개 |
| FE 화면 연결 고유 SP | 407 | 129개 화면에서 사용 |
| 화면 연결 쓰기 SP | 170 | INSERT/UPDATE/DELETE/MERGE를 포함하는 화면 연결 SP |
| SP 간 EXEC 관계 | 279 | 중첩 호출과 결과셋 계약에 영향 |
| 다른 DB를 참조하는 SP 파일 | 956 | 원문 토큰 기준. 주석만으로 판정하지 않음 |
| 관찰된 교차 DB 관계 | 254 | 생성 산출물의 테이블·컬럼 관계 기준 |
| 동적 SQL 표식 | 93 | 내부 TABLE 흐름이 완전히 펼쳐지지 않은 표식 |
| 선언된 외래키 | 1 | 데이터 이관 순서를 SP 업무규칙으로 보완해야 함 |

근거 산출물은 `anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z/README.md`, `anasa-procedure-reverse-engineering/generation-summary.json`, `anasa-procedure-reverse-engineering/data-flows/README.md`이다. 스냅샷에는 행 데이터와 비공개 함수 원문이 포함되지 않는다.

### 대표적인 교차 스키마 업무

작업일지 저장 SP `MISPD.dbo.str_PDWorkJournal_U`는 `MISPD`의 작업 데이터와 `MISTW.dbo.tbLogProductIn`을 함께 확인하고, 테이블 변수·savepoint·잠금 힌트·`MERGE`를 사용한다. 현재 화면의 `PATCH /api/v1/pdt-work-journals/work-report`가 이 SP를 직접 호출한다.

- 원문: `anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z/MISPD/modules/sql_stored_procedure/dbo/1313439753_str_PDWorkJournal_U.sql`
- 화면 연결: `anasa-procedure-reverse-engineering/screen-flows/screen-sp.tsv`의 `/d/work-journal`

주문 저장 SP `MISTW.dbo.proc_LOGB002_IU_01`은 SQL Server sequence와 중첩 SP 호출을 사용하고 내부 `BEGIN TRAN`에서 오류 시 `ROLLBACK`한 뒤 `ERRYN`, `ERRMSG`, 주문번호를 SELECT로 반환한다.

- 원문: `anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z/MISTW/modules/sql_stored_procedure/dbo/912722304_proc_LOGB002_IU_01.sql`

이런 업무는 5개 스키마로 합치면 호출 경로는 단순해지지만, 원자성·오류 반환·동시성 규칙을 다시 검증해야 한다.

## 3. 단일 데이터베이스·5개 스키마의 이점

### 3.1 현재 구조에 맞는 교차 영역 조회

SQL Server의 `MISPD.dbo.T`와 `MISTW.dbo.U`를 PostgreSQL의 `mispd.t`, `mistw.u`로 바꾸면 같은 데이터베이스에서 일반 조인으로 처리할 수 있다. 별도 DB 간 FDW 호출이나 네트워크 왕복이 없어 조회 경로와 장애 모델이 단순해진다.

### 3.2 한 트랜잭션으로 묶기 쉬움

한 PostgreSQL 데이터베이스 안에서는 서로 다른 스키마의 INSERT/UPDATE를 하나의 트랜잭션으로 커밋하거나 롤백할 수 있다. 주문·패킹·출고 또는 입고·재고처럼 여러 업무 영역을 동시에 갱신하는 경로의 데이터 원자성을 구현하기에 유리하다.

단, 이는 애플리케이션 또는 DB 함수가 같은 트랜잭션을 실제로 사용한다는 전제다. 스키마를 합치는 것만으로 현재 SP 내부의 SQL Server 트랜잭션 동작이 자동 보존되지는 않는다.

### 3.3 연결·백업·모니터링 단순화

애플리케이션은 업무 DB 5개에 대한 별도 연결 대신 하나의 데이터베이스 연결 풀을 사용할 수 있다. 전체 논리 백업과 전체 복원 절차도 하나의 일관된 대상이 된다. `pg_dump -n miscm` 같은 스키마 단위 추출도 가능하다.

그러나 운영 복구에서 “한 데이터베이스를 복원하면 원하는 한 업무만 복원된다”고 가정하면 안 된다. 스키마 간 의존성과 sequence, 함수, 뷰, 권한을 함께 복원해야 하며, 전체 복원은 5개 영역의 서비스 중단으로 이어질 수 있다.

## 4. 반드시 관리해야 할 문제

### 4.1 장애 범위가 5개 업무로 합쳐짐 — 높음

스키마는 PostgreSQL 프로세스, 클러스터, WAL, 스토리지, 메모리 캐시를 분리하지 않는다. 데이터베이스·클러스터 장애, 디스크 고갈, 긴 트랜잭션, 복구 작업이 5개 스키마에 동시에 영향을 준다.

특히 현재 ANASA처럼 생산·구매·물류·영업·공통 영역이 한 서버 자원을 공유할 때 한 대량 조회나 장기 쓰기 트랜잭션이 다른 업무의 지연으로 번질 수 있다.

대응:

- PostgreSQL 클러스터와 데이터베이스 디스크의 CPU·메모리·WAL·저장공간 알람을 전체 기준으로 운영한다.
- `statement_timeout`, `lock_timeout`, `idle_in_transaction_session_timeout`을 역할별로 설정한다.
- 대량 리포트와 배치에는 별도 실행 시간대·읽기 복제본·작업 큐를 검토한다.
- RTO가 업무별로 크게 다르면 이후 `misto` 또는 `mistw`를 별도 클러스터로 분리할 수 있게 마이그레이션 경계를 유지한다.

### 4.2 권한 경계가 약해지기 쉬움 — 높음

애플리케이션 계정 하나에 5개 스키마의 소유권을 주면 스키마 구분은 보안 경계가 아니다. SQL injection이나 애플리케이션 계정 탈취 시 전 업무 데이터가 한 번에 노출·변경될 수 있다.

최소 권한 원칙:

- 스키마별 소유자 역할을 별도로 둔다. 런타임 계정은 소유자가 아니며 `CREATE` 권한을 갖지 않는다.
- 런타임 역할에는 필요한 스키마의 `USAGE`, 필요한 테이블의 `SELECT/INSERT/UPDATE/DELETE`, 필요한 함수의 `EXECUTE`만 부여한다.
- `public` 스키마의 `CREATE` 권한을 제거하고, `search_path`에 신뢰하지 않는 스키마를 앞에 두지 않는다.
- `ALTER DEFAULT PRIVILEGES`로 이후 생성 객체의 권한 누수를 막는다.
- 운영 배치·마이그레이션 역할과 API 런타임 역할을 분리한다.
- 감사 로그에는 역할, 스키마, 함수/테이블, 요청 식별자를 남긴다.

### 4.3 `search_path`와 이름 충돌 — 높음

SQL Server의 `MISCM.dbo.X`는 PostgreSQL에서 `miscm.x`로 명시적으로 바꿔야 한다. PostgreSQL에서 `dbo`를 그대로 만들거나 `search_path`에 5개 스키마를 모두 넣으면 다음 문제가 생긴다.

- 같은 이름의 테이블·함수가 여러 스키마에 있을 때 호출 대상이 순서에 따라 달라짐
- 세션 풀에서 이전 요청의 `search_path`가 다음 요청에 남음
- 마이그레이션·리포트 SQL이 우연히 다른 스키마 객체를 사용함
- 신뢰하지 않는 스키마의 객체가 앞에 오면 함수/객체 가로채기 위험 발생

권장 규칙은 **업무 SQL에서 스키마를 항상 명시하고, `search_path`를 호환성 편의 수단으로 사용하지 않는 것**이다. 코드 생성기·ORM 모델·리포트 SQL·마이그레이션에 다음 매핑표를 공통으로 적용해야 한다.

| SQL Server | PostgreSQL 목표 |
| --- | --- |
| `MISCM.dbo.CMUSER` | `miscm.cmuser` |
| `MISPD.dbo.PDItemMaster` | `mispd.pditemmaster` |
| `MISSA.dbo.SACust` | `missa.sacust` |
| `MISTO.dbo.TOContractMaster` | `misto.tocontractmaster` |
| `MISTW.dbo.tbLogOrdEnr` | `mistw.tblogordenr` |

실제 이름을 대소문자·한글까지 보존하면 PostgreSQL의 큰따옴표 식별자를 모든 SQL에 강제해야 한다. 단계적 전환에서는 물리 테이블명을 소문자 canonical name으로 정하고, 필요한 외부 호환 이름만 뷰로 제공하는 편이 운영상 안전하다. 이 결정은 606개 테이블과 애플리케이션 모델 전체에 영향을 주므로 초기에 고정해야 한다.

### 4.4 교차 스키마 결합이 숨은 결합으로 남음 — 중간~높음

한 데이터베이스가 되면 어느 스키마든 다른 스키마를 쉽게 수정할 수 있다. 이것은 분산 트랜잭션 문제를 줄이는 장점인 동시에, `mispd` 변경이 `mistw` 주문·재고 규칙을 조용히 깨뜨릴 수 있다는 뜻이다.

대응:

- 다른 스키마의 테이블을 직접 쓰기보다 공개 함수 또는 명시적 서비스 계약을 둔다.
- 읽기 전용 공개 뷰와 쓰기 함수의 소유자/권한을 구분한다.
- 스키마 간 의존성 그래프를 CI에서 검사하고 순환 의존성을 금지한다.
- 공통 키·상태 코드·단위·금액 규칙은 별도 계약 문서와 차등 테스트로 관리한다.
- “같은 데이터베이스니까 자유롭게 조인/업데이트”를 개발 규칙으로 허용하지 않는다.

### 4.5 백업·복원 단위가 커짐 — 높음

단일 데이터베이스의 논리 백업은 편하지만 복원 대상은 커진다. 한 스키마만 장애가 나도 전체 DB 복원 또는 별도 임시 DB에 복원 후 필요한 객체·데이터를 대사해야 할 수 있다. 스키마 단위 dump는 가능하지만 다음 의존성을 별도로 검증해야 한다.

- 다른 스키마를 참조하는 뷰·함수·SP 대체 함수
- sequence 현재값과 identity 대체 규칙
- 권한·기본 권한·소유자
- 대용량 `bytea` 이미지와 인덱스
- 데이터 이관 중 생성된 변경분과 CDC/재적용 순서

운영 게이트:

1. 전체 DB 백업으로 깨끗한 PostgreSQL 인스턴스 복원
2. `miscm`, `mispd`, `missa`, `misto`, `mistw` 각각의 객체·행 수·합계·sequence 검증
3. 주문→패킹→출고, 구매→입고→재고, 생산·작업보고, AS 핵심 시나리오 재실행
4. 한 스키마만 복원하는 장애 시나리오와 전체 복원 시나리오를 각각 기록

백업 파일 생성 성공만으로 복구 가능하다고 표시하지 않는다.

### 4.6 성능 격리가 되지 않음 — 높음

현재 스냅샷에는 행 분포·호출 빈도·실행계획이 없으므로 단일 DB의 성능 적합성을 아직 확정할 수 없다. 특히 5개 스키마를 같은 PostgreSQL 클러스터에 넣으면 다음이 공유된다.

- connection pool과 backend process
- shared buffers와 OS page cache
- WAL·checkpoint·autovacuum 작업량
- 디스크 I/O와 임시 파일 공간
- lock manager와 장기 트랜잭션 영향

전환 전에 SQL Server Query Store 또는 동등한 관측으로 상위 호출 SP, p95/p99 시간, 논리 읽기, 쓰기량, 동시 실행 수를 수집해야 한다. 리포트·엑셀 다운로드·대량 재계산 SP는 업무 트랜잭션과 같은 풀을 사용하지 않도록 분리하는 것이 좋다.

### 4.7 PostgreSQL 문법·의미 리스크는 그대로 남음 — 매우 높음

5개 스키마는 SQL Server 전용 기능을 해결하지 않는다. 실제 SP 원문에서 다음 패턴을 확인했다.

| SQL Server 의존성 | 확인 수 | PostgreSQL 전환 시 조치 |
| --- | ---: | --- |
| 내부 `BEGIN TRAN` | 208개 SP | 트랜잭션 소유자를 앱 또는 함수 중 하나로 통일 |
| `GOTO` 오류 흐름 | 698개 SP | 예외 블록과 명시적 오류 코드로 재작성 |
| 임시 테이블 | 84개 SP | `TEMP TABLE`, CTE, 배열/JSONB 중 수명과 인덱스를 비교 |
| 테이블 변수 | 49개 SP | 임시 테이블·복합 타입·JSONB로 재설계 |
| SQL Server 날짜/변환 함수 | 704/583개 SP | `now()`, `date_trunc`, `to_timestamp`, 명시적 cast 등으로 차등 검증 |
| `ISNULL` | 959개 SP | `coalesce`로 변환하되 타입 추론과 빈 문자열 의미 검증 |
| `MERGE` | 1개 SP | `INSERT ... ON CONFLICT` 또는 PostgreSQL `MERGE`의 동시성 차이 검증 |
| 잠금 힌트 | 6개 SP | `FOR UPDATE`, `FOR NO KEY UPDATE`, advisory lock으로 의도 재현 |
| sequence 사용 | 190개 SP | sequence/identity 생성 시점과 gap 허용 정책 검증 |
| 동적 SQL | 13개 SP 원문 실행 | 허용 객체 목록과 파라미터 바인딩을 포함해 수동 재작성 |
| 다중 결과셋 호출 | 백엔드 49개 파일 | `RETURNS TABLE`, JSONB 또는 별도 API 계약으로 정규화 |

현재 백엔드 실행기는 SQL Server `EXEC`, pyodbc raw cursor, `nextset()`, `ERRYN/ERRMSG`, SP 내부 commit/rollback을 전제로 한다. PostgreSQL 드라이버로 교체할 때 연결 실행기와 모든 호출부를 함께 바꿔야 한다.

### 4.8 타입·문자열 비교·시간 의미가 달라짐 — 높음

실제 스키마에 `money` 690개, `datetime` 982개, `image` 35개, SQL Server `timestamp/rowversion` 1개가 있다. 문자 컬럼 상당수는 `Korean_Wansung_CI_AS`를 사용한다.

- `money`는 PostgreSQL `money`보다 `numeric(p,s)`를 권장하되 반올림·overflow를 비교한다.
- SQL Server `datetime`과 PostgreSQL `timestamp [with]out time zone`의 시간대·정밀도를 결정한다.
- `image`는 `bytea`로 옮기고 대용량 조회·API 직렬화·백업 크기를 시험한다.
- SQL Server `timestamp`는 날짜 타입이 아니다. 낙관적 잠금 버전 값으로 별도 매핑한다.
- SQL Server collation과 PostgreSQL의 database/ICU/column collation을 결정하고 한글·대소문자·LIKE·정렬 차이를 테스트한다.
- SQL Server의 빈 문자열, trailing space, implicit conversion, `NULL` 비교를 API 응답까지 차등 비교한다.

### 4.9 인증 암호화 함수가 비공개임 — P0 차단

`MISCM.dbo.fn_Ecp`와 `fn_Dcp`는 라이브 카탈로그에 존재하지만 정의가 비공개다. 로그인 SP와 사용자 저장 SP, 백엔드 `hash_helper.py`가 이 함수를 직접 사용한다.

- 함수 카탈로그: `anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z/MISCM/catalog/modules.json`
- 로그인 원문: `.../MISCM/modules/sql_stored_procedure/dbo/1967346073_str_EZM_LOGIN_S_20251212.sql`
- 백엔드 호출: `be_anasa/src/utils/hash_helper.py`

알고리즘과 키를 확보하지 않은 채 PostgreSQL 함수를 추정해 만들면 기존 계정이 로그인되지 않거나 보안 수준이 낮아질 수 있다. PostgreSQL 전환 전에 다음 중 하나를 승인해야 한다.

1. 기존 인증을 한시적으로 유지하는 SQL Server 인증 브리지
2. 로그인 성공 시 bcrypt 등 검증된 방식으로 재해시하는 점진 전환
3. 전체 사용자 비밀번호 재설정과 기존 암호화 컬럼 폐기

이 결정이 없으면 DB 전환 리허설은 가능해도 운영 전환은 `NO-GO`다.

## 5. 권장 목표 구조

초기 목표는 다음과 같이 단순하게 유지한다.

```text
PostgreSQL cluster
└── anasa (database)
    ├── miscm  # 공통·권한·기준정보
    ├── mispd  # 생산·품목·계획
    ├── missa  # 영업·거래처·AS 일부
    ├── misto  # 공구·구매·입고
    └── mistw  # 물류·주문·출고·집계
```

권장 원칙:

- `public`은 애플리케이션 객체 저장소로 사용하지 않고 `CREATE` 권한을 제거한다.
- 5개 스키마의 이름과 책임을 먼저 고정하고, SQL Server `dbo`를 별도 공용 스키마로 복제하지 않는다.
- 신규 ANASA 전용 객체는 해당 업무 스키마에 둔다. 교차 업무 공용 계약은 무분별한 여섯 번째 스키마를 만들기보다 공개 뷰/함수와 문서화된 소유권으로 시작한다.
- 모든 교차 스키마 참조는 `schema.object`를 사용한다.
- `search_path`는 세션 풀에서 명시적으로 초기화하고, 보안상 신뢰된 시스템 스키마만 기본값으로 둔다.
- 함수와 뷰는 `SECURITY DEFINER` 사용 시 소유자·`search_path`를 고정하고, 동적 SQL은 허용 목록과 바인딩을 강제한다.
- 마이그레이션은 스키마별로 실행 가능하되, 교차 스키마 의존성 순서를 manifest로 관리한다.

## 6. 다른 배치안과 비교

| 안 | 장점 | 문제 | 판정 |
| --- | --- | --- | --- |
| DB 1개·스키마 5개 | 교차 조인/트랜잭션 단순, 연결·운영 단순 | 장애·자원·복원 범위가 전체, 권한 설계 필요 | **1차 전환 권장** |
| DB 1개·스키마 1개 | SQL 단순 | 이름 충돌·소유권·모듈 경계 악화, 현재 결합을 숨김 | 비권장 |
| 클러스터 1개·DB 5개 | 논리적 DB 경계 | 교차 DB 조인/트랜잭션에 FDW·분산 처리 필요 | 현재 SP에는 부담 큼 |
| 클러스터/인스턴스 5개 | 장애·성능·복원 격리 | 비용, 운영, 데이터 동기화와 분산 트랜잭션 부담 | RTO 차이가 큰 영역에만 후속 검토 |

단일 DB 5개 스키마를 선택해도 이후 `mistw` 물류 또는 `misto` 공구 영역을 별도 클러스터로 뺄 수 있도록 스키마별 백업·권한·의존성·마이그레이션 경계를 유지해야 한다.

## 7. 전환 순서와 중단 조건

### 0단계 — 카탈로그와 사용량 확정

1. 라이브 SQL Server를 다시 추출해 SP·함수·뷰·트리거 목록과 SHA를 고정한다.
2. Query Store/Extended Events에서 실제 호출 SP, 호출량, p95/p99, 실패율, 동시성, 배치 호출을 수집한다.
3. 956개 교차 DB SP를 `조회`, `단일 스키마 쓰기`, `교차 스키마 쓰기`, `동적 SQL`, `인증/보안`으로 분류한다.
4. 라이브에만 존재하는 SP 3개와 정의 불일치 SP 15개의 운영 사용 여부를 확인한다.

### 1단계 — 구조와 권한

1. 빈 PostgreSQL 데이터베이스와 5개 스키마를 만든다.
2. 테이블·컬럼·PK·인덱스·sequence·기본값을 먼저 옮기고, 실제 데이터 이관 전 행 수·합계 대사 도구를 만든다.
3. 스키마 소유자, 마이그레이션 역할, 런타임 역할, 읽기 전용 리포트 역할을 분리한다.
4. `public` 권한, `search_path`, SSL, 비밀정보와 연결 풀 초기화 정책을 시험한다.

### 2단계 — 읽기 파일럿

FE 연결 SP 407개 중 교차 스키마·쓰기·동적 SQL·인증 의존성이 없는 단순 조회부터 이관한다. SQL Server와 PostgreSQL을 shadow 실행하고 다음을 비교한다.

- 행 수와 키 집합
- 숫자 합계·반올림
- 날짜·시간대·문자열 정렬과 LIKE 결과
- NULL·빈 결과셋·컬럼명·타입
- p95/p99와 동시 실행 시 lock/wait

### 3단계 — 쓰기·업무 트랜잭션

쓰기 SP 170개는 화면별로 하나씩 전환한다. 결과셋만 비교하지 말고 변경된 행, 생성된 sequence 값, 이력, 오류 시 rollback, 재시도와 동시 실행을 SQL Server와 대조한다. PostgreSQL 트랜잭션 경계는 앱 또는 DB 함수 한 곳이 소유하도록 정한다.

### 4단계 — 인증과 절체

`fn_Ecp/fn_Dcp` 정책, 복원 시험, 핵심 업무 대사, 모니터링, 롤백용 SQL Server 원본 보존이 모두 승인된 뒤 짧은 쓰기 동결을 거쳐 절체한다. 절체 후 SQL Server와 PostgreSQL을 무기한 dual-write하지 않는다. 분기된 두 원본을 장기간 운영하면 재고·주문·정산 대사가 더 어려워진다.

## 8. 승인 체크리스트

다음 항목이 확인되기 전에는 “PostgreSQL 5개 스키마 전환 완료”로 표시하지 않는다.

- [ ] 업무별 RTO/RPO와 허용 중단시간 승인
- [ ] `fn_Ecp/fn_Dcp` 인증 전환 방식과 보안 검토 완료
- [ ] 5개 스키마 이름·테이블명·`dbo` 매핑표 승인
- [ ] 956개 교차 DB SP의 실제 사용량과 전환 분류 완료
- [ ] 93개 동적 SQL 표식과 비공개 함수의 처리 방안 확정
- [ ] T-SQL 문법·타입·collation·시간·sequence 차등 테스트 완료
- [ ] 스키마별 권한과 `search_path` 보안 테스트 완료
- [ ] 전체 DB 복원 및 스키마 단위 복원 시험 완료
- [ ] 주문·패킹·출고, 구매·입고·재고, 생산·작업보고, AS 핵심 시나리오 대사 완료
- [ ] 성능 상위 쿼리의 p95/p99와 lock/wait 기준 승인
- [ ] 장애 시 기존 SQL Server 원본으로 되돌리는 절체·롤백 리허설 완료

## 9. 최종 판단

**단기·중기 목표로는 `PostgreSQL 1 DB + 5개 스키마`를 채택할 수 있다.** ANASA의 현재 교차 DB 결합을 같은 트랜잭션과 명시적 스키마 참조로 옮기기 쉬운 구조이기 때문이다.

그러나 다음 표현은 사용하지 않는다.

- “스키마가 5개라서 업무가 서로 격리된다”
- “SQL Server DB 5개를 PostgreSQL 스키마 5개로 바꾸면 SP가 자동 호환된다”
- “단일 DB라 백업·복원이 끝났다”
- “기능 변경이 적으므로 성능·장애 리스크가 작다”

운영 승인 문구는 다음이 적절하다.

> PostgreSQL 단일 데이터베이스·5개 스키마는 ANASA의 교차 DB 결합을 줄이는 1차 목표 구조로 채택한다. 단일 장애·자원·복원 범위와 SQL Server SP 의미 차이는 별도 검증 대상으로 남기며, 인증 전환·복원 훈련·핵심 업무 차등 대사를 통과한 경우에만 운영 원본으로 절체한다.
