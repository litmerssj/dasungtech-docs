# ANASA 최종 운영 견적서

| 항목 | 내용 |
| --- | --- |
| 작성일 | 2026-08-28 |
| 기준 리전 | AWS 서울 `ap-northeast-2` |
| 환율 | USD 1달러 = 1,400원 |
| 금액 표기 | VAT 10% 포함 |
| 앱 서버 | EC2 `t3.large`, 2 vCPU·8GiB |
| DB 서버 | EC2 `r7i.xlarge`, 4 vCPU·32GiB |
| DB 저장공간 | gp3 1.1TB, 현재 운영 DB 약 279GB의 3배 성장 기준 |
| SQL Server | SQL Server 2022 Standard 4코어 영구 라이선스 |

## 1. 최종 권장안

> **SQL Server Standard 4코어 영구 라이선스 + 앱 `t3.large` + DB 서버 `r7i.xlarge` + DB EBS 1.1TB**

최소 3년 이상 운영하고 앱 서버를 `t3.large`로 전환하는 것을 전제로 한 권장안이다. BIZCLIENT는 레거시 FAX송부로그 기능과 함께 운영 범위에서 제외한다.

| 구분 | 금액 |
| --- | ---: |
| 첫해 총비용 | **$14,331 / 약 2,006만원** |
| 2년 차 이후 월 운영비 | **$471 / 약 66만원** |
| 2년 차 이후 연 운영비 | **$5,652 / 약 791만원** |

첫해 총비용에는 SQL Server 영구 라이선스 일시구매 비용이 포함된다. 2년 차 이후에는 동일 버전을 계속 사용하고 추가 유지계약이 없다는 가정으로 인프라 반복비용만 계산했다.

## 2. 개별 비용

| 항목 | 사양·산정 기준 | 월 비용(VAT 포함) | 연 비용(VAT 포함) |
| --- | --- | ---: | ---: |
| 앱 EC2 | `t3.large`, 2 vCPU·8GiB | **$85.11 / 약 12만원** | **$1,021.36 / 약 143만원** |
| 앱 EBS | gp3 50GB | **$5.02 / 약 0.7만원** | **$60.19 / 약 8.4만원** |
| 공인 IPv4 | 1개 | **$4.09 / 약 0.6만원** | **$49.10 / 약 6.9만원** |
| 네트워크 | 현재 사용량 기준 상한 | **$5.17 / 약 0.7만원** | **$62.09 / 약 8.7만원** |
| DB EC2 | `r7i.xlarge`, 4 vCPU·32GiB | **$261.23 / 약 36.6만원** | **$3,134.80 / 약 438.9만원** |
| DB EBS | gp3 1.1TB | **$110.35 / 약 15.4만원** | **$1,324.22 / 약 185.4만원** |
| **인프라 합계** | 라이선스 제외 반복비용 | **$470.98 / 약 66만원** | **$5,651.78 / 약 791만원** |

### SQL Server 영구 라이선스

| 항목 | 세전 | VAT 포함 |
| --- | ---: | ---: |
| Standard 영구 4코어 | $7,890 | **$8,679 / 약 1,215만원** |

영구 라이선스를 AWS EC2에 적용할 수 있는지, Software Assurance와 License Mobility가 필요한지는 Microsoft CSP의 최종 확인을 받아야 한다.

이 금액은 기존 전산실 서버의 라이선스를 재사용할 수 없다는 보수적인 상한이다. 실제 운영 데이터가 Express 한도를 넘으므로 기존 서버에서 유료 SQL Server 에디션을 사용했을 가능성이 매우 높다. **기존 계약과 AWS 이전권을 확인하기 전에는 신규 영구 라이선스를 구매하지 않는다.**

| 기존 권리 확인 결과 | 기본안 첫해 예상비용 | 판단 |
| --- | ---: | --- |
| 재사용 불가 또는 증빙 없음 | **약 2,006만원** | 현재 최종 견적의 보수적 상한 |
| 적격 기존 권리를 추가 구매비 없이 사용 가능 | **약 791만원 + 계약·SA 관련 비용** | 신규 4코어 구매비 약 1,215만원 감소 가능 |

