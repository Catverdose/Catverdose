# Backend Developer · Java / Spring

동시 수정, 비동기 처리, 모델 서빙처럼
**실패했을 때 데이터와 시스템 상태가 어긋날 수 있는 경계**를 파고듭니다.
기능이 동작하는 데서 끝내지 않고, 재현 가능한 테스트와 측정으로 확인합니다.

**Portfolio** · https://catverdose.github.io/portfolio/

**Contact** · [eongpup@gmail.com](mailto:eongpup@gmail.com)

---

## Projects

### [UBot](https://github.com/ureca-UBot/UBot-BE) — 통신 상담 RAG 챗봇

`팀 프로젝트` · 담당: 비동기 채팅·장애 복구, AWS/GPU 인프라·배포·네트워크 접근 제어, 운영 관측·부하 검증, 모델 전환·입력 보호·게스트 채팅 · Merged PR [백엔드](https://github.com/ureca-UBot/UBot-BE/pulls?q=is%3Apr+is%3Amerged+author%3ACatverdose) · [프론트](https://github.com/ureca-UBot/UBot-FE/pulls?q=is%3Apr+is%3Amerged+author%3ACatverdose)

- **Spot GPU 교체를 고려한 비동기 채팅** ([#214](https://github.com/ureca-UBot/UBot-BE/pull/214) · [#281](https://github.com/ureca-UBot/UBot-BE/pull/281)) — API 접수(`202 + jobId`)와 Java Worker의 답변 생성을 분리. 작업 상태는 PostgreSQL, 전달은 Redis Streams에 두어 NLB 뒤 Spot GPU의 증감·교체와 분리. 모델 연결 실패·5xx는 같은 HTTP 호출만 작업 마감 내 재시도
- **다중 Worker의 작업 소유권·복구** ([#214](https://github.com/ureca-UBot/UBot-BE/pull/214) · [#289](https://github.com/ureca-UBot/UBot-BE/pull/289)) — Consumer Group과 `QUEUED → RUNNING` 조건부 선점, heartbeat·`started_at` 펜싱으로 여러 Worker의 중복 최종 저장을 방지. DB 커밋 후 ACK, Redis 메시지 유실 시 DB 기준 재발행
- **AWS/GPU 인프라 코드화** ([#133](https://github.com/ureca-UBot/UBot-BE/pull/133) · [#306](https://github.com/ureca-UBot/UBot-BE/pull/306)) — Terraform으로 실험용 Spot GPU 생성·삭제를 검증하고 기존 AWS PoC 자원을 IaC 관리 대상으로 편입. 내부 NLB, GPU AMI, Spot ASG의 모델 준비·health check·GPU 사용률 기반 확장 정책 구성
- **공개 진입점과 내부 접근 경계** ([FE #72](https://github.com/ureca-UBot/UBot-FE/pull/72) · [#306](https://github.com/ureca-UBot/UBot-BE/pull/306)) — 외부 사용자 요청은 FE Nginx(HTTPS)로 받고 `/api`는 VPC 사설 IP의 BE로 프록시. 보안 그룹으로 BE HTTP는 FE만, DB·Redis는 API/Worker만, GPU 추론 포트는 내부 NLB만 접근하도록 제한. BE Nginx는 FE 주소에서 전달한 클라이언트 IP만 신뢰
- **FE·BE CI/CD와 배포 실패 복구** ([#261](https://github.com/ureca-UBot/UBot-BE/pull/261) · [FE #72](https://github.com/ureca-UBot/UBot-FE/pull/72) · [#318](https://github.com/ureca-UBot/UBot-BE/pull/318)) — GitHub Actions OIDC·ECR image digest·SSM으로 BE 배포. FE는 릴리스 링크 전환 후 검증 실패 시 이전 버전으로 복구. CD 중단으로 Worker가 사라지면 독립 systemd 타이머로 같은 digest를 복구(강제 중단은 로컬 검증)
- **운영 지표·장애 알림** ([#268](https://github.com/ureca-UBot/UBot-BE/pull/268) · [#274](https://github.com/ureca-UBot/UBot-BE/pull/274)) — Worker 대기·실행·회수, LLM·검색·DB·Redis 메트릭을 구성하고 Prometheus 경보 6종과 런북 연결. Discord·Slack 알림·복구 통지는 로컬에서 검증
- **장애·부하 검증** ([장애 주입](https://github.com/ureca-UBot/UBot-BE/blob/develop/docs/evidence/fault-recovery-20261023.md) · [AWS 재시험](https://github.com/ureca-UBot/UBot-ServerTest/blob/main/chat-e2e/results/20261026-aws/README.md)) — 로컬 Worker·Redis·DB·vLLM 장애별 100건의 최종 저장을 확인. AWS 3,000건씩 2회에서 HTTP 202 접수 6,000건; Nginx 502·모델 컨텍스트 초과 원인을 조정한 뒤에도 최종 시스템 오류 4건과 대기 지연은 남음
- **모델 전환·벡터 검색** ([#146](https://github.com/ureca-UBot/UBot-BE/pull/146) · [#150](https://github.com/ureca-UBot/UBot-BE/pull/150) · [#153](https://github.com/ureca-UBot/UBot-BE/pull/153) · [#251](https://github.com/ureca-UBot/UBot-BE/pull/251)) — Ollama·vLLM Provider를 설정으로 교체하고 공통 계약 테스트·임베딩 Profile 분리. HNSW 검색 전환 후 합성 1만 건 기준 p50 4.6~6.2배 개선 관측
- **입력 보호·비회원 채팅** ([#104](https://github.com/ureca-UBot/UBot-BE/pull/104) · [#113](https://github.com/ureca-UBot/UBot-BE/pull/113) · [#131](https://github.com/ureca-UBot/UBot-BE/pull/131)) — LLM 호출 전 금지어 검사와 커밋 후 캐시 갱신, HttpSession 게스트 구분·대화 행 잠금으로 동시 요청에도 질문 횟수 상한 유지

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
