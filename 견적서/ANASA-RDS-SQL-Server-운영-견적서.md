# ANASA RDS for SQL Server 운영 견적서

| 항목 | 내용 |
| --- | --- |
| 작성일 | 2026-08-28 |
| 가격 기준 | AWS 공식 가격표 2026-08-28 게시본, 2026-08-01 유효 단가 |
| 기준 리전 | AWS 서울 `ap-northeast-2` |
| 환율 | USD 1달러 = 1,400원 |
| 금액 표기 | VAT 10% 포함 |
| 사용시간 | 월 744시간, 24시간 상시 운영 |
| 앱 서버 | EC2 `t3.large`, 2 vCPU·8GiB |
| DB 서비스 | RDS for SQL Server 2022 Standard, `db.r7i.xlarge`, 4 vCPU·32GiB |
| DB 배포 | Single-AZ, 온디맨드, License Included |
| DB 저장공간 | RDS gp3 1,100GB, 3,000 IOPS·125MiB/s 기본 성능 |

## 1. 최종 견적

> **앱 `t3.large` + RDS for SQL Server 2022 Standard `db.r7i.xlarge` Single-AZ + RDS gp3 1.1TB**

기존 [ANASA 최종 운영 견적서](./ANASA-최종-운영-견적서.md)의 서울 리전, 앱 서버, DB CPU·메모리, DB 저장공간과 SQL Server Standard 조건을 유지하고 DB 운영 방식만 EC2 직접 설치에서 RDS로 변경한 견적이다.

| 구분 | 금액(VAT 포함) |
| --- | ---: |
| 월 운영비 | **$1,292.36 / 약 181만원** |
| 첫해 총비용 | **$15,508.35 / 약 2,171만원** |
| 2년 차 이후 연 운영비 | **$15,508.35 / 약 2,171만원** |

RDS License Included 요금에 Windows와 SQL Server Standard 사용권이 포함되므로 SQL Server 영구 라이선스를 별도로 구매하지 않는다. 대신 라이선스 비용이 RDS 시간당 요금에 계속 포함되므로 첫해와 2년 차 이후의 반복비용이 같다.

## 2. 개별 비용

| 항목 | 사양·산정 기준 | 월 비용(VAT 포함) | 연 비용(VAT 포함) |
| --- | --- | ---: | ---: |
| 앱 EC2 | `t3.large`, 2 vCPU·8GiB | $85.11 / 약 12만원 | $1,021.36 / 약 143만원 |
| 앱 EBS | gp3 50GB | $5.02 / 약 0.7만원 | $60.19 / 약 8.4만원 |
| 공인 IPv4 | 1개 | $4.09 / 약 0.6만원 | $49.10 / 약 6.9만원 |
| 네트워크 | 현재 사용량 기준 상한 | $5.17 / 약 0.7만원 | $62.09 / 약 8.7만원 |
| RDS 인스턴스 | `db.r7i.xlarge` 인스턴스 부분, $0.600/시간 | $491.04 / 약 68.7만원 | $5,892.48 / 약 825만원 |
| Windows 라이선스 | $0.184/시간 | $150.59 / 약 21.1만원 | $1,807.03 / 약 253만원 |
| SQL Server Standard 라이선스 | $0.480/시간 | $392.83 / 약 55.0만원 | $4,713.98 / 약 660만원 |
| RDS gp3 | 1,100GB × $0.131/GB-월 | $158.51 / 약 22.2만원 | $1,902.12 / 약 266만원 |
| **합계** | License Included 반복비용 | **$1,292.36 / 약 181만원** | **$15,508.35 / 약 2,171만원** |

### RDS 시간당 단가 해석

`db.r7i.xlarge`는 AWS의 비번들 온디맨드 가격 체계를 사용한다. AWS Price List API에 표시되는 `$0.600/시간`은 전체 RDS 요금이 아니라 인스턴스 부분이다.

