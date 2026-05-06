# [프로젝트 기획서] Lablion: 개발자 커리어 아카이브 플랫폼 및 홈랩 인프라 구축

**작성일:** 2026. 05. 06  
**프로젝트명:** Lablion (랩라이언)  
**분야:** Full-Stack Development / DevOps / AI Serving

---

## 1. 프로젝트 개요 (Project Overview)
본 프로젝트는 개발자의 모든 학습 활동 및 프로젝트 수행 이력을 체계적으로 기록하고 관리하는 커리어 아카이브 플랫폼 Lablion 을 구축하는 것을 목적으로 함. 상용 클라우드 서비스에 의존하지 않고 **라즈베리파이(Raspberry Pi) 기반의 프라이빗 홈랩**을 직접 설계·운용함으로써, 인프라 제어권 확보 및 서비스 전 주기(Life-cycle)에 대한 엔지니어링 역량 강화를 지향함.

---

## 2. 프로젝트 목표 (Project Goals)

### 2.1. 단기 목표 (Phase 1: MVP 구현 및 인프라 안정화)
*   **AI 에이전트 주도 프론트엔드 개발:** AI를 적극 활용하여 React 기반의 모던하고 직관적인 UI/UX 구현
*   **아카이빙 코어 시스템 구축:** 가중치(중요도/난이도) 기반의 활동 기록 및 KPT 회고, 트러블슈팅 데이터 구조화.
*   **프라이빗 클라우드 환경 조성:** 라즈베리파이 5 내 Docker 컨테이너 오케스트레이션을 통한 서비스 독립성 확보.
*   **표준 계층형 아키텍처 적용:** Spring Boot 기반의 안정적인 REST API 설계 및 MariaDB 데이터 정규화.
*   **네트워크 라우팅 최적화:** Nginx 리버스 프록시를 활용한 보안성 강화 및 트래픽 게이트웨이 구축.

### 2.2. 확장 및 고도화 목표 (Phase 2: 지능화 및 운영 효율화)
*   **대규모 트래픽 방어막 구축:** Redis(인메모리 캐시)를 도입하여 DB 부하 감소 및 응답 속도 극대화, 필요시 메시지 큐(Kafka/RabbitMQ) 도입
*   **데이터 자동화 및 통합:** GitHub REST API 연동을 통한 리포지토리 메타데이터(README, 기술 스택 등) 자동 크롤링 및 동기화.
*   **AI 기반 분석 엔진 탑재:** FastAPI(Python) 기반 AI 서버를 구축하여 프로젝트 요약, 성과 수치화, 포트폴리오 키워드 자동 추출.
*   **인프라 가용성 확장:** Redis 도입을 통한 데이터 캐싱 및 K3s(Lightweight Kubernetes) 기반의 컨테이너 오토스케일링 검토.
*   **CI/CD 파이프라인 구축:** GitHub Actions와 연동한 배포 자동화를 구현하여 수정 사항의 즉각적인 운영 환경 반영.

---

## 3. 시스템 아키텍처 및 상세 기술 스택

### 3.1. 기술 스택 (Tech Stack)
*   **Frontend:** React, Vite, TailwindCSS (Component-based UI)
*   **Backend:** Java 17, Spring Boot 3.x, Spring Data JPA
*   **Database:** MariaDB (Relational Data Management)
*   **AI Serving:** Python, FastAPI, LangChain (Project Analysis)
*   **Infrastructure:** Raspberry Pi 5, Docker, Docker Compose, Nginx

### 3.2. 데이터 모델링 방향 (Entity Relationship)
*   **User:** 회원 관리 및 인증 정보 저장.
*   **Project:** 활동의 본체가 되는 테이블로 시작/종료일, 가중치 정보를 포함.
*   **Retrospective (KPT):** 프로젝트와 1:1 또는 1:N 매핑되는 회고 데이터.
*   **Troubleshooting:** 독립적인 에러 해결 사례를 구조화하여 저장.

---

## 4. 수행 로드맵 (Implementation Roadmap)

| 단계 | 구분 | 주요 과업 |
| :--- | :--- | :--- |
| **Stage 1** | **인프라 셋업** | 라즈베리파이 보안 설정, Docker Engine 및 Docker Compose 설치 |
| **Stage 2** | **데이터 설계** | MariaDB 스키마 정의 및 JPA Entity 매핑 (User, Project 중심) |
| **Stage 3** | **API 서버 개발** | 회원가입/로그인 및 프로젝트 CRUD 비즈니스 로직 구현 |
| **Stage 4** | **웹 인터페이스** | React 기반 대시보드 및 가중치 기반 정렬 기능 UI 개발 |
| **Stage 5** | **시스템 통합** | Nginx-App-DB 컨테이너 연동 및 홈랩 실환경 배포 테스트 |
| **Stage 6** | **AI/자동화 확장** | GitHub 연동 모듈 개발 및 AI 분석 엔진 서버 통합 (Phase 2) |

---

## 5. 핵심 용어 정의 (Glossary)

*   **홈랩 (Home Lab):** 개인 주거 공간 내에 서버 장비를 구축하여 학습 및 테스트 목적으로 운영하는 프라이빗 클라우드 환경.
*   **가상화 및 컨테이너 (Virtualization & Container):** OS 수준의 격리를 통해 애플리케이션을 실행 환경에 구애받지 않고 구동하게 하는 기술 (Docker).
*   **KPT 회고:** Keep(강점), Problem(약점), Try(개선점)의 약자로, 프로젝트 종료 후 성과를 분석하는 정형화된 회고 기법.
*   **CI/CD:** 지속적 통합(Continuous Integration)과 지속적 배포(Continuous Deployment)를 의미하며, 개발부터 운영 배포까지의 과정을 자동화하는 프로세스.
