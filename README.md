# Tickatch Config Repository

Tickatch MSA의 **중앙 설정 저장소**입니다.

---

## 개요

Config Server가 이 저장소에서 설정 파일을 읽어 각 마이크로서비스에 제공합니다.

```
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│   config-repo   │ ───► │  Config Server  │ ───► │  Microservices  │
│    (GitHub)     │      │     :3100       │      │                 │
└─────────────────┘      └─────────────────┘      └─────────────────┘
```

---

## 디렉토리 구조

```
config-repo/
├── configs/
│   ├── common/
│   │   └── application.yml              # 모든 서비스 공통 설정
│   │
│   ├── gateway-server/
│   │   ├── application.yml              # 라우팅 + Swagger 통합 설정
│   │   └── application-dev.yml          # 개발 환경 (로그 레벨)
│   │
│   ├── auth-service/
│   │   └── application.yml              # JWT, OAuth 설정
│   │
│   ├── user-service/
│   │   └── application.yml              # 사용자 서비스 설정
│   │
│   ├── product-service/
│   │   └── application.yml              # 상품 서비스 설정
│   │
│   ├── reservation-service/
│   │   └── application.yml              # 예약 서비스 설정
│   │
│   ├── reservation-seat-service/
│   │   └── application.yml              # 좌석 예약 서비스 설정
│   │
│   ├── notification-service/
│   │   └── application.yml              # 알림 서비스 설정
│   │
│   └── notification-sender-service/
│       └── application.yml              # 알림 발송 서비스 설정
│
├── .gitignore
└── README.md
```

---

## 서비스 목록

| 서비스 | 설명 | 주요 설정 |
|--------|------|----------|
| **gateway-server** | API Gateway | 라우팅, CORS, JWT 검증, Swagger 통합 |
| **auth-service** | 인증 서비스 | JWT (RS256), OAuth 프로바이더 |
| **user-service** | 사용자 서비스 | RabbitMQ Exchange |
| **product-service** | 상품 서비스 | RabbitMQ Exchange |
| **reservation-service** | 예약 서비스 | RabbitMQ Exchange |
| **reservation-seat-service** | 좌석 예약 서비스 | RabbitMQ Exchange |
| **notification-service** | 알림 서비스 | RabbitMQ Exchange (email, sms, mms, slack) |
| **notification-sender-service** | 알림 발송 서비스 | RabbitMQ Exchange |

---

## 설정 우선순위

Config Server는 다음 순서로 설정을 로드하며, 뒤에 있을수록 우선순위가 높습니다.

| 순서 | 경로 | 설명 |
|------|------|------|
| 1 | `configs/common/application.yml` | 공통 설정 |
| 2 | `configs/{application}/application.yml` | 서비스별 기본 설정 |
| 3 | `configs/{application}/application-{profile}.yml` | 서비스별 환경 설정 |

---

## 공통 설정 (common/application.yml)

모든 서비스에 적용되는 설정:

| 카테고리 | 설정 내용 |
|----------|----------|
| **Database** | PostgreSQL (HikariCP 커넥션 풀) |
| **JPA/Hibernate** | PostgreSQL Dialect, 배치 처리 |
| **Kafka** | 메시지 브로커 (Producer/Consumer) |
| **RabbitMQ** | 메시지 큐 (재시도 정책 포함) |
| **Actuator** | health, metrics, prometheus, refresh |
| **Tracing** | Zipkin 분산 추적 |
| **Resilience4j** | Circuit Breaker, Retry, Bulkhead, Rate Limiter |
| **SpringDoc** | OpenAPI/Swagger 문서화 |
| **Logging** | 로그 패턴 (TraceId, SpanId 포함) |

---

## 환경 변수 (민감 정보)

> ⚠️ **중요**: 아래 값들은 설정 파일에 직접 작성하지 않고, 환경 변수로 주입합니다.

### Database

| 변수 | 설명 |
|------|------|
| `DB_HOST` | PostgreSQL 호스트 |
| `DB_PORT` | PostgreSQL 포트 |
| `DB_NAME` | 데이터베이스 이름 |
| `DB_USERNAME` | DB 사용자명 |
| `DB_PASSWORD` | DB 비밀번호 |

### Messaging

| 변수 | 설명 |
|------|------|
| `KAFKA_BOOTSTRAP_SERVERS` | Kafka 브로커 주소 |
| `RABBITMQ_HOST` | RabbitMQ 호스트 |
| `RABBITMQ_PORT` | RabbitMQ 포트 |
| `RABBITMQ_USERNAME` | RabbitMQ 사용자명 |
| `RABBITMQ_PASSWORD` | RabbitMQ 비밀번호 |
| `RABBITMQ_VHOST` | RabbitMQ Virtual Host |

### Monitoring

| 변수 | 설명 |
|------|------|
| `ZIPKIN_ENDPOINT` | Zipkin 엔드포인트 |
| `PUSHGATEWAY_ENDPOINT` | Prometheus Pushgateway 주소 |

### Auth Service 전용

| 변수 | 설명 |
|------|------|
| `JWT_ISSUER` | JWT 발급자 |
| `JWT_KEY_ID` | JWT 키 ID |
| `JWT_PRIVATE_KEY` | RS256 개인키 (Base64) |
| `JWT_PUBLIC_KEY` | RS256 공개키 (Base64) |
| `JWT_JWKS_URI` | JWKS 엔드포인트 |

