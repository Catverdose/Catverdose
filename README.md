# Backend Developer · Java / Spring

동시 요청, 비동기 처리, 배포와 장애 복구처럼
**실패했을 때 데이터와 시스템 상태가 어긋날 수 있는 경계**를 파고듭니다.
서비스 코드에서 AWS 인프라까지 직접 설계하고, 재현 가능한 테스트와 측정으로 확인합니다.

**Portfolio** · https://catverdose.github.io/portfolio/

**Contact** · [eongpup@gmail.com](mailto:eongpup@gmail.com)

---

## Projects

### [UBot](https://github.com/ureca-UBot/UBot-BE) — 통신사 고객 상담 RAG 챗봇

`팀 프로젝트 · 2026.09~10` · 담당: AI 채팅 백엔드, AWS 인프라 설계와 배포, 운영 · 병합 PR [백엔드 34](https://github.com/ureca-UBot/UBot-BE/pulls?q=is%3Apr+is%3Amerged+author%3ACatverdose) · [프론트 15](https://github.com/ureca-UBot/UBot-FE/pulls?q=is%3Apr+is%3Amerged+author%3ACatverdose)

`사용자 → FE Nginx(HTTPS) → BE Nginx → API·Worker → State EC2(PostgreSQL·Redis) · 내부 NLB → Spot GPU ASG(vLLM)`

**AWS 인프라**

- **서버 역할과 네트워크 경계** ([#306](https://github.com/ureca-UBot/UBot-BE/pull/306) · [#261](https://github.com/ureca-UBot/UBot-BE/pull/261) · [FE #72](https://github.com/ureca-UBot/UBot-FE/pull/72)) — FE·BE·State EC2와 GPU를 나누고 보안 그룹을 단계별로 제한. 외부 요청은 FE Nginx로 받고, BE HTTP는 FE에서만, PostgreSQL·Redis는 API·Worker에서만, GPU 추론 포트는 내부 NLB에서만 받음. BE Nginx는 FE 사설 IP가 전달한 클라이언트 IP만 신뢰하고, 그 IP와 회원 토큰 기준으로 채팅 접수를 제한
- **Spot GPU 오토스케일링** ([#306](https://github.com/ureca-UBot/UBot-BE/pull/306)) — 모델을 미리 담은 AMI로 부팅하고 평균 GPU 사용률 60%를 기준으로 최대 3대까지 조절. 모델 적재 중 축소되지 않게 준비 시간 420초, 교체 중 답변이 끝나도록 연결 종료 유예 120초, 중단에 대비한 용량 재조정을 설정. 자동 확장 자체는 실제 트래픽으로 검증하지 않음
- **인프라 코드화** ([#133](https://github.com/ureca-UBot/UBot-BE/pull/133) · [#306](https://github.com/ureca-UBot/UBot-BE/pull/306)) — 실험용 GPU 서버를 만들고 지우는 구성을 분리하고, 기존 AWS 자원(VPC, 서버, ECR, IAM, 보안 그룹 등)을 Terraform 원격 state로 가져옴. FE·BE·State에 `prevent_destroy` 설정. 원격 plan으로 생성 0·변경 1·삭제 0을 확인(이 plan은 적용하지 않음)
- **CI/CD와 배포 실패 복구** ([#261](https://github.com/ureca-UBot/UBot-BE/pull/261) · [FE #72](https://github.com/ureca-UBot/UBot-FE/pull/72) · [#318](https://github.com/ureca-UBot/UBot-BE/pull/318)) — GitHub Actions OIDC·ECR·SSM으로 배포하고 CD 권한은 지정한 ECR과 인스턴스로 제한. FE는 릴리스 링크를 바꾼 뒤 새 버전이 안 뜨면 이전 버전으로 복구. CD 중단으로 Worker가 꺼지면 별도 systemd 타이머가 같은 이미지 digest로 복구(강제 종료는 로컬 검증)
- **운영 지표와 장애 알림** ([#268](https://github.com/ureca-UBot/UBot-BE/pull/268) · [#274](https://github.com/ureca-UBot/UBot-BE/pull/274)) — Worker·LLM·검색·DB·Redis 지표와 Prometheus 경보 6종, 런북 구성. Discord·Slack 알림은 로컬에서 검증

**채팅 백엔드**

- **채팅 답변을 비동기 Worker로 분리** ([#214](https://github.com/ureca-UBot/UBot-BE/pull/214) · [#281](https://github.com/ureca-UBot/UBot-BE/pull/281) · [#289](https://github.com/ureca-UBot/UBot-BE/pull/289)) — API는 질문을 접수해 `202 + jobId`를 돌려주고 Worker가 답변을 만듦. 작업의 기준은 PostgreSQL, Redis Streams는 실행 신호로만 쓰고 DB 커밋 뒤 ACK. heartbeat가 끊긴 작업은 다른 Worker가 넘겨받고, 늦게 끝난 이전 실행의 결과는 `started_at` 펜싱으로 버림. 로컬 동시 1,000건에서 시간 초과·실패 321건 → 0건, 대신 전체 완료 시간은 133초 → 373초로 늘어남
- **장애와 부하 시험** ([장애 주입](https://github.com/ureca-UBot/UBot-BE/blob/develop/docs/evidence/fault-recovery-20261023.md) · [AWS 시험](https://github.com/ureca-UBot/UBot-ServerTest/blob/main/chat-e2e/results/20261026-aws/README.md) · [#318](https://github.com/ureca-UBot/UBot-BE/pull/318)) — 로컬에서 Worker·Redis·DB·vLLM을 각각 멈춘 100건씩 시험해 400건 모두 답변 저장, 유실·중복 저장 0건. AWS에서 GPU 3대(Spot 2대 포함)로 3,000건씩 2회 보내 접수 응답, 최종 상태, DB 기록을 작업 ID로 대조. Nginx 연결 한도로 난 502와 모델 컨텍스트 초과를 고친 뒤에도 시스템 오류 4건과 긴 대기(접수→완료 p95 445·646초)는 남음
- **상담사 연결** ([#244](https://github.com/ureca-UBot/UBot-BE/pull/244) · [FE #68](https://github.com/ureca-UBot/UBot-FE/pull/68)) — 서버에 저장된 해당 대화만 상담사에게 인계하고, 재배정 뒤 이전 상담사의 접근을 차단. 메시지는 금지어와 임베딩 유사도 검사를 통과한 것만 전달하고, 재전송은 멱등 처리
- **입력 보호와 비회원 채팅** ([#104](https://github.com/ureca-UBot/UBot-BE/pull/104) · [#113](https://github.com/ureca-UBot/UBot-BE/pull/113) · [#131](https://github.com/ureca-UBot/UBot-BE/pull/131) · [FE #28](https://github.com/ureca-UBot/UBot-FE/pull/28)) — 임베딩과 LLM을 부르기 전에 금지어를 검사. 비회원은 HttpSession으로 구분하고 행 잠금으로 질문 횟수 상한을 유지하며, 로그인 후 대화를 회원에게 승계
- **모델 서버 전환과 FAQ 검색** ([#146](https://github.com/ureca-UBot/UBot-BE/pull/146) · [#150](https://github.com/ureca-UBot/UBot-BE/pull/150) · [#153](https://github.com/ureca-UBot/UBot-BE/pull/153) · [#251](https://github.com/ureca-UBot/UBot-BE/pull/251)) — `AI_MODE` 설정으로 Ollama와 vLLM을 바꾸고, 벡터를 모델별 Profile로 저장해 백필 API로 채움. FAQ 검색은 pgvector iterative scan을 쓴 HNSW로 전환해 합성 1만 건에서 p50 4.6~6.2배 개선을 관측(실제 FAQ 재측정 전)

그 밖에 상품 4종 관리 API와 화면([#157](https://github.com/ureca-UBot/UBot-BE/pull/157) · [FE #36](https://github.com/ureca-UBot/UBot-FE/pull/36)), 본인 회선 조회 API 10개([#165](https://github.com/ureca-UBot/UBot-BE/pull/165) · [FE #40](https://github.com/ureca-UBot/UBot-FE/pull/40)), 프론트 구조 분리([FE #64](https://github.com/ureca-UBot/UBot-FE/pull/64))를 맡음.

> 회원 인증(JWT), FAQ 관리, 매장 지도 검색은 팀원 담당

---

### [PetCoupon](https://github.com/PetCare-Platform/petcoupon-backend) — 선착순 쿠폰 발급 시스템

`팀 프로젝트 · 백엔드 6명 · 2026.08~09` · 담당: 이벤트·쿠폰 관리, 관리자 운영·모니터링 · [병합 PR 24](https://github.com/PetCare-Platform/petcoupon-backend/pulls?q=is%3Apr+is%3Amerged+author%3ACatverdose)

- **SSE 로그 모니터링의 오류 순환 차단** ([#179](https://github.com/PetCare-Platform/petcoupon-backend/pull/179) · [#192](https://github.com/PetCare-Platform/petcoupon-backend/pull/192)) — 관리자에게 WARN/ERROR를 실시간으로 보내는 API에서, 연결이 끊긴 뒤 난 오류 로그를 모니터링이 다시 수집해 불어나던 순환을 끊음. 구독자별 큐를 1,000개에서 50개로 줄이고, 가득 차면 오래된 로그를 버리되 버린 개수는 통지. 로컬에서 연결을 62회 강제 종료해 관련 예외 0건 확인
- **관리자 수정 ↔ 발급·스케줄러 경합** ([#87](https://github.com/PetCare-Platform/petcoupon-backend/pull/87) · [#74](https://github.com/PetCare-Platform/petcoupon-backend/pull/74) · [#145](https://github.com/PetCare-Platform/petcoupon-backend/pull/145)) — 쿠폰 수정은 `PESSIMISTIC_WRITE`와 `@DynamicUpdate`로 낡은 값의 덮어쓰기를 막고, 락 순서를 발급 경로와 맞춤. 상태 전이는 현재 상태를 조건에 둔 조건부 UPDATE로 한 번만 적용
- **관리자 인증과 로그 마스킹** ([#108](https://github.com/PetCare-Platform/petcoupon-backend/pull/108) · [#221](https://github.com/PetCare-Platform/petcoupon-backend/pull/221)) — 공유 코드로 단기 세션 토큰을 발급하고 Redis에는 해시만 저장. 코드가 설정되지 않으면 전부 거부. 모니터링 로그의 인증 헤더, 쿠키, 토큰을 마스킹
- **재고 데이터의 DB·Redis 경계** ([#135](https://github.com/PetCare-Platform/petcoupon-backend/pull/135) · [#182](https://github.com/PetCare-Platform/petcoupon-backend/pull/182)) — 관리자 목록은 DB 확정값, 단건 현황은 Redis 실시간값으로 나눔. 쿠폰 생성·총수량 수정 때 Redis 재고를 초기화하고 다시 읽어 대조하며, 실패하면 DB 변경도 롤백

> 팀 전체 검증 (AWS EC2 3대 · 20,000 VU · 재고 10,000): 초과·중복 발급 0건, 평균 접수 1,030 TPS. 접수 p95 16.31초로 목표(500ms) 미달 — 접수 단계 대기열로 분석

---

### [Engineering Memory](https://github.com/Catverdose/engineering-memory) — 개인 개발 기록 기반 RAG

`개인 프로젝트 · 2026.09` · [Phase 1 완료](https://github.com/Catverdose/engineering-memory/pull/1) · 근거 문서가 없으면 답하지 않고 `NO_CONTEXT`를 반환

`Browser → nginx → Go Gateway → Spring Boot → PostgreSQL/pgvector · Ollama`

- **오래된 색인 결과의 덮어쓰기 방지** — 비동기 색인 중 문서가 수정되면 저장 직전 `version · status · attempt`를 비교해 과거 embedding 결과를 버리고 재예약
- **LLM 장애 시 대화 복구** — 사용자 메시지와 `GENERATING` 상태를 먼저 저장하고, 재기동 시 남은 `GENERATING`을 `FAILED`로 전환
- **데이터 격리를 DB까지** — owner 조건(애플리케이션) + `(id, owner_id)` 복합 FK(DB)로 사용자 간 데이터가 섞이는 것을 이중으로 차단

---

## Experiments

### [Coupon Concurrency](https://github.com/Catverdose/concurrency-strategies) — 동시성 제어 9개 전략 비교

`개인 실험 · 2026.08` · 팀 실험(공통 코드 · Direct · Pessimistic 담당)을 혼자 확장 · 원시 결과 공개

- **초과 발급 없이도 깨지는 지점** — 한 회원이 3번씩 요청하게 바꾸자 1인 1매는 모든 전략이 지켰지만, 응답 분류·대상자 전원 발급·재고 원장까지 통과한 전략은 단일 인스턴스 `JVM_LOCK`뿐
- **Redis 선예약의 부작용** — 중복 요청이 회원 판정 전에 재고를 잡고 보상하는 사이 정상 회원이 품절 응답을 받음
- **낮은 지연의 정체** — `REDIS_WATCH`는 요청의 약 90%가 무응답. 재시도마다 연결을 새로 만든 것이 원인으로 유력하며, VU 10~200 모두에서 재현

### [Vector DB Benchmark](https://github.com/ureca-UBot/UBot-VectorDBTest) — 5개 DB · 620회 측정

`UBot · 단독 수행 · 2026.09`

- pgvector · Qdrant · Weaviate · Milvus · OpenSearch를 같은 입력·자원·정답 기준으로 측정 (합성 1만 청크, 구성마다 5회 재구축)
- 같은 Recall에서 Qdrant·OpenSearch가 더 빨랐지만 차이는 p50 1~2ms. 운영할 저장소를 늘리지 않으려고 이미 쓰는 PostgreSQL의 pgvector를 선택

### [LLM Serving Benchmark](https://github.com/ureca-UBot/UBot-LLMTest) — 서빙 엔진 4종 비교

`UBot · 팀 내 실험 · 2026.10`

- Ollama · llama.cpp · vLLM · SGLang을 로컬 RTX 3060, AWS T4, L4에서 비교. 기준선은 HTTP 완료 초당 8건
- 엔진을 바꿀 때마다 GPU 메모리가 비었는지 확인한 뒤 다음 측정을 시작
- L4에서 vLLM 9.13, SGLang 8.17로 기준선 초과를 관측 (반복 검증 전의 탐색 결과)

---

## Other

- **[Planly](https://github.com/Catverdose/planly-web)** — Todo와 공유 Calendar를 연결한 서비스. 3인 팀에서 Todo 도메인과 소유권 검증을 맡았고, 이후 개인 저장소에서 화면을 React로 이전 (2026.07~08)

---

## Tech

**주력** · Java 21 · Spring Boot · JPA · PostgreSQL · MySQL

**인프라** · AWS(EC2, VPC, NLB, ASG, ECR, SSM, IAM) · Terraform · Docker · Nginx · GitHub Actions · Prometheus · GCP

**데이터** · Redis(Streams) · pgvector · PostGIS · Flyway

**테스트** · Testcontainers · k6

**그 외** · SSE · Go · Ollama · vLLM · React · TypeScript
