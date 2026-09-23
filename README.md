# Backend Developer

Java와 Spring을 중심으로 백엔드 시스템을 개발하고 있습니다.

동시 수정, 비동기 처리, 실시간 스트리밍처럼  
**실패했을 때 데이터와 시스템 상태가 달라질 수 있는 경계**를 주로 파고듭니다.

구현한 기능이 동작하는 것에서 끝내지 않고,  
재현 가능한 테스트와 측정을 통해 정합성·실패 상태·운영 영향을 확인하려고 합니다.

---

## Focus

- **Data Consistency** — transaction boundary, DB constraint, concurrency control
- **Failure Handling** — async processing, scheduler, SSE, recovery
- **Verification** — integration test, regression test, benchmark
- **Backend Infrastructure** — Docker, Testcontainers, CI/CD, observability

---

## Selected Work

### [PetCoupon](https://github.com/PetCare-Platform/petcoupon-backend)
**Team Project · 24 Merged PRs**

선착순 쿠폰 발급 시스템에서  
**이벤트·쿠폰 관리와 관리자 운영·모니터링 영역**을 담당했습니다.

- 관리자 쿠폰 수정과 발급·스케줄러 간 경쟁을 `PESSIMISTIC_WRITE`와 일관된 lock order로 제어
- 이벤트·쿠폰 상태 전이를 조건부 UPDATE 기반 scheduler로 구현
- Redis 기반 관리자 세션 인증과 만료·폐기 구조 구현
- 관리자 SSE 모니터링에서 client disconnect → exception handler → logging → SSE로 이어지는 오류 feedback loop 추적 및 제거
- 구독자별 Queue로 느린 SSE client의 영향을 격리하고 로그 마스킹 비용을 제한
- 목록과 실시간 조회의 데이터 소스를 분리하고 실제 SQL 수를 테스트로 검증

**Team validation**  
20,000-request load test · overselling / duplicate issuance 0 · 1,030 TPS

---

### [Engineering Memory](https://github.com/Catverdose/engineering-memory)
**Personal Project · Ongoing**

개발 문서를 저장하고 검색·대화할 수 있는 개인 RAG 시스템입니다.

`Browser → Nginx → Go Gateway → Spring Boot → PostgreSQL / pgvector → Ollama`

- 비동기 문서 색인 중 수정된 문서가 과거 embedding 결과로 덮이지 않도록 `version / indexing attempt` 검증
- `PENDING / READY / FAILED` 상태와 bounded queue, 작업 coalescing, 재기동 복구 구성
- 사용자 메시지와 `GENERATING` 상태를 먼저 저장해 LLM 처리 중 장애가 발생해도 대화 상태 복구 가능
- 검색 근거가 없으면 `NO_CONTEXT`로 처리해 불필요한 LLM 호출 차단
- owner 조건과 복합 FK를 통해 애플리케이션과 DB 양쪽에서 데이터 격리

`Java 21 · Spring Boot · PostgreSQL · pgvector · Go · Docker · Ollama`

---

### [Vector DB Benchmark](https://github.com/ureca-UBot/UBot-VertorDBTest)
**Benchmark / Experiment**

RAG 서비스의 Vector DB 후보를  
**같은 검색 품질과 자원 조건에서 비교하기 위한 benchmark harness**를 구현했습니다.

- pgvector · Qdrant · Weaviate · Milvus · OpenSearch
- BGE-M3 dense 1024d · cosine · Top-K 10
- Java exact cosine search를 Ground Truth로 사용해 Recall@10 계산
- latency · QPS · CPU · RAM · index readiness 측정
- DB / Engine / Index 14개 구성
- 검색 설정 124개 × 독립 재구축 5회 = **620 measurements**
- calibration / evaluation query 분리 및 입력 SHA-256 고정
- 측정 이상치와 warm-up 미달을 제거하지 않고 원시 결과와 warning으로 보존

단일 latency 수치로 제품을 고르지 않고,  
필요한 Recall 수준에서 성능·자원·운영 복잡도를 함께 비교했습니다.

---

### [UBot Backend](https://github.com/ureca-UBot/UBot-BE)
**Team Project · Backend Infrastructure**

통신 상담용 RAG 챗봇 백엔드에서  
팀이 같은 환경에서 개발·테스트할 수 있는 실행 기반을 담당했습니다.

- PostgreSQL + pgvector + PostGIS + Ollama 개발 환경 구성
- Testcontainers로 테스트마다 독립 DB를 생성해 개발 DB와 테스트 환경 격리
- 로컬 Compose와 CI가 동일한 PostgreSQL Dockerfile 사용
- Flyway에서 pgvector / PostGIS extension 생성 책임 통일
- 실제 vector 저장·검색과 PostGIS spatial function을 CI에서 검증
- Java 21 multi-stage Docker image 및 배포용 Compose 구성

`Java 21 · Spring Boot · PostgreSQL · pgvector · PostGIS · Flyway · Testcontainers · Docker`

---

## Other Experience

**[Planly](https://github.com/Catverdose/planly-web)**  
Todo와 공유 Calendar를 연결한 Spring/JPA 웹 서비스.  
JWT 인증, 소유권 검증, 검색·필터·페이지네이션, Todo–Schedule 연동을 구현했습니다.

**[Coupon Concurrency Experiment](https://github.com/PetCare-Platform/coupon-concurrency-experiment)**  
선착순 쿠폰 문제에서 Direct / Pessimistic Lock 구현과 실험 문서화를 담당했고, 이후 개인적으로 동시성 제어 전략 비교 실험을 확장했습니다.

**[Java Web Fundamentals](https://github.com/Catverdose/memo-servlet-jsp)**  
Servlet → Service → DAO → JDBC 흐름과 JDBC transaction을 직접 구현하며 Spring 이전의 Java Web 요청 흐름을 학습했습니다.

---

## Tech

**Core**  
Java 21 · Spring Boot · JPA · MySQL · PostgreSQL

**Data / Infrastructure**  
Redis · pgvector · PostGIS · Docker · Testcontainers · Flyway · GitHub Actions

**Project Experience**  
Kafka · SSE · Nginx · Ollama · k6 · JUnit · Awaitility