### OAuth 프로바이더

| 변수 | 설명 |
|------|------|
| `OAUTH_FRONTEND_REDIRECT_URL` | 프론트엔드 리다이렉트 URL |
| `OAUTH_BASE_REDIRECT_URI` | OAuth 콜백 기본 URI |
| `OAUTH_KAKAO_CLIENT_ID` | 카카오 Client ID |
| `OAUTH_KAKAO_CLIENT_SECRET` | 카카오 Client Secret |
| `OAUTH_NAVER_CLIENT_ID` | 네이버 Client ID |
| `OAUTH_NAVER_CLIENT_SECRET` | 네이버 Client Secret |
| `OAUTH_GOOGLE_CLIENT_ID` | 구글 Client ID |
| `OAUTH_GOOGLE_CLIENT_SECRET` | 구글 Client Secret |

---

## Gateway 라우팅

| 서비스 | 경로 | 대상 |
|--------|------|------|
| Auth | `/api/v1/auth/**` | AUTH-SERVICE |
| User | `/api/v1/user/**` | USER-SERVICE |
| Product | `/api/v1/products/**` | PRODUCT-SERVICE |
| Payment | `/api/v1/payments/**` | PAYMENT-SERVICE |
| Reservation | `/api/v1/reservations/**` | RESERVATION-SERVICE |
| Reservation Seat | `/api/v1/reservation-seats/**` | RESERVATION-SEAT-SERVICE |
| Art Hall | `/api/v1/arthalls/**` | ARTHALL-SERVICE |
| Ticket | `/api/v1/tickets/**` | TICKET-SERVICE |

### Swagger UI 통합
- 접속: `http://{gateway-host}:{port}/swagger-ui.html`
- 각 서비스의 API 문서를 Gateway에서 통합 조회 가능

---

## 환경별 프로파일

| 프로파일 | 용도 | 로그 레벨 |
|----------|------|----------|
| `local` | 로컬 개발 | DEBUG |
| `dev` | 개발 서버 | INFO |
| `prod` | 운영 서버 | WARN |

### 사용 방법

```bash
# 로컬 환경으로 실행
java -jar app.jar --spring.profiles.active=local

# 개발 환경으로 실행
java -jar app.jar --spring.profiles.active=dev

# 운영 환경으로 실행
java -jar app.jar --spring.profiles.active=prod
```

---

## 메시징 (RabbitMQ Exchange)

| Exchange | 사용 서비스 |
|----------|------------|
| `tickatch.user` | auth-service, user-service |
| `tickatch.product` | product-service, reservation-service, reservation-seat-service |
| `tickatch.arthall` | product-service |
| `tickatch.reservation` | notification-service |
| `tickatch.reservation-seat` | product-service |
| `tickatch.ticket` | notification-service |
| `tickatch.notification-sender` | notification-service, notification-sender-service |
| `tickatch.email` | notification-service, notification-sender-service |
| `tickatch.sms` | notification-service, notification-sender-service |
| `tickatch.mms` | notification-service, notification-sender-service |
| `tickatch.slack` | notification-service, notification-sender-service |

---

## 설정 갱신

설정 변경 후 서비스 재시작 없이 적용하려면:

```bash
# 특정 서비스 설정 갱신
curl -X POST http://{service-host}:{port}/actuator/refresh

# 전체 서비스 설정 갱신 (Spring Cloud Bus)
curl -X POST http://config-server:3100/actuator/busrefresh
```

---

## 모니터링 엔드포인트

모든 서비스에서 사용 가능한 Actuator 엔드포인트:

| 엔드포인트 | 설명 |
|-----------|------|
| `/actuator/health` | 서비스 상태 확인 |
| `/actuator/info` | 서비스 정보 |
| `/actuator/metrics` | 메트릭 정보 |
| `/actuator/prometheus` | Prometheus 메트릭 |
| `/actuator/refresh` | 설정 갱신 |

---

## 설정 파일 작성 가이드

### 민감 정보 처리

```yaml
# ❌ 잘못된 예시 - 절대 하지 마세요!
spring:
  datasource:
    password: my-secret-password

# ✅ 올바른 예시 - 환경 변수 사용
spring:
  datasource:
    password: ${DB_PASSWORD}
```

### 환경별 기본값 설정

```yaml
spring:
  datasource:
    url: jdbc:postgresql://${DB_HOST:localhost}:${DB_PORT:5432}/${DB_NAME:tickatch}
```

---

## 주의사항

- ⚠️ **민감 정보(비밀번호, API 키, 시크릿)는 절대 커밋하지 마세요**
- 모든 민감 정보는 환경 변수로 주입
- `*-secrets.yml` 파일은 `.gitignore`에 포함
- 설정 변경 시 영향받는 서비스 확인 필요
- PR 리뷰 시 민감 정보 노출 여부 반드시 확인

---

## .gitignore

```gitignore
# 민감 정보 파일
*-secrets.yml
*-secrets.yaml
*.secret
.env
.env.*
!.env.example

# IDE
.idea/
*.iml
.vscode/

# OS
.DS_Store
Thumbs.db
```