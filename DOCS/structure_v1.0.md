# 프로젝트 기획 및 아키텍처 설계서 (v1.0)
**프로젝트명:** 다중 사용자 기반 To-Do List 웹 서비스

---

## 1. 핵심 기능 정의 (Requirements)

본 서비스는 다중 사용자 환경을 지원하며, 데이터 격리 및 보안을 원칙으로 합니다.

### A. 사용자 관리 (User Management)
*   **회원가입:** 사용자는 고유한 아이디, 이메일, 그리고 비밀번호를 입력하여 계정을 생성합니다. 저장 시 비밀번호는 단방향 암호화 처리됩니다.
*   **로그인 및 인증:** 가입된 정보로 로그인을 수행하며, 인증 성공 시 토큰(JWT) 또는 세션을 발급하여 이후 API 요청의 권한 검증 수단으로 사용합니다.

### B. 할 일 관리 (To-Do Management)
*   **생성 (Create):** 인증된 사용자는 새로운 할 일(제목, 상세 내용)을 등록할 수 있습니다.
*   **조회 (Read):** 사용자는 본인의 계정으로 등록된 할 일 목록만 조회할 수 있습니다. 타인의 데이터에는 접근할 수 없습니다.
*   **수정 (Update):** 할 일의 제목, 내용, 그리고 완료 여부(Status)를 변경할 수 있습니다.
*   **삭제 (Delete):** 본인이 등록한 할 일 데이터를 시스템에서 삭제할 수 있습니다.

---

## 2. 데이터베이스 모델링 (ERD)

회원(users)과 할 일(todos)은 1:N (일대다) 관계를 구성합니다.

### Table: users (회원 정보)
| 컬럼명 (Column) | 데이터 타입 | 제약 조건 | 설명 |
| :--- | :--- | :--- | :--- |
| `id` | BIGINT | PK, Auto Increment | 회원 고유 식별자 |
| `username` | VARCHAR(50) | NOT NULL, UNIQUE | 로그인 아이디 |
| `password` | VARCHAR(255) | NOT NULL | 암호화된 비밀번호 |
| `email` | VARCHAR(100) | NOT NULL, UNIQUE | 이메일 주소 |
| `created_at` | TIMESTAMP | NOT NULL | 가입 일시 |

### Table: todos (할 일 정보)
| 컬럼명 (Column) | 데이터 타입 | 제약 조건 | 설명 |
| :--- | :--- | :--- | :--- |
| `id` | BIGINT | PK, Auto Increment | 할 일 고유 식별자 |
| `user_id` | BIGINT | FK (users.id), NOT NULL | 작성자 식별자 (외래 키) |
| `title` | VARCHAR(100) | NOT NULL | 할 일 제목 |
| `description` | TEXT | NULL | 상세 내용 |
| `is_completed`| BOOLEAN | DEFAULT FALSE | 완료 여부 |
| `created_at` | TIMESTAMP | NOT NULL | 생성 일시 |
| `updated_at` | TIMESTAMP | NOT NULL | 수정 일시 |

---

## 3. 백엔드 시스템 아키텍처 (Spring Boot)

본 프로젝트는 유지보수와 확장성을 고려하여 계층형 아키텍처(Layered Architecture) 패턴을 채택합니다.

### 디렉토리 및 패키지 구조
```text
com.homelab.todo
├── config/             # 설정 클래스 (Spring Security, DB 설정, CORS 등)
├── domain/             # 데이터베이스 테이블과 매핑되는 Entity 클래스 (User, Todo)
├── repository/         # 데이터베이스와 직접 통신하는 인터페이스 (JpaRepository)
├── service/            # 핵심 비즈니스 로직 및 트랜잭션을 처리하는 클래스
├── controller/         # 클라이언트(웹)의 HTTP API 요청을 처리하는 계층
├── dto/                # 계층 간 데이터 교환을 위한 객체 (Data Transfer Object)
└── exception/          # 전역 에러 처리 및 사용자 정의 예외 처리 로직
