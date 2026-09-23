# Backend Developer · Java / Spring

동시 수정, 비동기 처리, 실시간 스트리밍처럼
**실패했을 때 데이터와 시스템 상태가 어긋날 수 있는 경계**를 파고듭니다.
기능이 동작하는 데서 끝내지 않고, 재현 가능한 테스트와 측정으로 정합성을 확인합니다.

---

## Projects

### [PetCoupon](https://github.com/PetCare-Platform/petcoupon-backend) — 선착순 쿠폰 발급 시스템

`팀 프로젝트` · 담당: 이벤트·쿠폰 관리, 관리자 운영·모니터링 · [Merged PR 24개](https://github.com/PetCare-Platform/petcoupon-backend/pulls?q=is%3Apr+is%3Amerged+author%3ACatverdose)

- **관리자 수정 ↔ 발급·스케줄러 경합** — 같은 쿠폰을 동시에 건드리는 경로를 `PESSIMISTIC_WRITE`로 직렬화하고, 락 획득 순서를 통일해 교착을 막음
- **SSE 오류 feedback loop 제거** — client disconnect → 예외 로깅 → 그 로그가 다시 SSE로 전송 → 또 실패로 이어지는 순환을 추적해 끊고, 구독자별 Queue로 느린 client를 격리
- **조회 경로 분리** — 목록/실시간 조회의 데이터 소스를 나누고, 실제 실행 SQL 수를 테스트로 고정

> 팀 전체 검증: 20,000 요청 부하 테스트 · 초과/중복 발급 0건 · 1,030 TPS

---

### [Engineering Memory](https://github.com/Catverdose/engineering-memory) — 개인 개발 기록 기반 RAG

`개인 프로젝트` · Phase 1 완료 · 근거 문서가 없으면 답하지 않고 `NO_CONTEXT`를 반환하는 지식 어시스턴트 · [Architecture](https://github.com/Catverdose/engineering-memory/blob/main/docs/architecture.md)

`Browser → Nginx → Go Gateway → Spring Boot → PostgreSQL/pgvector → Ollama`

- **오래된 색인 결과의 덮어쓰기 방지** — 비동기 색인 중 문서가 수정되면 `version / indexing attempt`를 비교해 과거 embedding 결과를 버림. bounded queue·작업 coalescing·재기동 복구로 `PENDING / READY / FAILED` 상태 유지
- **LLM 장애 시 대화 복구** — 사용자 메시지와 `GENERATING` 상태를 먼저 저장한 뒤 생성을 호출해, 처리 중 장애가 나도 대화 상태를 되살림
- **데이터 격리를 DB까지** — owner 조건(애플리케이션) + 복합 FK(DB)로 사용자 간 데이터 참조를 이중으로 차단

---

### [Vector DB Benchmark](https://github.com/ureca-UBot/UBot-VertorDBTest) — RAG용 Vector DB 선정 실험

`Benchmark / Experiment`

- **비교 조건 고정** — pgvector · Qdrant · Weaviate · Milvus · OpenSearch를 BGE-M3 1024d · cosine · Top-10 · 동일 자원 제한에서 비교. Java exact cosine search를 Ground Truth로 Recall@10 산출
- **측정 신뢰성** — DB/Engine/Index 14개 구성, 검색 설정 124개 × 독립 재구축 5회 = 620 measurements. calibration/evaluation query 분리, 입력 SHA-256 고정, 이상치는 지우지 않고 warning으로 보존

---

### [UBot Backend](https://github.com/ureca-UBot/UBot-BE) — 통신 상담 RAG 챗봇

`팀 프로젝트` · 담당: 팀 개발·테스트 실행 기반

- **테스트 격리** — Testcontainers로 테스트마다 독립 DB를 만들어 개발 DB와 분리
- **환경 차이 제거** — 로컬 Compose와 CI가 같은 PostgreSQL Dockerfile을 쓰고, pgvector/PostGIS extension 생성 책임을 Flyway로 일원화
- **CI에서 실제 기능 검증** — 실제 vector 저장·검색과 PostGIS spatial function을 CI에서 실행

---

## Other

- **[Coupon Concurrency Experiment](https://github.com/PetCare-Platform/coupon-concurrency-experiment)** — 선착순 쿠폰의 동시성 제어 전략을 같은 조건에서 비교. Direct·Pessimistic Lock 구현과 실험 문서화 담당, 이후 개인적으로 비교 실험 확장
- **[Planly](https://github.com/Catverdose/planly-web)** — Todo와 공유 Calendar를 연결한 서비스. 팀 미니 프로젝트를 이어받아 backend/frontend 분리, JWT 인증·소유권 검증, 검색·필터·페이지네이션, Todo–Schedule 연동 구현

---

## Tech

**주력** · Java 21 · Spring Boot · JPA · PostgreSQL · MySQL

**사용 경험** · Redis · pgvector · PostGIS · Flyway · Docker · Testcontainers · GitHub Actions · SSE · Nginx · Go · Ollama · k6
