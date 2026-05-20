# 🚀 프로젝트 이름

> 한 줄로 프로젝트를 설명하는 문장을 작성합니다.  
> 예) 사용자 간 중고 거래를 위한 RESTful API 서버

---

## 1. 📌 프로젝트 소개

### 기획 배경 및 목적
- 어떤 **문제**를 해결하기 위해 만들었는지
- 프로젝트의 **핵심 가치**와 차별점
- 개발 기간 : `2026.05.01 ~ 2026.06.30` (약 2개월)
- 팀 구성 : 백엔드 2명 / 프론트엔드 1명 (또는 개인 프로젝트)

### 주요 기능
| 기능 | 설명 |
|------|------|
| 🔐 회원가입 / 로그인 | JWT 기반 인증 · 소셜 로그인(OAuth2) |
| 📝 게시글 CRUD | 이미지 첨부, 페이징, 검색 |
| 💬 실시간 채팅 | WebSocket + STOMP |
| 📊 관리자 대시보드 | 통계, 사용자 관리 |

---

## 2. 🛠️ 기술 스택

| 분류 | 기술 |
|------|------|
| **Language** | Java 17 |
| **Framework** | Spring Boot 3.x, Spring Security, Spring Data JPA |
| **Database** | MySQL 8.0 / Redis (캐싱) |
| **Infra** | Docker, Docker Compose, GitHub Actions (CI/CD) |
| **API 문서** | Swagger (SpringDoc OpenAPI 3) |
| **기타** | Lombok, MapStruct, JUnit 5, Mockito |

---

## 3. 🏗️ 시스템 아키텍처

```
[Client]  ──▶  [Nginx]  ──▶  [Spring Boot App]  ──▶  [MySQL]
                                     │
                                     ├──▶  [Redis] (캐시/세션)
                                     └──▶  [S3]    (파일 저장)
```

### 프로젝트 구조
```
src/
├── main/
│   ├── java/com/example/project/
│   │   ├── domain/          # 도메인별 패키지
│   │   │   ├── user/
│   │   │   │   ├── controller/
│   │   │   │   ├── service/
│   │   │   │   ├── repository/
│   │   │   │   ├── dto/
│   │   │   │   └── entity/
│   │   │   └── post/
│   │   ├── global/          # 공통 설정
│   │   │   ├── config/
│   │   │   ├── exception/
│   │   │   └── security/
│   │   └── ProjectApplication.java
│   └── resources/
│       ├── application.yml
│       └── application-prod.yml
└── test/
```

---

## 4. 📡 API 설계

### 인증 API
| Method | Endpoint | 설명 | 인증 |
|--------|----------|------|------|
| `POST` | `/api/auth/signup` | 회원가입 | ✕ |
| `POST` | `/api/auth/login` | 로그인 (JWT 발급) | ✕ |
| `POST` | `/api/auth/reissue` | 토큰 재발급 | ✔ |

### 게시글 API
| Method | Endpoint | 설명 | 인증 |
|--------|----------|------|------|
| `GET` | `/api/posts` | 게시글 목록 조회 | ✕ |
| `GET` | `/api/posts/{id}` | 게시글 상세 조회 | ✕ |
| `POST` | `/api/posts` | 게시글 작성 | ✔ |
| `PUT` | `/api/posts/{id}` | 게시글 수정 | ✔ |
| `DELETE` | `/api/posts/{id}` | 게시글 삭제 | ✔ |

> 📄 전체 API 문서는 서버 실행 후 [`http://localhost:8080/swagger-ui.html`](http://localhost:8080/swagger-ui.html) 에서 확인할 수 있습니다.

---

## 5. 🗄️ ERD (데이터베이스 설계)

```
┌──────────┐       ┌──────────┐       ┌──────────┐
│  users   │       │  posts   │       │ comments │
├──────────┤       ├──────────┤       ├──────────┤
│ id (PK)  │◄──┐   │ id (PK)  │◄──┐   │ id (PK)  │
│ email    │   └──│ user_id  │   └──│ post_id  │
│ password │       │ title    │       │ user_id  │
│ nickname │       │ content  │       │ content  │
│ role     │       │ created  │       │ created  │
└──────────┘       └──────────┘       └──────────┘
```

> 💡 실제 프로젝트에서는 [dbdiagram.io](https://dbdiagram.io) 등의 도구로 ERD 이미지를 생성하여 첨부하세요.

---

## 6. 🚀 실행 방법

### 사전 요구사항
- Java 17+
- Docker & Docker Compose
- MySQL 8.0 (또는 Docker로 실행)

### 로컬 실행
```bash
# 1. 레포지토리 클론
git clone https://github.com/your-username/your-project.git
cd your-project

# 2. 환경 변수 설정
cp .env.example .env
# .env 파일에서 DB 접속 정보 수정

# 3. Docker로 DB 실행
docker compose up -d db

# 4. 애플리케이션 빌드 및 실행
./gradlew clean build
./gradlew bootRun
```

### Docker로 전체 실행
```bash
docker compose up -d
```

> 서버가 정상적으로 실행되면 `http://localhost:8080` 에서 접속 가능합니다.

---

## 📎 기타

### 트러블 슈팅
프로젝트 진행 중 겪었던 문제와 해결 과정을 기록합니다.

| 문제 | 원인 | 해결 |
|------|------|------|
| N+1 쿼리 발생 | Lazy Loading 시 연관 엔티티 조회 | `@EntityGraph` + Fetch Join 적용 |
| JWT 만료 시 에러 | Refresh Token 미구현 | Redis 기반 Refresh Token 로테이션 적용 |
| 동시성 이슈 | 좋아요 수 갱신 시 Race Condition | 비관적 락(`@Lock`) 적용 |

### 배운 점 / 회고
- 프로젝트를 통해 학습한 내용이나 개선하고 싶은 부분을 작성합니다.
- 블로그 링크 등을 첨부하면 더 좋습니다.

---

## 📜 라이선스
이 프로젝트는 MIT 라이선스를 따릅니다.
