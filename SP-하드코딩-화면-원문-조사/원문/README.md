# 조사 대상 SP 전체 원문

이 폴더는 [화면별 SP 하드코딩 목록](../01-화면별-SP-하드코딩-목록.md)에 기록한 우선 조사 SP의 전체 원문을 DB별 Markdown으로 보관한다. SQL 본문은 들여쓰기·주석·샘플 실행문을 포함해 체크인 원문을 그대로 옮겼다. 원문 앞의 화면·route 표시는 매핑 정보이며 SQL 본문에 추가한 내용이 아니다.

## 원문 파일

- [MISCM](./MISCM.md) — 이번 우선 목록에는 공통 화면 caller가 없어 원문 파일을 만들지 않았다.
- [MISPD](./MISPD.md) — 생산계획·품목·재고·입고·작업 관련 SP
- [MISSA](./MISSA.md) — 이번 우선 목록에는 직접 caller가 없어 원문 파일을 만들지 않았다.
- [MISTO](./MISTO.md) — 공구·배송·시공시스템지원 관련 SP
- [MISTW](./MISTW.md) — 주문·출고·마감·매입·AS 관련 SP

실서버에만 존재하는 `MISTW.proc_LOGB005_S_03_V2`는 backend 저장소 파일이 없으므로 별도 [실서버 원문](./MISTW-proc_LOGB005_S_03_V2-live.md)으로 보관했다.

## 원문 기준

- backend 원문: `be_anasa` `develop` HEAD `e3cb724616e872435f487d66c4703707d1ebb662`
- 운영 원문: `live-schema-20260824T064641Z` snapshot
- SQL은 실행하지 않았고, 고객 데이터 행을 포함하지 않았다.
- 운영 snapshot과 backend 정의가 다른 SP는 파일 머리말에 `운영 원문 우선`을 표시했다.