| 시간당 구성 | 세전 단가 |
| --- | ---: |
| 인스턴스 | $0.600 |
| Windows 라이선스 | $0.184 |
| SQL Server Standard 라이선스 | $0.480 |
| **RDS 시간당 합계** | **$1.264** |

따라서 월 RDS 인스턴스·라이선스 비용은 `$1.264 × 744시간 × 1.1 = $1,034.46`이다. SQL Server 라이선스는 가격표에서 분리 표시되지만 고객이 별도 라이선스를 구매하는 BYOL 구조가 아니라 최종 RDS 청구액에 포함되는 License Included 구조다.

### DB 스토리지와 EBS의 관계

RDS for SQL Server도 내부적으로 Amazon EBS 볼륨을 데이터와 로그 저장에 사용한다. 다만 사용자가 EBS를 직접 만들거나 연결하지 않고 RDS의 할당 스토리지로 주문한다.

- 기존안: DB EBS gp3 1.1TB를 사용자가 관리하고 월 약 15.4만원 부담
- RDS안: RDS gp3 1.1TB를 AWS가 관리하고 월 약 22.2만원 부담
- 본 견적에는 DB용 EBS를 별도 항목으로 중복 추가하지 않음

## 3. 기존 EC2 직접 설치안과 비교

| 구분 | EC2 + 영구 라이선스 | RDS License Included | RDS 증감 |
| --- | ---: | ---: | ---: |
| 첫해 | $14,330.78 / 약 2,006만원 | **$15,508.35 / 약 2,171만원** | **약 165만원 증가** |
| 2년 차 이후 월 | $470.98 / 약 66만원 | **$1,292.36 / 약 181만원** | **약 115만원 증가** |
| 2년 차 이후 연 | $5,651.78 / 약 791만원 | **$15,508.35 / 약 2,171만원** | **약 1,380만원 증가** |
| 3년 누적 | $25,634.34 / 약 3,589만원 | **$46,525.05 / 약 6,514만원** | **약 2,925만원 증가** |

RDS안은 신규 영구 라이선스를 사는 보수적인 EC2 상한 견적과 비교해도 첫해 약 8.2% 높다. 2년 차부터는 EC2안에서 영구 라이선스 비용이 반복되지 않지만 RDS는 라이선스 포함 시간당 요금이 계속 발생하므로 연 반복비용이 약 174% 높다.

따라서 **최소 3년 운영의 비용만 보면 기존 EC2 + 적격 SQL Server 라이선스 방식이 유리**하다. RDS는 비용 절감안이 아니라 다음 운영 이점을 위해 추가비용을 지불하는 선택지다.

- DB 서버 OS·EBS 연결·SQL Server 설치 관리 축소
- 자동 백업, 시점 복구와 패치 관리 기능 이용
- 별도 영구 라이선스 구매 및 AWS 반입권 판단 불필요
- Multi-AZ 관리형 장애조치로 확장 가능

기존 SQL Server 권리를 추가 구매비 없이 EC2에 사용할 수 있다면 EC2안과 RDS안의 비용 차이는 이 표보다 더 커진다.

## 4. DB 성장별 RDS 비용

앱 서버와 `db.r7i.xlarge`는 고정하고 RDS gp3 용량만 변경한 비교다. 모든 금액은 VAT 포함이다.

| DB 증가 | 예상 DB | RDS gp3 | 월 총비용 | 연 총비용 |
| --- | ---: | ---: | ---: | ---: |
| 현재 DB (1배) | 279GB | 500GB | $1,205.90 / 약 169만원 | $14,470.83 / 약 2,026만원 |
| 현재 DB의 2배 | 557GB | 750GB | $1,241.93 / 약 174만원 | $14,903.13 / 약 2,086만원 |
| **현재 DB의 3배** | **836GB** | **1,100GB** | **$1,292.36 / 약 181만원** | **$15,508.35 / 약 2,171만원** |
| 현재 DB의 5배 | 1.39TB | 1,800GB | $1,393.23 / 약 195만원 | $16,718.79 / 약 2,341만원 |

