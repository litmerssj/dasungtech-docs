# SQL Server 2022 에디션 비교

| 항목 | 내용 |
| --- | --- |
| 작성일 | 2026-08-28 |
| 제품 기준 | Microsoft SQL Server 2022 (16.x) |
| 비교 목적 | ANASA 운영 DB 에디션 선정 |
| 가격 기준 | Microsoft 미국 공개 예상 소매가, 할인·VAT 제외 |

## 1. 에디션별 용도와 라이선스

| 에디션 | 주요 용도 | 실제 운영 허용 | 비용·구매 방식 | ANASA 적용 판단 |
| --- | --- | :---: | --- | --- |
| Enterprise | 미션 크리티컬 DB, 대규모 성능, 고급 이중화 | 가능 | 유료, 코어 라이선스 중심 | Enterprise 전용 기능이 필요할 때만 검토 |
| Standard | 일반 기업 업무·ERP 운영 | 가능 | 유료, 코어 또는 Server+CAL | **현재 운영 권장안** |
| Web | 웹 호스팅 사업자가 제공하는 웹 서비스 | 제한적 | 호스팅·SPLA 채널 중심, 일반 직접 구매용 아님 | 일반 ERP 운영 목적에 부적합 |
| Developer | Enterprise 기능 기반 개발·테스트 | **불가** | 무료 | 현재 스테이징에만 사용 가능 |
| Evaluation | Enterprise 기능 평가 | **불가** | 무료, 180일 제한 | 단기 평가에만 사용 가능 |
| Express | 소규모·경량 운영 DB | 가능 | 무료 | DB당 10GB 제한으로 ANASA 사용 불가 |

Developer와 Evaluation은 기능상 Enterprise 수준이지만 운영 권한을 제공하지 않는다. Web Edition은 SQL Server 2022에서 제공되지만 웹 호스팅 사업자용 채널에 한정되는 에디션이다.

Express에는 동일한 10GB·CPU·메모리 한도를 적용받으면서 Full-Text Search 등 구성요소가 추가된 `Express with Advanced Services` 설치 옵션이 있다. `LocalDB`는 Express의 경량 개발용 실행 방식으로 일반 서버 운영 에디션이 아니다.

## 2. 에디션별 성능·용량 한도

다음 값은 SQL Server 2022 에디션 자체의 최대 한도다. 실제 처리 성능은 EC2의 CPU, 메모리, EBS IOPS·처리량, 쿼리 구조와 동시 사용자 수에 의해 결정된다.

| 에디션 | DB 엔진 최대 연산 용량 | 버퍼 풀 최대 메모리 | Columnstore 캐시 | 메모리 최적화 데이터/DB | 최대 관계형 DB 크기 |
| --- | ---: | ---: | ---: | ---: | ---: |
| Enterprise | 운영체제 최대 | 운영체제 최대 | 제한 없음 | 제한 없음 | 524PB |
| Standard | 4소켓 또는 24코어 중 작은 값 | 128GB | 32GB | 32GB | 524PB |
| Web | 4소켓 또는 16코어 중 작은 값 | 64GB | 16GB | 16GB | 524PB |
| Developer | Enterprise와 동일 | Enterprise와 동일 | Enterprise와 동일 | Enterprise와 동일 | 524PB |
| Evaluation | Enterprise와 동일 | Enterprise와 동일 | Enterprise와 동일 | Enterprise와 동일 | 524PB |
| Express | 1소켓 또는 4코어 중 작은 값 | 1,410MB | 352MB | 352MB | **DB당 10GB** |

SQL Server 2022 Standard의 코어 한도는 24코어다. Microsoft의 최신 공통 문서에 더 높은 값이 보이더라도 해당 문서의 각주에 따라 SQL Server 2022 이하에는 24코어 한도를 적용한다.

## 3. 운영·고가용성 기능 비교

