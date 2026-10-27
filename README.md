# Backend Developer · Java / Spring

동시 수정, 비동기 처리, 모델 서빙처럼
**실패했을 때 데이터와 시스템 상태가 어긋날 수 있는 경계**를 파고듭니다.
기능이 동작하는 데서 끝내지 않고, 재현 가능한 테스트와 측정으로 확인합니다.

**Portfolio** · https://catverdose.github.io/portfolio/

**Contact** · [eongpup@gmail.com](mailto:eongpup@gmail.com)

---

## Projects

### [UBot](https://github.com/ureca-UBot/UBot-BE) — 통신 상담 RAG 챗봇

`팀 프로젝트` · 담당: 비동기 채팅·장애 복구, 입력 필터·비회원 채팅, 모델 서버 전환, 인프라·운영 모니터링 · Merged PR [백엔드](https://github.com/ureca-UBot/UBot-BE/pulls?q=is%3Apr+is%3Amerged+author%3ACatverdose) · [프론트](https://github.com/ureca-UBot/UBot-FE/pulls?q=is%3Apr+is%3Amerged+author%3ACatverdose)

- **비동기 채팅과 작업 복구** ([#214](https://github.com/ureca-UBot/UBot-BE/pull/214) · [#281](https://github.com/ureca-UBot/UBot-BE/pull/281) · [#289](https://github.com/ureca-UBot/UBot-BE/pull/289)) — API 접수(202·jobId)와 Worker 실행을 분리. DB 작업 원장과 Redis Streams 알림, heartbeat·펜싱·커밋 후 ACK로 장애 복구 경계를 관리하고 실행·복구 책임을 분리
- **배포 실패 후 Worker 자동 복구** ([#318](https://github.com/ureca-UBot/UBot-BE/pull/318)) — CD 중단으로 Worker가 사라지면 독립 systemd 타이머가 배포·API 상태를 확인해 같은 이미지 digest로 복구. 강제 중단 시나리오는 로컬 Linux에서 검증
- **AWS 인프라·CI/CD** ([#133](https://github.com/ureca-UBot/UBot-BE/pull/133) · [#261](https://github.com/ureca-UBot/UBot-BE/pull/261) · [#306](https://github.com/ureca-UBot/UBot-BE/pull/306)) — Terraform으로 네트워크·EC2·GPU 인프라를 코드화하고, GitHub Actions OIDC → ECR → SSM 배포 파이프라인 구성. 실제 AWS 배포와 GPU 테스트 자원 생성·삭제를 검증
- **벡터 검색·운영 관측** ([#251](https://github.com/ureca-UBot/UBot-BE/pull/251) · [#268](https://github.com/ureca-UBot/UBot-BE/pull/268) · [#274](https://github.com/ureca-UBot/UBot-BE/pull/274)) — FAQ 검색을 HNSW로 전환해 합성 1만 건 기준 p50 4.6~6.2배 개선을 관측. Worker·LLM·검색·DB·Redis 지표와 장애 알림 구성
- **LLM 호출 전 입력 차단** ([#104](https://github.com/ureca-UBot/UBot-BE/pull/104)) — 금지어를 임베딩·LLM 호출 전에 검사하고, 활성 금지어 캐시는 변경 트랜잭션 커밋 이후 갱신
- **비회원 채팅과 횟수 제한** ([#113](https://github.com/ureca-UBot/UBot-BE/pull/113) · [#131](https://github.com/ureca-UBot/UBot-BE/pull/131)) — HttpSession으로 게스트를 구분하고, 대화 행 잠금으로 동시 요청에도 질문 횟수 상한을 유지
- **모델 서버 전환** ([#146](https://github.com/ureca-UBot/UBot-BE/pull/146) · [#150](https://github.com/ureca-UBot/UBot-BE/pull/150) · [#153](https://github.com/ureca-UBot/UBot-BE/pull/153)) — Ollama·vLLM 구현체를 설정으로 선택하고 공통 계약 테스트로 검증. 모델별 벡터 Profile 분리

> 회원 인증(JWT), FAQ 관리, 매장 지도 검색은 팀원 담당

---

### [PetCoupon](https://github.com/PetCare-Platform/petcoupon-backend) — 선착순 쿠폰 발급 시스템

`팀 프로젝트 · 백엔드 6명` · 담당: 이벤트·쿠폰 관리, 관리자 운영·모니터링 · [Merged PR 24개](https://github.com/PetCare-Platform/petcoupon-backend/pulls?q=is%3Apr+is%3Amerged+author%3ACatverdose)

- **SSE 모니터링의 오류 자기증폭 차단** — 큐가 차면 낸 오류 로그를 모니터링이 다시 수집하던 순환을 끊음. 구독자별 큐를 1,000개에서 50개로 줄이고, 가득 차면 오래된 로그를 버리되 버린 개수는 통지
- **관리자 수정 ↔ 발급·스케줄러 경합** — 같은 쿠폰을 동시에 건드리는 경로를 `PESSIMISTIC_WRITE`로 직렬화하고 락 획득 순서를 통일. 상태 전이는 현재 상태를 조건에 둔 조건부 UPDATE로 한 번만 적용
- **조회 경로 분리** — 목록/실시간 조회의 데이터 소스를 나누고 실제 실행 SQL 수를 테스트로 고정

> 팀 전체 검증 (AWS EC2 3대 · 20,000 VU · 재고 10,000): 초과·중복 발급 0건, 평균 1,030 TPS. 접수 p95 16.31초로 목표(500ms) 미달 — 접수 단계 대기열로 분석

---

### [Engineering Memory](https://github.com/Catverdose/engineering-memory) — 개인 개발 기록 기반 RAG

`개인 프로젝트` · [Phase 1 완료](https://github.com/Catverdose/engineering-memory/pull/1) · 근거 문서가 없으면 답하지 않고 `NO_CONTEXT`를 반환

`Browser → nginx → Go Gateway → Spring Boot → PostgreSQL/pgvector · Ollama`

- **오래된 색인 결과의 덮어쓰기 방지** — 비동기 색인 중 문서가 수정되면 저장 직전 `version · status · attempt`를 비교해 과거 embedding 결과를 버리고 재예약
- **LLM 장애 시 대화 복구** — 사용자 메시지와 `GENERATING` 상태를 먼저 저장하고, 재기동 시 남은 `GENERATING`을 `FAILED`로 전환
- **데이터 격리를 DB까지** — owner 조건(애플리케이션) + `(id, owner_id)` 복합 FK(DB)로 사용자 간 데이터가 섞이는 것을 이중으로 차단

---

## Experiments

### [Coupon Concurrency](https://github.com/Catverdose/concurrency-strategies) — 동시성 제어 9개 전략 비교

`개인 실험` · 팀 실험(공통 코드 · Direct · Pessimistic 담당)을 혼자 확장 · 원시 결과 공개

- **초과 발급 없이도 깨지는 지점** — 한 회원이 3번씩 요청하게 바꾸자 1인 1매는 모든 전략이 지켰지만, 응답 분류·대상자 전원 발급·재고 원장까지 통과한 전략은 단일 인스턴스 `JVM_LOCK`뿐
- **Redis 선예약의 부작용** — 중복 요청이 회원 판정 전에 재고를 잡고 보상하는 사이 정상 회원이 품절 응답을 받음
- **낮은 지연의 정체** — `REDIS_WATCH`는 요청의 약 90%가 무응답. 재시도마다 연결을 새로 만든 것이 원인으로 유력하며, VU 10~200 모두에서 재현

### [Vector DB Benchmark](https://github.com/ureca-UBot/UBot-VertorDBTest) — 5개 DB · 620회 측정

`UBot · 단독 수행`

- pgvector · Qdrant · Weaviate · Milvus · OpenSearch를 같은 입력·자원·정답 기준으로 측정 (합성 1만 청크)
- 성능은 Qdrant가 가장 좋았지만, 팀은 운영할 저장소를 늘리지 않으려고 이미 쓰는 PostgreSQL의 pgvector를 선택

### [LLM Serving Benchmark](https://github.com/ureca-UBot/UBot-LLMTest) — 서빙 엔진 4종 비교

`UBot · 팀 내 실험`

- Ollama · llama.cpp · vLLM · SGLang을 로컬 RTX 3060, AWS T4, L4에서 비교. 기준선은 HTTP 완료 초당 8건
- 엔진을 바꿀 때마다 GPU 메모리가 비었는지 확인한 뒤 다음 측정을 시작
- L4에서 vLLM 9.13, SGLang 8.17로 기준선 초과를 관측 (반복 검증 전의 탐색 결과)

---

## Other

- **[Planly](https://github.com/Catverdose/planly-web)** — Todo와 공유 Calendar를 연결한 서비스. 3인 팀에서 Todo 도메인과 소유권 검증을 맡았고, 이후 개인 저장소에서 화면을 React로 이전

---

## Tech

**주력** · Java 21 · Spring Boot · JPA · PostgreSQL · MySQL

**사용 경험** · Redis · pgvector · PostGIS · Flyway · Docker · Nginx · GitHub Actions · AWS EC2 · Terraform · GCP · Testcontainers · k6 · SSE · Go · Ollama · vLLM · React · TypeScript