RDS for SQL Server gp3는 용량이 커져도 자동 스트라이핑 임계값이 없으므로 1.1TB에서도 기본 성능은 3,000 IOPS·125MiB/s다. 기존 EC2 gp3 기본 성능과 같은 출발점이며, 운영 부하 측정 결과가 기본 성능을 넘으면 추가 IOPS·처리량 또는 io2 비용을 별도로 산정한다.

## 5. 선택 옵션 — RDS Multi-AZ

동일한 `db.r7i.xlarge`, SQL Server Standard, gp3 1.1TB를 RDS Multi-AZ로 변경한 경우다.

| 항목 | 세전 단가 | 월 비용(VAT 포함) |
| --- | ---: | ---: |
| RDS 인스턴스 부분 | $1.200/시간 | $982.08 |
| Windows 라이선스 | $0.368/시간 | $301.17 |
| SQL Server Standard 라이선스 | $0.480/시간 | $392.83 |
| RDS gp3 1.1TB | $0.262/GB-월 | $317.02 |
| 기존 앱·부대 비용 | 기존과 동일 | $99.39 |
| **Multi-AZ 총비용** |  | **$2,092.49 / 약 293만원** |

| 구분 | 금액(VAT 포함) |
| --- | ---: |
| 월 운영비 | **$2,092.49 / 약 293만원** |
| 연 운영비 | **$25,109.98 / 약 3,515만원** |

이 구성은 DB의 대기 인스턴스와 자동 장애조치만 추가한다. 앱 서버는 여전히 한 대이므로 서비스 전체 HA가 아니다. 기존 견적의 앱 서버 이중화와 ALB를 함께 적용하면 월 약 15~16만원이 더해져 전체 HA 월 비용은 약 **308~309만원**이 된다.

## 6. 프로시저 호환성과 선별 ORM 대체

RDS for SQL Server는 SQL Server 엔진을 그대로 사용하므로 일반적인 조회·등록·수정·삭제 프로시저는 대부분 유지할 수 있다. 다만 RDS는 고객이 운영체제와 DB 서버를 직접 관리하는 환경이 아니므로, OS 파일·로컬 드라이브·서버 권한에 의존하는 프로시저는 그대로 작동한다고 가정하지 않는다.

`be_anasa` 정적 코드 점검 결과는 다음과 같다. 아래 수치는 레거시·미사용 코드가 일부 포함될 수 있으므로 실제 운영 호출량과 구분한다.

| 점검 항목 | 확인 결과 |
| --- | ---: |
| Repository 파일 | 176개 |
| `StoredProcedureExecutor` 사용 파일 | 154개 |
| SP 실행 호출부 | 586개 |
| SP Executor 상속 클래스 | 132개 |
| 프로시저 SQL 파일 | 1,820개(프로시저 manifest 약 1,814개) |
| SQLAlchemy ORM 모델 파일 | 120개 |
| ORM 문장 호출부 | 약 672개 |

현재 연결 계층은 `mssql+pyodbc`와 SQL Server ODBC 드라이버를 사용하고, 5개 업무 DB별 세션을 생성한다. 저장 프로시저 실행기는 `EXEC`, 여러 결과 집합(`nextset()`), `ERRYN`·`ERRMSG` 오류 규약, 중첩 프로시저와 트랜잭션을 직접 처리한다. 따라서 모든 프로시저를 한 번에 ORM으로 재작성하는 것보다 RDS에서 문제가 되는 기능만 선별해 대체하는 방식이 안전하다.