| 기능 | Enterprise | Standard | Web | Developer | Evaluation | Express |
| --- | :---: | :---: | :---: | :---: | :---: | :---: |
| SQL Server Agent | 지원 | 지원 | 지원 | 지원 | 지원 | 미지원 |
| 백업 압축 | 지원 | 지원 | 미지원 | 지원 | 지원 | 미지원 |
| Always On 전체 가용성 그룹 | 지원 | 미지원 | 미지원 | 지원 | 지원 | 미지원 |
| Basic Availability Group | 전체 AG 사용 | 지원 | 미지원 | 전체 AG 사용 | 전체 AG 사용 | 미지원 |
| 온라인 인덱스 생성·재구성 | 지원 | 미지원 | 미지원 | 지원 | 지원 | 미지원 |
| Resource Governor | 지원 | 미지원 | 미지원 | 지원 | 지원 | 미지원 |
| 데이터 압축·파티셔닝 | 지원 | 지원 | 지원 | 지원 | 지원 | 지원 |
| 자동 튜닝 | 지원 | 미지원 | 미지원 | 지원 | 지원 | 미지원 |

Standard는 전체 Always On 가용성 그룹 대신 2개 복제본·단일 DB 범위의 Basic Availability Group을 지원한다. ANASA에서 확인된 현재 구성은 HADR과 클러스터를 사용하지 않는다.

## 4. 공개 가격 비교

| 에디션·방식 | Microsoft 공개 단가 | 4코어 기준 |
| --- | ---: | ---: |
| Standard 영구 코어 라이선스 | $3,945 / 2코어 | **$7,890 일시 구매** |
| Standard 연간 구독 | $1,418 / 2코어·년 | **$2,836/년** |
| Standard Azure Arc 종량제 | $73 / 코어·월 | **$292/월** |
| Standard Server+CAL | 서버 $989 + CAL $230/사용자·장치 | 사용자·장치 수에 따라 변동 |
| Enterprise 영구 코어 라이선스 | $15,123 / 2코어 | **$30,246 일시 구매** |
| Enterprise 연간 구독 | $5,434 / 2코어·년 | **$10,868/년** |
| Enterprise Azure Arc 종량제 | $274 / 코어·월 | **$1,096/월** |
| Web | 공개 일반 소매가 없음 | 호스팅·SPLA 계약 확인 필요 |
| Developer | 무료 | 운영 사용 불가 |
| Evaluation | 무료 | 180일 평가용 |
| Express | 무료 | DB당 10GB 제한 |

현재 견적의 4코어는 성능 권장이 아니라 코어 라이선스의 VM당 최소 구매 기준을 반영한 것이다. 실제 구매 가격은 국가, CSP 할인, 계약 채널, Software Assurance 여부에 따라 달라진다.

## 5. ANASA 선정 결론

| 검토 항목 | 확인 결과 | 판단 |
| --- | --- | --- |
| 현재 DB 데이터 | 263.41GB, 로그 포함 278.72GB | Express 불가 |
| 10GB 초과 DB | MISTO, MISPD, MISTW | Express 불가 확정 |
| Enterprise 영구 기능 | 접근 가능한 5개 DB 검사 결과 0건 | Standard 이전을 막는 기능 미발견 |
| HADR·클러스터 | 사용하지 않음 | Enterprise 필요 근거 없음 |
| 견적 DB 서버 | 4 vCPU·32GiB | Standard 한도 내 충분한 여유 |
| 향후 DB 5배 | 약 1.39TB | DB 크기만으로 Enterprise 필요 없음 |

현재 확인 범위에서는 Standard가 비용과 기능의 균형이 가장 적절하다. `BIZCLIENT`는 접근 권한 확보 후 같은 기능 검사를 수행해야 한다.

## 6. 공식 참고 자료

- [Microsoft Learn: SQL Server 2022 에디션 및 지원 기능](https://learn.microsoft.com/en-us/sql/sql-server/editions-and-components-of-sql-server-2022?view=sql-server-ver16)
- [Microsoft Learn: SQL Server 에디션별 연산 용량 한도](https://learn.microsoft.com/en-us/sql/sql-server/compute-capacity-limits-by-edition-of-sql-server?view=sql-server-ver16)
- [Microsoft: SQL Server 2022 가격 및 라이선스](https://www.microsoft.com/en-us/sql-server/sql-server-2022-pricing)
- [Microsoft: SQL Server 라이선스 자료](https://www.microsoft.com/licensing/docs/view/SQL-Server)
