# Backend Developer

**Java와 Spring을 중심으로 백엔드 개발을 공부하고 있습니다.**

Servlet, JSP, JDBC로 웹 요청과 데이터 접근의 기본 흐름을 익힌 뒤,
Spring Boot와 JPA 기반의 REST API 개발로 역량을 확장했습니다.

프로젝트에서는 인증과 권한 검증, 테스트, 데이터 정합성, 동시성 문제를 다뤘고,
선착순 쿠폰 발급 시스템에서는 Redis와 Kafka, 성능 테스트와 CI/CD 및 AWS 배포까지 경험했습니다.

> 기능을 구현하는 데서 끝내지 않고,
> **문제의 원인을 파악하고 안정적으로 개선할 수 있는** 백엔드 개발자를 목표로 하고 있습니다.

---

## Tech Stack

**Backend**

![Java](https://img.shields.io/badge/Java-007396?logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?logo=springboot&logoColor=white)
![Spring MVC](https://img.shields.io/badge/Spring%20MVC-6DB33F?logo=spring&logoColor=white)
![Spring Data JPA](https://img.shields.io/badge/Spring%20Data%20JPA-6DB33F?logo=spring&logoColor=white)
![Spring Batch](https://img.shields.io/badge/Spring%20Batch-6DB33F?logo=spring&logoColor=white)

**Database**

![MySQL](https://img.shields.io/badge/MySQL-4479A1?logo=mysql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?logo=redis&logoColor=white)

**Messaging**

![Kafka](https://img.shields.io/badge/Apache%20Kafka-231F20?logo=apachekafka&logoColor=white)

**Test**

![JUnit](https://img.shields.io/badge/JUnit5-25A162?logo=junit5&logoColor=white)
![Mockito](https://img.shields.io/badge/Mockito-78A641)
![MockMvc](https://img.shields.io/badge/MockMvc-6DB33F?logo=spring&logoColor=white)
![k6](https://img.shields.io/badge/k6-7D64FF?logo=k6&logoColor=white)

**Infra & DevOps**

![AWS EC2](https://img.shields.io/badge/AWS%20EC2-232F3E?logo=amazonec2&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?logo=git&logoColor=white)

---

## Projects

### [선착순 쿠폰 발급 시스템](https://github.com/PetCare-Platform/petcoupon-backend)

> 동시 요청 환경에서 데이터 정합성을 보장하는 쿠폰 발급 백엔드

재고 10,000장에 20,000명이 동시에 몰려도 **초과 발급 0건, 1인 1매**를 보장하는 것이 목표였습니다.
재고 판정은 Redis Lua로 원자적으로 끝내고, DB 저장은 요청 밖에서 처리하도록 분리했습니다.

- **동시성 제어** — Redis Lua 원자 판정, DB Unique 제약을 최종 방어선으로 사용
- **비동기 발급 파이프라인** — Redis Stream → Outbox → Kafka → MySQL 확정
- **장애 복구** — Pending 회수, Outbox 재시도, DLQ 재처리 및 재고 보상
- **정합성 검증** — Spring Batch 기반 사후 전수 검사
- **성능 테스트** — k6 기반 부하 시나리오와 SQL 후검증
- **CI/CD** — GitHub Actions로 빌드·테스트 후 EC2 배포와 헬스 체크까지 자동화

담당: 이벤트·쿠폰 관리, 관리자 인증, 상태 전이 스케줄러, 실시간 모니터링(SSE)

기술 선택 근거는 [동시성 전략 비교 실험](https://github.com/PetCare-Platform/coupon-concurrency-experiment)에 정리했습니다.

### Spring Boot REST API

> 인증과 권한 검증, 예외 처리와 테스트를 적용한 REST API

- JWT 기반 인증
- 리소스 소유권 검증
- Validation 및 공통 예외 처리
- JUnit, Mockito, MockMvc 기반 테스트

### Java Web Fundamentals

> Servlet, JSP, JDBC를 사용해 웹 요청부터 데이터 저장까지 직접 구현한 프로젝트

- Servlet 요청 및 응답 처리
- JSP 기반 화면 구성
- JDBC 기반 데이터 접근
- Spring 사용 이전의 웹 애플리케이션 동작 구조 학습