| 현재 기능 | RDS 적용 또는 대체 방식 |
| --- | --- |
| 일반 업무 조회·등록·수정·삭제 SP | RDS SQL Server에서 우선 유지; 필요한 화면부터 ORM 또는 SQLAlchemy Core로 단계적 전환 |
| 여러 결과 집합·중첩 트랜잭션 SP | 기존 SP 유지 후 결과·오류·롤백 계약을 검증하고 필요한 것만 ORM으로 재작성 |
| `Batch_Database_Backup`의 `BACKUP ... TO DISK` | RDS 자동 백업·스냅샷 또는 RDS의 S3 백업 기능으로 교체 |
| `proc_ExportToFile`, `proc_ExportToPdf`의 `sp_OACreate`·로컬 경로 저장 | ORM으로 바이너리 조회 후 FastAPI·작업 서비스에서 파일 생성, S3 저장으로 교체 |
| SQL Agent·주기성 데이터 처리 | RDS 지원 여부를 확인하고 EventBridge Scheduler·ECS 작업·애플리케이션 스케줄러로 대체 검토 |
| `sysadmin`, OS 명령, 서버 설정 변경 | RDS 파라미터 그룹·AWS 콘솔/API·운영 자동화로 대체 |

5개 DB를 RDS SQL Server에서 유지하려면 가능한 한 하나의 RDS 인스턴스에 배치해야 기존 인스턴스 내부 교차 DB 호출을 보존할 수 있다. DB를 여러 RDS 인스턴스로 나누면 연결·트랜잭션·교차 DB 로직을 애플리케이션에서 다시 설계해야 한다.

> ORM으로 일부 프로시저를 대체해도 RDS SQL Server를 사용하는 동안에는 SQL Server 라이선스가 RDS License Included 요금에 포함된다. SQL Server 라이선스 비용 자체를 없애려면 PostgreSQL 등 다른 DB 엔진으로 전환해야 하며, 이는 별도의 스키마·데이터·애플리케이션 전환 프로젝트다.

### 혼합 운영 아키텍처

RDS SQL Server에서는 모든 프로시저를 ORM으로 바꾸지 않아도 된다. RDS에서 지원되는 업무 프로시저는 유지하고, 서버 운영체제나 로컬 파일에 의존하는 기능만 애플리케이션·RDS 기능·S3로 분리한다.

```text
앱 EC2 (FastAPI)
 ├─ 기존 업무 SP 호출 ───────────────┐
 ├─ 선별 ORM/SQLAlchemy 로직 ─────────┤→ RDS SQL Server
 └─ 파일·PDF 처리 / S3 업로드 ───────┘   ├─ MISCM
                                         ├─ MISTW
                                         ├─ MISSA
                                         ├─ MISTO
                                         └─ MISPD
                         RDS 자동 백업·스냅샷
```

| 대체 대상 | 처리 방법 | ORM만으로 해결되는가 |
| --- | --- | --- |
| 일반 조회·등록·수정·삭제 | 기존 SP 유지 또는 화면 단위로 ORM 전환 | 가능하지만 결과·트랜잭션 회귀 필요 |
| 여러 결과 집합·중첩 SP | 기존 SP 우선 유지; 필요한 경우 ORM/SQLAlchemy Core로 재현 | 가능하지만 반환 계약을 다시 고정해야 함 |
| `Batch_Database_Backup`의 `BACKUP ... TO DISK` | RDS 자동 백업·스냅샷·S3 백업 기능 | 아니오. RDS 기능으로 대체 |
| `proc_ExportToFile`, `proc_ExportToPdf`의 `sp_OACreate`·로컬 경로 | ORM으로 바이너리 조회 후 FastAPI/작업 서비스에서 파일 생성, S3 저장 | DB 조회만 ORM, 파일 처리는 애플리케이션 |
| SQL Agent·주기성 처리 | RDS 지원 여부 확인 후 EventBridge Scheduler·ECS 작업·애플리케이션 스케줄러 검토 | 아니오. 스케줄러 운영이 필요 |
| `sysadmin`, OS 명령, 서버 설정 | RDS 파라미터 그룹·AWS 콘솔/API·운영 자동화 | 아니오. RDS 관리 범위로 대체 |

이 전략은 RDS SQL Server의 라이선스 포함 요금을 그대로 사용하면서도, 모든 SP를 한 번에 재작성하는 위험을 줄인다. 반대로 **SQL Server 라이선스 비용을 완전히 없애려면 RDS PostgreSQL 등 다른 엔진으로 별도 이전해야 하며, ORM 일부 전환만으로는 라이선스가 없어지지 않는다.**

