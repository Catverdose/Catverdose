# Backend Developer

Java와 Spring을 중심으로 웹 백엔드를 개발하고 있습니다.

선착순 쿠폰 발급 시스템에서는 대규모 동시 요청 환경을 검증하고  
Redis, Kafka, 성능 테스트, CI/CD와 AWS 배포까지 경험했습니다.

> 기능 구현에 그치지 않고  
> **문제의 원인을 파악하고 안정적으로 개선할 수 있는 백엔드 개발자**를 목표로 합니다.
---

## Tech Stack

### Core

![Java](https://img.shields.io/badge/Java-007396?logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?logo=springboot&logoColor=white)
![Spring MVC](https://img.shields.io/badge/Spring%20MVC-6DB33F?logo=spring&logoColor=white)
![Spring Data JPA](https://img.shields.io/badge/Spring%20Data%20JPA-6DB33F?logo=spring&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?logo=mysql&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?logo=git&logoColor=white)

### Project Experience

![Redis](https://img.shields.io/badge/Redis-DC382D?logo=redis&logoColor=white)
![Apache Kafka](https://img.shields.io/badge/Apache%20Kafka-231F20?logo=apachekafka&logoColor=white)
![Spring Batch](https://img.shields.io/badge/Spring%20Batch-6DB33F?logo=spring&logoColor=white)
![JUnit5](https://img.shields.io/badge/JUnit5-25A162?logo=junit5&logoColor=white)
![Mockito](https://img.shields.io/badge/Mockito-78A641)
![k6](https://img.shields.io/badge/k6-7D64FF?logo=k6&logoColor=white)
![AWS EC2](https://img.shields.io/badge/AWS%20EC2-232F3E?logo=amazonec2&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)

---

## Projects

### [PetCoupon](https://github.com/PetCare-Platform/petcoupon-backend)
선착순 쿠폰 발급 시스템

- Java, Spring Boot, Redis, Kafka
- 이벤트/쿠폰 관리, 관리자 인증, 상태 스케줄러, SSE 담당
- 20,000건 동시 요청에서 초과·중복 발급 0건 검증
- GitHub Actions와 AWS EC2 기반 CI/CD 경험

### [Vector DB Test](https://github.com/ureca-final-project-temp/UBot-VertorDBTest)

FAQ RAG에 사용할 Vector DB 선정을 위한 비교 테스트

* pgvector, Qdrant, Weaviate, Milvus, OpenSearch 비교
* 동일한 BGE-M3 임베딩과 Recall@10 기준으로 p95 latency, QPS, 자원 사용량 측정
* 14개 DB / engine / index 조합을 반복 실행해 성능과 안정성 검증
* 실제 서비스 규모보다 큰 10,000개 벡터 조건에서 후보별 특성 비교

### [Planly](https://github.com/Catverdose/planly-web)
Todo와 공유 캘린더를 연결한 일정 관리 서비스

- Java, Spring Boot, JPA, MySQL, React
- JWT 인증/인가와 Todo-Schedule 연동 구현
- 기존 팀 프로젝트를 기능 확장 및 구조 개선

### [Java Web Fundamentals](https://github.com/Catverdose/memo-servlet-jsp)
Servlet, JSP, JDBC 기반 Java 웹 학습 프로젝트

- Servlet → Service → DAO → JDBC 흐름 직접 구현
- Spring 이전의 Java 웹 요청 및 데이터 처리 구조 학습
→ JDBC 기반 MySQL 연동
→ JSP 기반 View 구성
→ DB 접속 설정 외부화
