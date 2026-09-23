# Backend Developer · Java / Spring

동시 수정, 비동기 처리, 실시간 스트리밍처럼
**실패했을 때 데이터와 시스템 상태가 어긋날 수 있는 경계**를 파고듭니다.
기능이 동작하는 데서 끝내지 않고, 재현 가능한 테스트와 측정으로 정합성을 확인합니다.

---

## Projects

### [PetCoupon](https://github.com/PetCare-Platform/petcoupon-backend) — 선착순 쿠폰 발급 시스템

`팀 프로젝트 · 백엔드 6명` · 담당: 이벤트·쿠폰 관리, 관리자 운영·모니터링 · [Merged PR 24개](https://github.com/PetCare-Platform/petcoupon-backend/pulls?q=is%3Apr+is%3Amerged+author%3ACatverdose)

- **관리자 수정 ↔ 발급·스케줄러 경합** — 같은 쿠폰을 동시에 건드리는 경로를 `PESSIMISTIC_WRITE`로 직렬화하고 락 획득 순서를 통일. 상태 전이는 현재 상태를 조건에 둔 조건부 UPDATE로 한 번만 적용
- **SSE 모니터링의 오류 자기증폭 차단** — client disconnect → 예외 로깅 → 그 로그가 다시 SSE로 전송 → 또 실패로 이어지는 순환을 끊음. 구독자별 Queue가 차면 비즈니스 요청을 막지 않고 모니터링 이벤트를 버리되, 버린 양은 지표로 남김
- **조회 경로 분리** — 목록/실시간 조회의 데이터 소스를 나누고 실제 실행 SQL 수를 테스트로 고정

> 팀 전체 검증 (AWS EC2 3대 · 20,000 VU · 재고 10,000): 초과·중복 발급 0건, 평균 1,030 TPS. p95 16.31초로 3초 목표는 미달 — 접수 단계 대기열로 분석

---

### [Engineering Memory](https://github.com/Catverdose/engineering-memory) — 개인 개발 기록 기반 RAG

`개인 프로젝트` · [Phase 1 완료](https://github.com/Catverdose/engineering-memory/pull/1) · 근거 문서가 없으면 답하지 않고 `NO_CONTEXT`를 반환

`Browser → nginx → Go Gateway → Spring Boot → PostgreSQL/pgvector · Ollama`

- **오래된 색인 결과의 덮어쓰기 방지** — 비동기 색인 중 문서가 수정되면 저장 직전 `version · status · attempt`를 비교해 과거 embedding 결과를 버리고 재예약
- **LLM 장애 시 대화 복구** — 사용자 메시지와 `GENERATING` 상태를 먼저 저장하고, 재기동 시 남은 `GENERATING`을 `FAILED`로 전환
- **데이터 격리를 DB까지** — owner 조건(애플리케이션) + `(id, owner_id)` 복합 FK(DB)로 사용자 간 데이터가 섞이는 것을 이중으로 차단

---

### [Coupon Concurrency Experiment](https://github.com/Catverdose/concurrency-strategies) — 동시성 제어 9개 전략 비교

`개인 실험` · 팀 실험(Direct·Pessimistic 담당)을 혼자 확장 · 원시 결과 공개

- **초과 발급 없이도 깨지는 지점** — 한 회원이 3번씩 요청하게 바꾸자 1인 1매는 모든 전략이 지켰지만, 응답 분류·대상자 전원 발급·재고 원장까지 통과한 전략은 단일 인스턴스 `JVM_LOCK`뿐
- **Redis 선예약의 부작용** — 중복 요청이 회원 판정 전에 재고를 잡고 보상하는 사이 정상 회원이 품절 응답을 받음. `REDIS_WATCH`는 재시도마다 연결을 새로 만들어 로컬 포트가 고갈, 요청의 약 90%가 무응답
- **재현성 확인** — 정순·역순 실행과 VU 10~200 확장에서 실패 유형이 같게 나오는지 확인. 실패·경고가 난 실행도 지우지 않고 공개

---

### [UBot](https://github.com/ureca-UBot/UBot-BE) — 통신 상담 RAG 챗봇

`팀 프로젝트` · 담당: 팀 개발·테스트 실행 기반, Vector DB 선정 벤치마크

- **로컬과 CI 환경 일치** ([#10](https://github.com/ureca-UBot/UBot-BE/pull/10)) — 로컬 Compose와 CI Testcontainers가 같은 DB Dockerfile(PostgreSQL 18 + pgvector + PostGIS)을 쓰고, extension 생성을 Flyway로 일원화. pgvector·PostGIS·spatial function을 CI에서 실제로 검증
- **배포 구성** ([#56](https://github.com/ureca-UBot/UBot-BE/pull/56) · [#58](https://github.com/ureca-UBot/UBot-BE/pull/58)) — Java 21 multi-stage 이미지, 비밀값은 실행 시 주입, health check 기반 기동 순서
- **[Vector DB 벤치마크](https://github.com/ureca-UBot/UBot-VertorDBTest)** — pgvector · Qdrant · Weaviate · Milvus · OpenSearch를 같은 입력·자원·정답 기준으로 620회 측정. 합성 데이터 탐색 결과만으로는 제품을 확정하지 않고 실데이터 검증을 다음 단계로 정의

---

## Other

- **[Planly](https://github.com/Catverdose/planly-web)** — Todo와 공유 Calendar를 연결한 서비스. 팀 미니 프로젝트를 이어받아 backend/frontend 분리, JWT 인증·소유권 검증, 검색·필터·페이지네이션, Todo–Schedule 연동 구현

---

## Tech

**주력** · Java 21 · Spring Boot · JPA · PostgreSQL · MySQL

**사용 경험** · Redis · pgvector · PostGIS · Flyway · Docker · Testcontainers · GitHub Actions · SSE · Nginx · Go · Ollama · k6