## 7. 적용 전 필수 PoC

ANASA는 운영 DB 5개와 다수의 저장 프로시저를 함께 사용하므로 비용 승인만으로 RDS 이전을 확정하지 않는다. 다음 항목을 복제 데이터로 검증한다.

- 5개 DB 사이 호출과 트랜잭션
- SQL Agent 작업, 로그인·권한과 애플리케이션 연결 계정
- 현재 T-SQL과 저장 프로시저 기능
- 네이티브 백업·복원과 약 279GB 데이터의 실제 이전 시간
- RDS에서 제공하지 않는 `sysadmin`, OS·파일시스템 접근에 의존하는 작업 유무
- 유지관리 시간의 재부팅과 애플리케이션 재연결
- Multi-AZ 선택 시 5개 DB 장애조치 일관성과 연결 복구
- gp3 3,000 IOPS·125MiB/s에서 피크 조회·배치 성능

Single-AZ RDS는 관리형 백업을 제공하지만 인스턴스 또는 AZ 장애 중 서비스가 계속된다는 뜻은 아니다. 업무가 요구하는 RTO·RPO가 Single-AZ 복구시간보다 짧으면 Multi-AZ 비용과 장애조치 시험을 별도로 승인한다.

## 8. 포함·제외 범위

### 포함

- 앱 EC2 `t3.large`
- 앱 EBS gp3 50GB
- 공인 IPv4 1개와 현재 수준의 외부 네트워크 전송
- RDS for SQL Server Standard `db.r7i.xlarge` Single-AZ
- Windows와 SQL Server Standard License Included 요금
- RDS gp3 1.1TB

### 제외

- RDS Multi-AZ와 앱 서버 이중화
- BIZCLIENT와 레거시 FAX송부로그 기능
- 데이터 이전, 호환성 수정, DBA 작업과 성능 테스트 인력비
- 무료 허용량을 초과하는 자동 백업, 수동 스냅샷과 장기 보관
- 추가 gp3 IOPS·처리량 또는 io2
- 앱과 RDS가 서로 다른 AZ에 있을 때의 교차 AZ 데이터 전송
- Enhanced Monitoring, CloudWatch 로그 증가분, Secrets Manager와 AWS Support
- Vercel 유료 요금, 관리형 Redis, NAT Gateway
- 예약 인스턴스·Database Savings Plans·CSP·MAP 할인

본 견적은 Single-AZ RDS를 앱 EC2와 같은 AZ의 프라이빗 서브넷 경로로 연결하는 것을 가정한다. 실제 배치, 백업 보존기간, 피크 IOPS와 할인 계약을 확정한 뒤 AWS Pricing Calculator에서 최종 구매 견적을 다시 생성한다.

## 9. 참고 자료

- [ANASA 최종 운영 견적서 — EC2 직접 설치안](./ANASA-최종-운영-견적서.md)
- [ANASA 저장 프로시저 → ORM 전환 타당성·비용 평가](./ANASA-저장-프로시저-ORM-전환-타당성-비용-평가.md)
- [ANASA 기존 SQL Server 라이선스 및 AWS 이전권 확인](./ANASA-기존-SQL-Server-라이선스-및-AWS-이전권-확인.md)
- [AWS RDS for SQL Server 요금](https://aws.amazon.com/rds/sqlserver/pricing/)
- [AWS Amazon RDS 관리 모델](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)
- [AWS Price List API — Amazon RDS 서울 리전](https://pricing.us-east-1.amazonaws.com/offers/v1.0/aws/AmazonRDS/current/ap-northeast-2/index.json)
- [AWS RDS DB 인스턴스 스토리지](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Storage.html)
- [AWS RDS for SQL Server 라이선스](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.Licensing.html)
- [AWS RDS for SQL Server 기능 지원](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SQLServer.html)
- [AWS Pricing Calculator — RDS for SQL Server](https://calculator.aws/#/createCalculator/RDSSQLServer)
