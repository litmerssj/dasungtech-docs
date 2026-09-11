# 2026-09-07 운영 SQL Server snapshot ↔ BE 대조 결과

## 결론

2026-09-07 분석 기준으로 확정한 운영 원문 snapshot과 현재 작업공간의 `be_anasa`를 대조했다.

판정은 다음과 같다.

| 영역 | 결과 | 의미 |
| --- | --- | --- |
| 저장 프로시저 목록 | 부분 일치 | snapshot 1,815개, BE manifest 1,814개 |
| 저장 프로시저 정의 | 1,799개 일치 / 15개 불일치 | 이름만 같다고 동일 구현으로 보지 않음 |
| 운영 snapshot에만 존재 | 1개 | `MISTW.proc_LOGB005_S_03_V2` |
| BE에만 존재 | 0개 | 현재 BE manifest에 추가 고유 SP 없음 |
| 테이블·컬럼 전체 이관 | 확인 불가 | BE에는 원본 606개 테이블 전체를 적재하는 manifest/runner가 없음 |
| 원본 행 데이터 보존 | 미검증 | snapshot에 업무 행 데이터가 포함되지 않아 row 대사 불가 |

따라서 현재 상태는 **SP/스키마 기준선은 부분 일치하지만, 원본 데이터가 모두 BE 테이블에 보존됐다고 승인할 수 없는 상태**다. 실제 데이터가 삭제됐다고 확정한 것도 아니다.

## 기준과 재현 정보

- 분석 기준일: `2026-09-07 (Asia/Seoul)`
- 운영 원문: `anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z`
  - 분석 문서상 9/7 기준 원본으로 사용했다.
  - 파일 자체의 캡처 시각은 `2026-08-24T06:46:41Z`이다.
  - snapshot manifest SHA-256: `771f2175852f17ada99c3c12bbb39bbd2363b5b9f9e1d08dc8b107b4a3cca2eb`
- 비교한 BE: 현재 작업공간 `be_anasa` `HEAD=f9c5fa7185a6c82b1a53fb6b522d68acdbb33aeb`
  - procedure manifest SHA-256: `57d486a64d66da8d15f600e6475ea1642f68adfd563f032897d8bc4bea00b94f`
- 비교 방식: `anasa-procedure-reverse-engineering/tools/materialize_live_sp_source.py`의 정의 정규화 규칙을 사용했다. snapshot 래퍼(`SET ANSI_NULLS`, `SET QUOTED_IDENTIFIER`, `GO`)와 선언부 차이를 제거하고 공백·대소문자 차이를 무시했다. 저장 프로시저는 실행하지 않았다.

9/7 분석 문서에 기록된 이전 BE 기준(`develop`의 당시 커밋)은 `1,797개 일치 / 15개 불일치 / 운영 전용 3개`였다. 현재 checkout의 manifest를 같은 snapshot에 다시 대조하면 BE가 갱신되어 `1,799개 일치 / 15개 불일치 / 운영 전용 1개`로 계산된다.

## 저장 프로시저 대조 상세

### 정의 불일치 15개

| DB | 프로시저 |
| --- | --- |
| `MISPD` | `str_PDOutsourcingOut_D` |
| `MISPD` | `str_PDPlanProcess_S` |
| `MISPD` | `str_PDPLNA00100_S` |
| `MISPD` | `str_PDPLNB00600_S` |
| `MISTW` | `proc_LOGB004_I_03` |
| `MISTW` | `proc_LOGC001_U_01` |
| `MISTW` | `proc_LOGC007_S_GP_OUTDTL` |
| `MISTW` | `proc_LOGE002_S_01` |
| `MISTW` | `proc_LOGE002_S_02` |
| `MISTW` | `proc_LOGE002_S_03` |
| `MISTW` | `proc_LOGE002_S_04` |
| `MISTW` | `proc_LOGG001_S_02` |
| `MISTW` | `proc_LOGG022_S_06` |
| `MISTW` | `proc_LOGG025_S_01` |
| `MISTW` | `proc_PURC006_S` |

이 15개는 프로젝트의 엄격한 원문 정규화(주석 포함) 기준에서 불일치한 목록이다. SQL 주석까지 제거한 추가 비교에서는 10개가 실행 본문 차이이고, 5개는 snapshot/BE의 설명 주석 차이로 분류된다. 실행 본문 차이는 운영 원문을 우선해 조회 조건·반환 컬럼·삭제 결과셋·페이징·금액/수량 계산 영향을 별도 확인해야 한다.

실행 본문 차이 10개:

- `MISPD.str_PDOutsourcingOut_D`
- `MISPD.str_PDPlanProcess_S`
- `MISPD.str_PDPLNA00100_S`
- `MISPD.str_PDPLNB00600_S`
- `MISTW.proc_LOGB004_I_03`
- `MISTW.proc_LOGE002_S_01`
- `MISTW.proc_LOGG001_S_02`
- `MISTW.proc_LOGG022_S_06`
- `MISTW.proc_LOGG025_S_01`
- `MISTW.proc_PURC006_S`