상세 확인 절차는 [기존 SQL Server 라이선스 및 AWS 이전권 확인](./anasa-existing-sql-server-license-and-aws-mobility.md)을 따른다.

### 운영 라이선스 필수 여부

ANASA가 운영 DB로 Microsoft SQL Server를 계속 사용하는 경우에는 **정식 운영 라이선스 또는 라이선스가 포함된 서비스 계약이 반드시 필요하다.** 다만 별도의 4코어 영구 라이선스 구매만이 유일한 방법은 아니다.

| 운영 방식 | 라이선스 처리 | ANASA 판단 |
| --- | --- | --- |
| EC2에 SQL Server Standard 직접 설치 | 코어 또는 Server+CAL 라이선스 별도 확보 | 현재 견적은 4코어 영구 라이선스 기준 |
| SQL Server 라이선스 포함 EC2 AMI | EC2 시간당 요금에 라이선스 포함 | 초기 구매비는 없지만 월 사용료 증가 |
| RDS for SQL Server License Included | RDS 요금에 라이선스 포함 | 관리 부담은 줄지만 [동일 사양 RDS 별도 견적](./anasa-rds-sql-server-cost-estimate.md)과 호환성 PoC 필요 |
| 기존 보유 라이선스 사용(BYOL) | 유효한 사용권과 AWS 적용 조건 필요 | Software Assurance·License Mobility를 CSP에 확인 |
| Developer Edition | 무료 | 개발·테스트 전용이므로 실제 사용자 대상 운영 불가 |
| Express Edition | 무료 | DB당 10GB 제한으로 MISTO·MISPD·MISTW 수용 불가 |
| PostgreSQL 등으로 완전 이전 | SQL Server 라이선스 불필요 | 프로시저·스키마·애플리케이션·약 279GB 데이터 이전과 검증 필요 |

현재와 같이 SQL Server를 유지하면서 Developer Edition으로 실제 ERP를 운영하는 것은 허용되지 않는다. 따라서 운영 전환 전까지 `영구/구독 라이선스`, `라이선스 포함 AWS 서비스`, `적격 BYOL` 중 하나를 확정해야 한다. SQL Server를 완전히 제거하고 다른 DB로 이전한 경우에만 SQL Server 라이선스 비용이 없어지며, 그때는 DB 전환 개발비와 검증 비용을 별도로 산정한다.

4코어는 현재 DB가 반드시 4코어 성능을 요구해서가 아니라, VM의 코어 라이선스에 적용되는 최소 수량이다. ANASA의 현재 서버와 제안 서버는 모두 AWS EC2 가상머신이며, 코어 방식은 VM에 2 vCPU만 할당해도 VM당 최소 4코어를 라이선스해야 한다. 제안 DB 서버인 `r7i.xlarge`는 4 vCPU이므로 4코어 라이선스와 일치한다.

SQL Server Standard는 Server+CAL 방식도 가능하다. 서버 라이선스 1개와 SQL Server에 접근할 수 있는 실제 사용자 또는 장치마다 CAL을 구매하며, 애플리케이션 서버가 DB 계정 하나로 접속해도 실제 ERP 이용 인원을 기준으로 산정한다. Microsoft 공개가 기준으로 약 30 CAL에서 4코어 영구 라이선스와 비용이 비슷해지므로, 사용자·공용 단말기가 29개 이하로 고정된다면 CSP에 Server+CAL 비교 견적을 요청한다. 사용자·장치가 더 많거나 외부 이용자를 정확히 셀 수 없다면 4코어 방식이 단순하고 안전하다.

