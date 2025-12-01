# Tickatch Config Repository

Tickatch MSA의 **중앙 설정 저장소**입니다.

---

## 개요

Config Server가 이 저장소에서 설정 파일을 읽어 각 마이크로서비스에 제공합니다.

```
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│   config-repo   │ ───► │  Config Server  │ ───► │  Microservices  │
│    (GitHub)     │      │     :8888       │      │                 │
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
│   │   ├── application.yml              # 기본 설정 + 라우터
│   │   ├── application-local.yml        # 로컬 환경
│   │   ├── application-dev.yml          # 개발 환경
│   │   └── application-prod.yml         # 운영 환경
│   │
│   ├── account-service/
│   │   └── application.yml
│   │
│   ├── transaction-service/
│   │   └── application.yml
│   │
│   ├── transfer-service/
│   │   └── application.yml
│   │
│   ├── card-service/
│   │   └── application.yml
│   │
│   ├── ledger-service/
│   │   └── application.yml
│   │
│   ├── auth-server/
│   │   └── application.yml
│   │
│   └── user-service/
│       └── application.yml
│
├── .gitignore
└── README.md
```

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

- **Eureka Client**: 서비스 디스커버리 연결
- **Actuator**: 헬스체크, 메트릭 엔드포인트
- **Tracing**: Zipkin 분산 추적
- **Kafka**: 메시지 브로커 연결
- **Logging**: 로그 패턴

---

## 서비스별 포트

| 서비스 | 포트 | DB |
|--------|------|-----|
| gateway-server | 8080, 8081 | - |
| account-service | 8081 | PostgreSQL |
| transaction-service | 8082 | PostgreSQL |
| transfer-service | 8083 | PostgreSQL |
| card-service | 8084 | PostgreSQL |
| ledger-service | 8085 | PostgreSQL |
| auth-server | 8086 | PostgreSQL |
| user-service | 8087 | PostgreSQL |

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

# 운영 환경으로 실행
java -jar app.jar --spring.profiles.active=prod
```

---

## 설정 갱신

설정 변경 후 서비스 재시작 없이 적용하려면:

```bash
# 특정 서비스 설정 갱신
curl -X POST http://{service-host}:{port}/actuator/refresh

# 전체 서비스 설정 갱신 (Spring Cloud Bus 사용 시)
curl -X POST http://config-server:8888/actuator/busrefresh
```

---

## 주의사항

- 민감 정보(비밀번호, API 키)는 환경 변수로 주입
- `*-secrets.yml` 파일은 `.gitignore`에 포함되어 커밋되지 않음
- 설정 변경 시 영향받는 서비스 확인 필요