설명 주석 차이로만 남은 5개:

- `MISTW.proc_LOGC001_U_01`
- `MISTW.proc_LOGC007_S_GP_OUTDTL`
- `MISTW.proc_LOGE002_S_02`
- `MISTW.proc_LOGE002_S_03`
- `MISTW.proc_LOGE002_S_04`

### 운영 snapshot에만 있는 SP

- `MISTW.proc_LOGB005_S_03_V2`

현재 BE manifest에는 대응 파일이 없다. 운영에 실제 caller가 있는지와 현재 화면/API 대체 구현이 동등한지는 별도 판정 대상이다.

## 테이블·컬럼 기준선과 BE 표현 범위

snapshot 카탈로그의 업무 DB별 기준은 다음과 같다.

| DB | 테이블 | 컬럼(카탈로그) | 저장 프로시저 |
| --- | ---: | ---: | ---: |
| `MISCM` | 111 | 1,787 | 201 |
| `MISPD` | 109 | 2,201 | 266 |
| `MISSA` | 43 | 993 | 126 |
| `MISTO` | 192 | 3,179 | 326 |
| `MISTW` | 151 | 3,048 | 896 |
| **합계** | **606** | **11,208** | **1,815** |

현재 BE의 Python ORM 선언을 정적으로 세면 `__tablename__` 고유 이름은 99개뿐이다(이 중 snapshot의 테이블 이름과 일치하는 이름 98개). 이는 BE가 많은 업무를 레거시 SP/raw SQL로 직접 읽는 구조이므로 곧바로 “502개 테이블이 삭제됐다”는 뜻은 아니다. 다만 **원본 606개 테이블의 행을 BE 대상 테이블로 모두 적재·보존하는 전체 이관 구조가 현재 BE에 표현돼 있지 않다**는 뜻이다.

또한 BE Alembic은 전체 레거시 스키마를 ORM 자동생성하는 구조가 아니다. `target_metadata=None`이고 autogenerate가 꺼져 있으며, 모델에 없는 레거시 테이블을 자동 삭제하지 않도록 수동 revision만 사용한다. 외부 SQL runner도 연결·데이터 복사기가 아니라 검토된 SQL 파일의 배치·checksum 검증기다.

## 데이터 보존 판정이 아직 불가능한 이유

1. snapshot README가 테이블/업무 행 데이터를 캡처하지 않았다고 명시한다.
2. 현재 BE checkout에 source→target 전체 테이블 매핑과 적재 manifest가 없다.
3. 테이블별 행 수, PK/logical key 집합, row hash, LOB 길이/hash, 금액·수량 합계, 고아 행, identity/sequence, 재실행 결과를 원본과 대상에서 함께 만든 결과물이 없다.
4. legacy `DATAMIG` SP 원문에는 전체 또는 기간 범위 `TRUNCATE`/`DELETE` 후 `INSERT` 흐름이 있다. 예를 들어 `proc_DataMig_A_31`, `_35`, `_44`와 `proc_DataMig2_Daily_43~49`가 확인된다. 이 SP들이 현재 BE 절체에서 실행됐다는 증거는 없지만, 실행한다면 백업과 대사 없이 기존 행 보존을 보장할 수 없다.

## 별도 스키마 주의사항

9/7 운영 접속 기록에는 운영 SQL Server 2014의 `MISPD.dbo.PDItemMaster`에 `MaterialGb`가 없다고 남아 있고, 기존 snapshot ERD에는 `MaterialGb`가 있다. BE의 `pd_material_kind` 모델은 `MISPD.dbo.MaterialKind.MaterialGb`를 선언할 뿐 `PDItemMaster.MaterialGb`의 원본 값 보존을 증명하지 않는다. 이 차이는 이관 전에 기준 DB와 대상 컬럼 매핑을 확정해야 한다.

## 최종 상태와 승인 조건

- SP/원문 기준: `PARTIAL_MATCH`
- 테이블/컬럼 전체 이관: `NOT_ESTABLISHED`
- 원본 행 데이터 보존: `UNVERIFIED`
- 운영 절체 승인: `NO-GO`

운영 원본을 보존했다고 판정하려면 Windows 업무용 유선 PC에서 해당 snapshot 기준시점의 원본과 BE 대상 DB를 읽기 전용으로 대조하고, 최소한 다음 결과를 저장해야 한다.

- 테이블별 행 수·NULL 수
- PK 또는 승인된 logical key 집합·중복 수
- key별 row hash와 LOB 길이/hash
- 금액·수량 합계와 날짜 범위
- 교차 DB 참조 누락·고아 행
- identity/sequence 원본·대상 현재값과 신규 INSERT 충돌 시험
- 이관 재실행 시 중복·누락 여부

이 문서는 코드·메타데이터 대조 결과이며 고객 업무 행을 포함하지 않는다. 이 검토에서 DB 변경 0건, 저장 프로시저 실행 0건이다.