SP를 ORM으로 바꾸는 것만으로 SQL Server 라이선스가 없어지지는 않는다. PostgreSQL 등으로 DB 엔진까지 전환해야 하며, 현재 API·SP 규모를 반영한 상세 비용은 [저장 프로시저 → ORM 전환 타당성·비용 평가](./anasa-stored-procedure-to-orm-migration-assessment.md)를 따른다.

## 3. 예상 리스크 1 — 조회 최적화

조회 최적화 비용은 기본 견적에 포함하지 않고 선택 예비비로 둔다. 인덱스·캐시·EBS 성능·DB 서버 증설은 부하 측정 후 필요한 단계까지만 적용한다.

| 최적화 수준 | 적용 내용 | 월 추가비용 | 2년 차 이후 총 월 비용 |
| --- | --- | ---: | ---: |
| 무증설 | Query Store, 실행계획, 쿼리 수정, 기존 Redis 캐시 | **0원** | **약 66만원** |
| 일반 최적화 | 인덱스, EBS 0–300GB, 단일 Redis 또는 경량 gp3 보강 | **0–18만원** | **약 66–84만원** |
| 고성능 최적화 | Redis HA, 높은 gp3 성능, DB 8 vCPU·64GiB | **약 53–70만원** | **약 119–136만원** |

DB를 8 vCPU로 증설하면 SQL Server도 8코어 라이선스가 필요할 수 있다. 영구 라이선스 기준 추가 4코어 비용은 VAT 포함 **약 1,215만원 일시비용**이며, 위 고성능형 월 비용에는 포함하지 않았다.

| 고성능 최적화 첫해 | 예상 비용 |
| --- | ---: |
| 기본 첫해 비용 | 약 2,006만원 |
| 추가 4코어 영구 라이선스 | 약 1,215만원 |
| 고성능 인프라 월 증액의 연간분 | 약 632–840만원 |
| **첫해 총 예상 범위** | **약 3,854–4,061만원** |

일회성 쿼리 분석·개발·DBA 작업비는 별도이며, 실행계획과 부하 테스트 결과가 나온 뒤 확정한다.

## 4. 예상 리스크 2 — HA 서버 이중화

HA 범위에 따라 비용이 크게 달라진다. 앱 서버만 두 대로 구성하는 경우와 앱·DB 전체를 이중화하는 경우를 분리한다.

### 4.1 앱 서버 2대

구성은 `t3.large` 앱 서버 2대, 앱 EBS 2개, Application Load Balancer다. DB는 한 대이므로 앱 장애에는 대응하지만 DB 장애는 대응하지 못한다.

| 추가 항목 | 월 추가비용(VAT 포함) |
| --- | ---: |
| 두 번째 `t3.large` | 약 12만원 |
| 두 번째 앱 EBS 50GB | 약 0.7만원 |
| ALB 고정비·처리량 | 약 2.5–3.4만원 |
| **월 추가비용** | **약 15–16만원** |
| **2년 차 이후 총 월 비용** | **약 81–82만원** |
| **첫해 총 예상 비용** | **약 2,189–2,200만원** |

### 4.2 앱·DB 전체 HA

구성은 앱 `t3.large` 2대, ALB, 관리형 Redis HA, DB `r7i.xlarge` 2대와 DB EBS 1.1TB 2개다. 교차 AZ 복제 트래픽은 사용량에 따라 추가된다.

| 추가 항목 | 월 추가비용(VAT 포함) |
| --- | ---: |
| 앱 서버 이중화·ALB | 약 15–16만원 |
| 두 번째 DB EC2 | 약 36.6만원 |
| 두 번째 DB EBS 1.1TB | 약 15.4만원 |
| Redis HA | 약 13–20만원 |
| **월 추가비용** | **약 80–88만원 + 교차 AZ 전송비** |
| **2년 차 이후 총 월 비용** | **약 146–154만원 + 교차 AZ 전송비** |

대기 DB의 SQL Server 라이선스는 계약 조건에 따라 달라진다.

