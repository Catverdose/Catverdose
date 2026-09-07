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

### 1. [선착순 쿠폰 발급 시스템](https://github.com/PetCare-Platform/petcoupon-backend)

> 대규모 동시 요청 환경에서 데이터 정합성과 안정적인 발급 처리를 검증한 팀 프로젝트

재고 10,000장에 20,000건의 동시 요청이 발생하는 상황을 기준으로  
**초과 발급 0건과 1인 1매 보장**을 핵심 요구사항으로 설정했습니다.

#### Project

- Redis Lua를 통한 원자적 재고 및 중복 신청 판정
- Redis Stream과 Kafka를 활용한 비동기 발급 처리
- Outbox, Retry, DLQ를 활용한 메시지 유실 및 장애 복구 대응
- DB Unique Constraint를 통한 최종 정합성 방어
- Spring Batch 기반 발급 데이터 사후 검증
- k6를 활용한 부하 및 정합성 테스트
- GitHub Actions와 AWS EC2를 활용한 CI/CD 및 배포

#### Result

- 통합 테스트 80개 시나리오 전건 통과
- 20,000건 동시 요청 조건 반복 검증
- 초과 발급 0건
- 중복 발급 0건
- 최대 접수 처리량 1,030 TPS

#### My Contribution

- 이벤트 및 쿠폰 관리 기능
- 관리자 인증 및 권한 처리
- 이벤트 상태 전이 스케줄러
- SSE 기반 실시간 모니터링 기능

기술을 먼저 선택하지 않고 동시성 제어 방법별 성능과 정합성을 직접 비교했습니다.

→ [동시성 전략 비교 실험](https://github.com/PetCare-Platform/coupon-concurrency-experiment)

---

### 2. [Planly](https://github.com/Catverdose/planly-web)

> 개인 Todo와 공유 캘린더를 연결한 일정 관리 웹 서비스

기존 팀 프로젝트를 기반으로 기능을 확장하고 구조를 개선한 후속 프로젝트입니다.

#### Backend

- Java / Spring Boot 기반 REST API
- Spring Security와 JWT 기반 인증 및 인가
- Validation 및 예외 처리
- Spring Data JPA와 MySQL을 활용한 데이터 관리
- Todo와 Schedule 간 연결 및 상태 동기화
- 테스트를 통한 인증과 주요 비즈니스 로직 검증

#### Improvement

- 기존 정적 JavaScript 화면을 React 기반 SPA로 전환
- Todo 상세 정보 및 검색, 필터, 페이지네이션 추가
- Todo와 Schedule 간 상호 생성 및 동기화 기능 구현
- 세부 할 일과 진행률 관리 기능 추가
- 인증 상태 관리와 토큰 재발급 흐름 개선

기존 코드를 그대로 유지하기보다  
기능 간 데이터 관계와 변경 영향을 확인하며 구조를 확장하는 경험을 했습니다.

---

### 3. [Java Web Fundamentals](https://github.com/Catverdose/memo-servlet-jsp)

> Servlet, JSP, JDBC를 사용해 Spring 이전의 Java 웹 동작 구조를 직접 구현하는 학습 프로젝트

프레임워크의 추상화에 의존하기 전에  
HTTP 요청부터 SQL 실행과 응답 반환까지의 흐름을 이해하기 위해 진행하고 있습니다.

```text
Browser
→ Servlet Controller
→ Service
→ DAO
→ JDBC
→ MySQL
→ JSP
→ Servlet 기반 요청 및 응답 처리
→ Controller / Service / DAO 계층 분리
→ JDBC 기반 MySQL 연동
→ JSP 기반 View 구성
→ DB 접속 설정 외부화
```

## What I Focus On

### 문제의 원인을 먼저 찾습니다

오류가 발생한 지점만 수정하기보다  
요청 경로와 데이터 변경 흐름을 따라가며 원인을 좁히려고 합니다.

### 기술 선택에는 근거가 필요하다고 생각합니다

동시성 문제에서는 비관적 락, 낙관적 락, 조건부 UPDATE, Redis, Kafka를 직접 비교하고  
정합성, 처리량, 응답 시간과 운영 복잡도를 함께 고려했습니다.

### 변경의 영향 범위를 확인합니다

기능 하나를 수정할 때 다른 도메인과 데이터에 미치는 영향을 확인하고  
테스트와 문서화를 통해 안정적인 변경을 만드는 것을 중요하게 생각합니다.
