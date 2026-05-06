# 프로젝트 설계서 (v1.1) - Lablion: 커리어 아카이브 플랫폼

**작성일:** 2026. 05. 06  
**프로젝트명:** Lablion (랩라이언)  
**기반 기술:** Spring Boot, MariaDB, Docker, Raspberry Pi 5

---

## 1. 핵심 기능 정의 (Requirements)

본 서비스는 개발자의 활동 이력을 저장하고 관리하며 심화 회고(KPT), 트러블슈팅 내역을 구조화하여 관리합니다.

### A. 사용자 관리 (User Management)
*   **회원가입 및 보안:** 사용자는 아이디, 이메일, 비밀번호를 입력하여 가입하며, 비밀번호는 BCrypt 알고리즘으로 단방향 암호화되어 저장됩니다.
*   **인증 및 데이터 격리:** JWT(JSON Web Token) 기반 인증을 통해 로그인하며, 모든 API는 현재 로그인한 사용자의 데이터에만 접근할 수 있도록 격리됩니다.

### B. 커리어 아카이브 관리 (Archive Management)
*   **프로젝트 CRUD:** 활동명, 설명, 기간, 가중치(중요도/난이도 1~5) 정보를 기록합니다.
*   **가중치 기반 정렬:** 사용자가 설정한 중요도/난이도 수치를 바탕으로 목록을 정렬하여 핵심 프로젝트를 상단에 노출합니다.
*   **심화 기록 연동:** 
    *   **KPT 회고:** 프로젝트별 Keep(강점), Problem(문제점), Try(개선점)를 1:1 관계로 기록합니다.
    *   **트러블슈팅 DB:** 개발 중 발생한 에러 로그와 해결 과정을 1:N 관계로 상세히 저장합니다.

---

## 2. 데이터베이스 모델링 (ERD)

회원(Users)을 중심으로 프로젝트가 생성되며, 각 프로젝트는 회고(KPT)와 문제 해결 기록(Troubleshooting)을 하위 객체로 가집니다.

### Table: users (회원 정보)
| 컬럼명 | 데이터 타입 | 제약 조건 | 설명 |
| :--- | :--- | :--- | :--- |
| `id` | BIGINT | PK, AI | 회원 고유 식별자 |
| `username` | VARCHAR(50) | NOT NULL, UNIQUE | 로그인 아이디 |
| `password` | VARCHAR(255) | NOT NULL | 암호화된 비밀번호 |
| `email` | VARCHAR(100) | NOT NULL, UNIQUE | 사용자 이메일 |
| `created_at` | TIMESTAMP | NOT NULL | 가입 일시 |

### Table: projects (프로젝트 정보)
| 컬럼명 | 데이터 타입 | 제약 조건 | 설명 |
| :--- | :--- | :--- | :--- |
| `id` | BIGINT | PK, AI | 프로젝트 고유 식별자 |
| `user_id` | BIGINT | FK (users.id) | 작성자 고유 식별자 |
| `title` | VARCHAR(100) | NOT NULL | 프로젝트 제목 |
| `description` | TEXT | NULL | 프로젝트 설명 |
| `importance` | INT | NOT NULL (1-5) | 중요도 (정렬용) |
| `difficulty` | INT | NOT NULL (1-5) | 난이도 (정렬용) |
| `start_date` | DATE | NOT NULL | 활동 시작일 |
| `end_date` | DATE | NULL | 활동 종료일 |

### Table: kpts (KPT 회고)
| 컬럼명 | 데이터 타입 | 제약 조건 | 설명 |
| :--- | :--- | :--- | :--- |
| `id` | BIGINT | PK, AI | 회고 고유 식별자 |
| `project_id` | BIGINT | FK (projects.id) | 연결된 프로젝트 ID (1:1) |
| `keep_content` | TEXT | NULL | 유지할 점 |
| `problem_content`| TEXT | NULL | 문제점 |
| `try_content` | TEXT | NULL | 시도할 점 |

### Table: troubleshootings (문제 해결 기록)
| 컬럼명 | 데이터 타입 | 제약 조건 | 설명 |
| :--- | :--- | :--- | :--- |
| `id` | BIGINT | PK, AI | 기록 고유 식별자 |
| `project_id` | BIGINT | FK (projects.id) | 연결된 프로젝트 ID (1:N) |
| `title` | VARCHAR(200) | NOT NULL | 에러 제목/명칭 |
| `error_log` | TEXT | NULL | 실제 발생 에러 로그 |
| `solution` | TEXT | NOT NULL | 해결 방법 및 분석 |

---

## 3. 백엔드 시스템 아키텍처 (Spring Boot)

유지보수와 확장을 위해 역할이 분리된 **계층형 아키텍처(Layered Architecture)**를 유지합니다.

### 패키지 구조 및 역할
```text
com.homelab.todo (또는 lablion)
├── config/             # Spring Security, Swagger, CORS 환경 설정
├── domain/             # JPA Entity 클래스 (User, Project, Kpt, Troubleshooting)
├── repository/         # DB 접근을 위한 JpaRepository 인터페이스
├── service/            # 트랜잭션 처리 및 핵심 비즈니스 로직 (권한 검증 포함)
├── controller/         # API 엔드포인트 정의 및 요청 전달
├── dto/                # 계층 간 데이터 전송 객체 (Request/Response)
└── exception/          # 전역 에러 핸들러 및 커스텀 예외 정의