| 대기 DB 라이선스 조건 | 전체 HA 첫해 예상 비용 |
| --- | ---: |
| 적격 수동 대기 서버 권리 적용 가능 | **약 2,969–3,064만원** |
| 두 번째 4코어 영구 라이선스 필요 | **약 4,184–4,279만원** |

SQL Server Standard의 Basic Availability Group은 한 가용성 그룹에 단일 DB를 지원한다. ANASA는 운영 DB가 5개이므로 장애조치 일관성, 저장 프로시저의 교차 DB 호출, 로그인·SQL Agent·연결 문자열 전환을 PoC로 검증해야 한다. 요구 RTO·RPO가 확정되기 전에는 전체 HA 비용을 확정 견적으로 약속하지 않는다.

## 5. 최종 전달 요약

| 구분 | 첫해 | 2년 차 이후 월 |
| --- | ---: | ---: |
| **기본 권장안** | **약 2,006만원** | **약 66만원** |
| 기본 + 일반 조회 최적화 | **약 2,006만원 + 작업비·선택 인프라** | **약 66–84만원** |
| 기본 + 고성능 조회 최적화 | **약 3,854–4,061만원 + 작업비** | **약 119–136만원** |
| 기본 + 앱 서버 HA | **약 2,189–2,200만원** | **약 81–82만원** |
| 기본 + 전체 HA | **약 2,969–4,279만원** | **약 146–154만원 + 전송비** |

> 고객 전달 기준 견적은 **첫해 약 2,006만원, 2년 차 이후 월 약 66만원**이다. 조회 최적화와 HA는 운영 부하·RTO·RPO 및 라이선스 권리 확인 후 선택하는 위험 예비비로 별도 승인한다.

## 6. 포함·제외 범위

### 포함

- 앱 EC2 `t3.large`
- 앱 EBS gp3 50GB
- 공인 IPv4와 현재 수준의 네트워크 전송
- DB EC2 `r7i.xlarge`
- DB EBS gp3 1.1TB
- SQL Server 2022 Standard 4코어 영구 라이선스 공개가

### 제외

- BIZCLIENT와 레거시 FAX송부로그 기능
- Vercel 유료 요금
- 오프사이트 백업과 장기 보관
- 모니터링·알람 고도화
- 운영·유지보수 인력과 일회성 개발·DBA 작업비
- CSP 할인, Software Assurance, License Mobility 비용
- 교차 AZ 데이터 전송과 실제 트래픽 증가분

## 7. 참고 자료

- [ANASA 서버·DB·SQL Server 통합 예상 견적 조사](./anasa-infrastructure-cost-estimate.md)
- [ANASA RDS for SQL Server 운영 견적](./anasa-rds-sql-server-cost-estimate.md)
- [ANASA 기존 SQL Server 라이선스 및 AWS 이전권 확인](./anasa-existing-sql-server-license-and-aws-mobility.md)
- [ANASA 저장 프로시저 → ORM 전환 타당성·비용 평가](./anasa-stored-procedure-to-orm-migration-assessment.md)
- [SQL Server 2022 에디션 비교](./sql-server-2022-edition-comparison.md)
- [AWS EBS gp3 저장공간 가격 기준](./aws-ebs-gp3-pricing.md)
- [ANASA 단일 서버 가용성 및 클라우드 전환 위험 분석](../인프라/anasa-single-server-availability-and-cloud-migration.md)
- [Microsoft SQL Server 2022 가격](https://www.microsoft.com/en-us/sql-server/sql-server-2022-pricing)
- [AWS: SQL Server on EC2 라이선스 방식](https://docs.aws.amazon.com/sql-server-ec2/latest/userguide/sql-server-on-ec2-licensing.html)
- [AWS: RDS for SQL Server 라이선스](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.Licensing.html)
- [AWS Elastic Load Balancing 가격](https://aws.amazon.com/elasticloadbalancing/pricing/)
- [AWS SQL Server on EC2 HA/DR Guidance](https://docs.aws.amazon.com/prescriptive-guidance/latest/sql-server-ec2-ha-dr/welcome.html)
