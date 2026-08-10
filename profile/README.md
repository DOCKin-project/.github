


<div align="center">

<img src="https://github.com/DOCKin-project.png" width="120" style="border-radius: 50%;" alt="DOCKin Logo" />

# DOCKin
**조선소 현장용 AI 안전/업무 보조 앱** *2025 K-조선 해커톤 프로젝트 대상 (산업통상자원부 주최)*

<br />

[**App Repo »**](https://github.com/DOCKin-project/DOCKin-app) &nbsp; | &nbsp;
[**Backend Repo »**](https://github.com/DOCKin-project/DOCKin-backend) &nbsp; | &nbsp; 
[**AI Server Repo »**](https://github.com/DOCKin-project/DOCKin-aiserver)

</div>

## 📑 목차
1. [🔍 프로젝트 개요](#-프로젝트-개요)
2. [🏗️ 전체 개발 구조](#️-전체-개발-구조)
3. [📂 Directory Structure](#-directory-structure)
4. [📂 API Documentation](#-api-documentation)
5. [📂 Database](#-database)
6. [🎥 시연 영상](#-시연-영상)
7. [✨ 핵심 기능](#-핵심-기능)
8. [🛠️ 기술 스택](#️-기술-스택)
9. [💻 주요 구현 코드](#-주요-구현-코드)
10. [👥 팀원 소개](#-팀원-소개)


--- 
### 개발 기간 
- 기획 기간(예선): 2025.09 ~ 2025.10
- 본선 개발 기간: 2025.10 ~ 2025.11
- 리팩토링 기간 : 2026. 01 ~ ing
---

## 🔍 프로젝트 개요 
DOCKin은 조선소 근로자를 위한 AI 음성 인식, 다국어 번역, 안전·근태 관리를 통합한 모바일 앱입니다.  
실제 조선 현장에서 자주 발생하는 문제(언어 장벽, 보고 누락, 위치 혼선)를 기술로 해결하는 것을 목표로 합니다.

---

## 🏗️ 전체 개발 구조
![](../image/architecture1.jpg)



## 📂 Directory Structure 

<details>
<summary><b>📂 백엔드 상세 폴더 구조 보기 (Project Structure)</b></summary>
<br />

```bash
[ DOCKin-spring ]
├── .github/workflows       # CI/CD 자동화 (GitHub Actions)
├── nginx/conf.d            # Nginx 리버스 프록시 및 서버 설정
├── src/main/java/com/DOCKin
│   ├── absence             # 휴가 신청/승인 및 잔여 일수 관리
│   ├── ai                  # AI 연동 모듈 (FastAPI, STT, 번역 연동)
│   │   ├── controller      # AI 기능 API 엔드포인트
│   │   ├── dto             # 데이터 전송 객체
│   │   ├── model           # AI 히스토리 및 로그 엔티티
│   │   └── service         # FastAPI 서비스 연동 로직
│   ├── attendance          # 근태 관리 시스템 (출퇴근 기록, 근무일 캘린더)
│   ├── chat                # 실시간 채팅 (WebSocket/STOMP 기반)
│   ├── checklist           # 작업 전 점검 체크리스트
│   ├── member              # 회원 및 인증 시스템 (JWT/Security)
│   ├── global              # 글로벌 설정 (Security, WebSocket, Error Handling)
│   │   ├── config          # 주요 Bean 및 프로토콜 설정
│   │   ├── security        # JWT 기반 인증/인가 로직
│   │   └── util            # 오디오 컨버터 등 공통 유틸리티
│   ├── rag                 # 벡터 검색 (청킹 → 임베딩 → pgvector 검색)
│   │   ├── chunking        # 문서 청킹 전략
│   │   ├── model           # document_chunks 엔티티
│   │   ├── repository      # 벡터 유사도 검색 쿼리
│   │   └── service         # 색인 · 검색 · 챗봇 근거 주입
│   ├── safetyCourse        # 안전 교육 관리 시스템
│   └── worklog             # 작업 일지 및 코멘트 시스템
├── src/main/resources
│   ├── application.properties  # 앱 환경 설정
│   └── db/migration            # Flyway 마이그레이션 (스키마 전체)
├── compose.yaml            # Docker 인프라 구성
└── Dockerfile              # 백엔드 컨테이너 빌드 설정

```
</details>



## 📂 API Documentation 

<details>
<summary><b>📂 상세 API 엔드포인트 보기 (Endpoints Specification)</b></summary>
<br />

### 👤 인증 및 계정 (Auth)
| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `POST` | `/member/signup` | 회원가입 |
| `POST` | `/member/login` | 로그인 및 JWT 토큰 발급 |
| `POST` | `/member/logout` | 로그아웃 (토큰 무효화) |
| `DELETE` | `/member/{userId}` | 회원 탈퇴 |

### 🤖 AI 및 스마트 연동 (AI / STT)
| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `POST` | `/api/work-logs/stt` | **음성 파일 기반 작업일지 생성 (STT)** |
| `POST` | `/api/ai/rt-translate` | STT 실시간 번역 연동 |
| `POST` | `/api/ai/translate/{logId}` | 작업일지 다국어 번역 요청 |
| `POST` | `/api/ai/chatbot` | 현장 안전 가이드 챗봇 |

### 📝 작업일지 및 댓글 (Work Logs & Comments)
| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/api/work-logs` | 전체 작업일지 목록 조회 (Paging) |
| `POST` | `/api/work-logs` | 일반 텍스트 기반 일지 생성 |
| `GET` | `/api/work-logs/search` | 키워드 활용 게시물 검색 |
| `GET` | `/api/work-logs/others/{targetUserId}` | 특정 근로자의 작업일지 조회 |
| `POST` | `/api/work-logs/{logId}/comments` | 관리자 피드백(댓글) 작성 |
| `PUT` | `/api/work-logs/{logId}` | 일지 내용 및 이미지 수정 |
| `DELETE` | `/api/work-logs/{logId}` | 작업일지 삭제 |

### 💬 실시간 소통 (Chat)
| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `POST` | `/api/chat/room` | 협업 채팅방 신규 생성 |
| `GET` | `/api/chat/rooms` | 참여 중인 모든 채팅방 목록 |
| `GET` | `/api/chat/room/{roomId}/messages` | 채팅 내역 조회 (무한 스크롤) |
| `GET` | `/api/chat/room/{roomId}/messages/search` | 채팅 내역 키워드 검색 |
| `DELETE` | `/api/chat/room/leave/{roomId}` | 채팅방 나가기 |

### ⏰ 근태 및 안전 교육 (Attendance & Safety)
| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `POST` | `/api/attendance/in` | 출근 기록 등록 (위치/시간) |
| `POST` | `/api/attendance/out` | 퇴근 기록 등록 |
| `GET` | `/api/attendance` | 본인 근태 기록 조회 |
| `GET` | `/api/safety/user/training/uncompleted` | 미이수 안전 교육 목록 확인 |
| `PATCH` | `/api/safety/user/training/complete` | 교육 영상 이수 완료 처리 |
| `POST` | `/api/safety/admin/courses` | 안전 교육 과정 등록 |
| `GET` | `/api/safety/admin/courses/user/{userId}` | 근로자별 이수 현황 조회 |

### 🏖️ 휴가 신청 및 승인 (Absence)
| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `POST` | `/api/absence/requests` | 휴가 신청 (증빙 파일 첨부) |
| `GET` | `/api/absence/requests` | 본인 휴가 신청 내역 조회 |
| `GET` | `/api/absence/admin/requests` | 전체 휴가 신청 목록 (관리자) |
| `PATCH` | `/api/absence/admin/requests/{requestId}/approve` | 휴가 승인 (근태 자동 반영) |
| `PATCH` | `/api/absence/admin/requests/{requestId}/reject` | 휴가 반려 |

### ✅ 작업 전 점검 (Checklist)
| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/api/checklist/user/checklists` | 배정된 체크리스트 목록 조회 |
| `PATCH` | `/api/checklist/user/checklists/{checklistId}/items/{itemId}/check` | 점검 항목 체크 |
| `POST` | `/api/checklist/admin/checklists` | 체크리스트 생성 (관리자) |
| `POST` | `/api/checklist/admin/checklists/{checklistId}/items` | 점검 항목 추가 |
| `PUT` | `/api/checklist/admin/checklists/{checklistId}` | 체크리스트 수정 |
| `DELETE` | `/api/checklist/admin/checklists/{checklistId}` | 체크리스트 삭제 |

</details>

## 📂 Database 

<details>
<summary><b>📂 PostgreSQL 17 + pgvector</b></summary>
<br />

스키마는 **Flyway**(`src/main/resources/db/migration`)가 전부 관리하며,
애플리케이션 기동 시 `ddl-auto=validate`로 엔티티와의 일치를 검증한다.
어긋나면 기동이 실패하므로 스키마 변경은 반드시 마이그레이션을 거친다.

> 아래 ERD와 명세는 **실행 중인 DB에서 생성**한 것이다. 손으로 옮겨 적으면
> 그 순간부터 낡기 시작하므로 그렇게 하지 않았다.
> (전체 24개 테이블 중 Flyway 이력 테이블과 벤치마크용 테이블은 제외)

**`document_chunks`에 외래키가 없는 것은 의도한 설계다.** 벡터 검색은 권한을
`WHERE`에서 미리 거르는 **선필터**로 구현했고, 그러려면 소유자와 공개 범위가
청크 행에 함께 있어야 한다. 그래서 `owner_user_id`·`visibility`를 비정규화해
들고 있다. 후필터(검색 후 걸러내기)는 "내가 못 보는 문서가 존재한다"는 사실이
결과 개수로 새어 나가므로 택하지 않았다.

#### 회원 · 인증

```mermaid
erDiagram
    users ||--o{ authority : "권한"
    users {
        varchar user_id PK
        timestamp created_at
        varchar language_code
        varchar name
        varchar password
        integer remaining_leave_days
        varchar role
        varchar ship_yard_area
        boolean tts_enabled
        varchar work_shift
    }
    authority {
        bigint log_id PK
        smallint authority
        varchar member_id FK
    }
    refresh_token {
        varchar user_id PK
        varchar token
    }
```

<details>
<summary><b>회원 · 인증 — 컬럼 명세</b></summary>


**`users`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `user_id` | varchar(50) | N | PK |  |
| `created_at` | timestamp | N |  |  |
| `language_code` | varchar(255) | N |  |  |
| `name` | varchar(10) | N |  |  |
| `password` | varchar(256) | N |  |  |
| `remaining_leave_days` | integer | N |  |  |
| `role` | varchar(255) | N |  |  |
| `ship_yard_area` | varchar(100) | N |  |  |
| `tts_enabled` | boolean | N |  |  |
| `work_shift` | varchar(255) | Y |  |  |

**`authority`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `log_id` | bigint | N | PK |  |
| `authority` | smallint | Y |  |  |
| `member_id` | varchar(50) | Y | FK | `users.user_id` |

**`refresh_token`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `user_id` | varchar(255) | N | PK |  |
| `token` | varchar(512) | N |  |  |

</details>

#### 근태 · 휴가

```mermaid
erDiagram
    users ||--o{ absence_requests : "신청/결재"
    users ||--o{ attendance : "출퇴근"
    attendance {
        bigint id PK
        timestamp clock_in_time
        timestamp clock_out_time
        varchar in_location
        varchar out_location
        varchar status
        varchar total_work_time
        date work_date
        varchar user_id FK
    }
    work_calendar {
        date calendar_date PK
        varchar day_type
        varchar description
    }
    absence_requests {
        integer request_id PK
        varchar decision_comment
        varchar document_url
        date end_date
        timestamp processed_at
        varchar reason
        timestamp requested_at
        date start_date
        varchar status
        varchar request_type
        varchar user_id FK
        varchar processed_by FK
    }
```

<details>
<summary><b>근태 · 휴가 — 컬럼 명세</b></summary>


**`attendance`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `id` | bigint | N | PK |  |
| `clock_in_time` | timestamp | Y |  |  |
| `clock_out_time` | timestamp | Y |  |  |
| `in_location` | varchar(255) | Y |  |  |
| `out_location` | varchar(255) | Y |  |  |
| `status` | varchar(20) | N |  |  |
| `total_work_time` | varchar(255) | Y |  |  |
| `work_date` | date | N |  |  |
| `user_id` | varchar(50) | N | FK | `users.user_id` |

**`work_calendar`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `calendar_date` | date | N | PK |  |
| `day_type` | varchar(20) | N |  |  |
| `description` | varchar(100) | Y |  |  |

**`absence_requests`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `request_id` | integer | N | PK |  |
| `decision_comment` | varchar(255) | Y |  |  |
| `document_url` | varchar(255) | Y |  |  |
| `end_date` | date | N |  |  |
| `processed_at` | timestamp | Y |  |  |
| `reason` | varchar(255) | N |  |  |
| `requested_at` | timestamp | Y |  |  |
| `start_date` | date | N |  |  |
| `status` | varchar(20) | N |  |  |
| `request_type` | varchar(20) | N |  |  |
| `user_id` | varchar(50) | N | FK | `users.user_id` |
| `processed_by` | varchar(50) | Y | FK | `users.user_id` |

</details>

#### 작업일지 · 설비

```mermaid
erDiagram
    work_logs ||--o{ log_images : "첨부(구)"
    work_logs ||--o{ work_log_comments : "댓글"
    users ||--o{ work_log_comments : "작성자"
    work_logs ||--o{ work_log_images : "첨부"
    work_logs ||--o{ work_log_translations : "번역본"
    equipment ||--o{ work_logs : "대상 설비"
    users ||--o{ work_logs : "작성"
    work_logs {
        bigint log_id PK
        varchar audio_file_url
        timestamp created_at
        text log_text
        varchar title
        timestamp updated_at
        bigint equipment_id FK
        varchar user_id FK
    }
    work_log_translations {
        bigint translation_id PK
        timestamp created_at
        varchar language_code
        text original_text
        text original_title
        varchar trace_id
        text translated_text
        text translated_title
        timestamp updated_at
        varchar user_id
        bigint log_id FK
    }
    work_log_comments {
        bigint comment_id PK
        text content
        timestamp created_at
        timestamp updated_at
        bigint log_id FK
        varchar user_id FK
    }
    work_log_images {
        bigint id PK
        varchar image_url
        bigint work_log_id FK
    }
    log_images {
        bigint imgae_id PK
        varchar image_url
        bigint log_id FK
    }
    equipment {
        bigint equipment_id PK
        varchar name
        varchar nfc_tag
        varchar qr_code
    }
```

<details>
<summary><b>작업일지 · 설비 — 컬럼 명세</b></summary>


**`work_logs`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `log_id` | bigint | N | PK |  |
| `audio_file_url` | varchar(255) | Y |  |  |
| `created_at` | timestamp | N |  |  |
| `log_text` | text | N |  |  |
| `title` | varchar(256) | N |  |  |
| `updated_at` | timestamp | Y |  |  |
| `equipment_id` | bigint | Y | FK | `equipment.equipment_id` |
| `user_id` | varchar(50) | Y | FK | `users.user_id` |

**`work_log_translations`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `translation_id` | bigint | N | PK |  |
| `created_at` | timestamp | Y |  |  |
| `language_code` | varchar(10) | Y |  |  |
| `original_text` | text | Y |  |  |
| `original_title` | text | Y |  |  |
| `trace_id` | varchar(255) | Y |  |  |
| `translated_text` | text | Y |  |  |
| `translated_title` | text | Y |  |  |
| `updated_at` | timestamp | Y |  |  |
| `user_id` | varchar(255) | Y |  |  |
| `log_id` | bigint | Y | FK | `work_logs.log_id` |

**`work_log_comments`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `comment_id` | bigint | N | PK |  |
| `content` | text | N |  |  |
| `created_at` | timestamp | Y |  |  |
| `updated_at` | timestamp | Y |  |  |
| `log_id` | bigint | N | FK | `work_logs.log_id` |
| `user_id` | varchar(50) | N | FK | `users.user_id` |

**`work_log_images`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `id` | bigint | N | PK |  |
| `image_url` | varchar(255) | Y |  |  |
| `work_log_id` | bigint | Y | FK | `work_logs.log_id` |

**`log_images`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `imgae_id` | bigint | N | PK |  |
| `image_url` | varchar(255) | Y |  |  |
| `log_id` | bigint | Y | FK | `work_logs.log_id` |

**`equipment`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `equipment_id` | bigint | N | PK |  |
| `name` | varchar(100) | N |  |  |
| `nfc_tag` | varchar(100) | N |  |  |
| `qr_code` | varchar(100) | N |  |  |

</details>

#### RAG 벡터 검색

```mermaid
erDiagram
    document_chunks {
        bigint chunk_id PK
        integer chunk_index
        text content
        varchar content_hash
        timestamp created_at
        vector embedding
        integer embedding_dim
        varchar embedding_model
        varchar language_code
        varchar owner_user_id
        bigint source_id
        varchar source_type
        timestamp updated_at
        varchar visibility
    }
    chat_history {
        bigint id PK
        timestamp created_at
        text reply
        varchar retrieval_mode
        text source_chunk_ids
        varchar trace_id
        varchar user_id
        text user_query
    }
```

<details>
<summary><b>RAG 벡터 검색 — 컬럼 명세</b></summary>


**`document_chunks`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `chunk_id` | bigint | N | PK |  |
| `chunk_index` | integer | N |  |  |
| `content` | text | N |  |  |
| `content_hash` | varchar(64) | N |  |  |
| `created_at` | timestamp | Y |  |  |
| `embedding` | vector | N |  |  |
| `embedding_dim` | integer | N |  |  |
| `embedding_model` | varchar(64) | N |  |  |
| `language_code` | varchar(10) | Y |  |  |
| `owner_user_id` | varchar(50) | Y |  |  |
| `source_id` | bigint | N |  |  |
| `source_type` | varchar(32) | N |  |  |
| `updated_at` | timestamp | Y |  |  |
| `visibility` | varchar(16) | N |  |  |

**`chat_history`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `id` | bigint | N | PK |  |
| `created_at` | timestamp | Y |  |  |
| `reply` | text | Y |  |  |
| `retrieval_mode` | varchar(16) | Y |  |  |
| `source_chunk_ids` | text | Y |  |  |
| `trace_id` | varchar(255) | Y |  |  |
| `user_id` | varchar(255) | Y |  |  |
| `user_query` | text | Y |  |  |

</details>

#### 실시간 채팅

```mermaid
erDiagram
    chat_rooms ||--o{ chat_members : "참여"
    users ||--o{ chat_members : "참여자"
    chat_rooms ||--o{ chat_messages : "메시지"
    chat_rooms {
        integer room_id PK
        timestamp created_at
        varchar creator_id
        boolean is_group
        timestamp last_message_at
        varchar last_message_content
        varchar room_name
    }
    chat_members {
        integer id PK
        timestamp joined_at
        timestamp last_read_time
        integer room_id FK
        varchar user_id FK
    }
    chat_messages {
        bigint message_id PK
        text content
        varchar file_url
        varchar message_type
        varchar sender_id
        timestamp sent_at
        integer room_id FK
    }
```

<details>
<summary><b>실시간 채팅 — 컬럼 명세</b></summary>


**`chat_rooms`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `room_id` | integer | N | PK |  |
| `created_at` | timestamp | Y |  |  |
| `creator_id` | varchar(255) | Y |  |  |
| `is_group` | boolean | Y |  |  |
| `last_message_at` | timestamp | Y |  |  |
| `last_message_content` | varchar(255) | Y |  |  |
| `room_name` | varchar(255) | Y |  |  |

**`chat_members`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `id` | integer | N | PK |  |
| `joined_at` | timestamp | Y |  |  |
| `last_read_time` | timestamp | Y |  |  |
| `room_id` | integer | N | FK | `chat_rooms.room_id` |
| `user_id` | varchar(50) | N | FK | `users.user_id` |

**`chat_messages`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `message_id` | bigint | N | PK |  |
| `content` | text | N |  |  |
| `file_url` | varchar(255) | Y |  |  |
| `message_type` | varchar(255) | N |  |  |
| `sender_id` | varchar(255) | N |  |  |
| `sent_at` | timestamp | Y |  |  |
| `room_id` | integer | N | FK | `chat_rooms.room_id` |

</details>

#### 안전교육 · 체크리스트

```mermaid
erDiagram
    checklists ||--o{ checklist_items : "항목"
    checklist_items ||--o{ checklist_results : "점검 결과"
    users ||--o{ checklist_results : "점검자"
    equipment ||--o{ checklists : "점검 대상"
    safety_courses ||--o{ safety_enrollments : "과정"
    users ||--o{ safety_enrollments : "수강"
    safety_courses {
        integer course_id PK
        timestamp created_at
        varchar created_by
        text description
        integer duration_minutes
        varchar material_url
        varchar title
        varchar video_url
    }
    safety_enrollments {
        integer enrollment_id PK
        timestamp completion_date
        timestamp enrolled_at
        varchar status
        integer course_id FK
        varchar user_id FK
    }
    checklists {
        integer checklist_id PK
        timestamp created_at
        varchar phase
        varchar title
        timestamp updated_at
        bigint equipment_id FK
    }
    checklist_items {
        integer item_id PK
        varchar content
        integer sequence
        integer checklist_id FK
    }
    checklist_results {
        integer result_id PK
        timestamp checked_at
        boolean is_checked
        integer checklist_item_id FK
        varchar user_id FK
    }
```

<details>
<summary><b>안전교육 · 체크리스트 — 컬럼 명세</b></summary>


**`safety_courses`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `course_id` | integer | N | PK |  |
| `created_at` | timestamp | N |  |  |
| `created_by` | varchar(50) | Y |  |  |
| `description` | text | Y |  |  |
| `duration_minutes` | integer | Y |  |  |
| `material_url` | varchar(255) | Y |  |  |
| `title` | varchar(255) | N |  |  |
| `video_url` | varchar(255) | N |  |  |

**`safety_enrollments`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `enrollment_id` | integer | N | PK |  |
| `completion_date` | timestamp | Y |  |  |
| `enrolled_at` | timestamp | Y |  |  |
| `status` | varchar(255) | N |  |  |
| `course_id` | integer | Y | FK | `safety_courses.course_id` |
| `user_id` | varchar(50) | N | FK | `users.user_id` |

**`checklists`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `checklist_id` | integer | N | PK |  |
| `created_at` | timestamp | Y |  |  |
| `phase` | varchar(10) | N |  |  |
| `title` | varchar(100) | N |  |  |
| `updated_at` | timestamp | Y |  |  |
| `equipment_id` | bigint | N | FK | `equipment.equipment_id` |

**`checklist_items`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `item_id` | integer | N | PK |  |
| `content` | varchar(255) | N |  |  |
| `sequence` | integer | N |  |  |
| `checklist_id` | integer | N | FK | `checklists.checklist_id` |

**`checklist_results`**

| 컬럼 | 타입 | NULL | 키 | 참조 |
| :--- | :--- | :--- | :--- | :--- |
| `result_id` | integer | N | PK |  |
| `checked_at` | timestamp | N |  |  |
| `is_checked` | boolean | N |  |  |
| `checklist_item_id` | integer | N | FK | `checklist_items.item_id` |
| `user_id` | varchar(50) | N | FK | `users.user_id` |

</details>

</details>



---



## 🎥 시연 영상 
<img src="../image/demo.gif" width="100%" alt="DOCKin Service Demo" />

---

## ✨ 핵심 기능
![](../image/feature1.jpg)
![](../image/feature2.jpg)
![](../image/feature3.jpg)
![](../image/feature4.jpg)


---

## 🛠️ 기술 스택 

### 📱 Front End
| 역할 | 종류 |
| :--- | :--- |
| **Framework** | ![React Native](https://img.shields.io/badge/REACT_NATIVE-61DAFB?style=for-the-badge&logo=react&logoColor=black) |
| **State** | ![Zustand](https://img.shields.io/badge/ZUSTAND-443E38?style=for-the-badge&logo=react) ![React Query](https://img.shields.io/badge/REACT_QUERY-FF4154?style=for-the-badge&logo=react-query&logoColor=white) |
| **Language** | ![JavaScript](https://img.shields.io/badge/JAVASCRIPT-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black) |
| **Logic/Form** | ![Axios](https://img.shields.io/badge/AXIOS-5A29E4?style=for-the-badge&logo=axios&logoColor=white) ![React Hook Form](https://img.shields.io/badge/HOOK_FORM-EC5990?style=for-the-badge&logo=reacthookform&logoColor=white) ![Zod](https://img.shields.io/badge/ZOD-3E67B1?style=for-the-badge&logo=zod&logoColor=white) |
| **Deployment** | ![Vercel](https://img.shields.io/badge/VERCEL-000000?style=for-the-badge&logo=vercel&logoColor=white) |

### ⚙️ Back End
| 역할               | 종류                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|:-----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Framework**    | ![Spring Boot](https://img.shields.io/badge/SPRING_BOOT-6DB33F?style=for-the-badge&logo=springboot&logoColor=white)                                                                                                                                                                                                                                                                                                                       |
| **Language**     | ![Java](https://img.shields.io/badge/JAVA-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)                                                                                                                                                                                                                                                                                                                                        |
| **Database/ORM** | ![PostgreSQL](https://img.shields.io/badge/POSTGRESQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white) ![pgvector](https://img.shields.io/badge/PGVECTOR-4169E1?style=for-the-badge&logo=postgresql&logoColor=white) ![JPA](https://img.shields.io/badge/JPA-6DB33F?style=for-the-badge&logo=hibernate&logoColor=white) ![Flyway](https://img.shields.io/badge/FLYWAY-CC0200?style=for-the-badge&logo=flyway&logoColor=white) |
| **Security**     | ![JWT](https://img.shields.io/badge/JWT-000000?style=for-the-badge&logo=JSON%20web%20tokens&logoColor=white) ![Spring Security](https://img.shields.io/badge/SPRING_SECURITY-6DB33F?style=for-the-badge&logo=springsecurity&logoColor=white)                                                                                                                                                                                              |
| **Protocol**     | ![WebSocket](https://img.shields.io/badge/WEBSOCKET-010101?style=for-the-badge&logo=socket.io&logoColor=white)                                                                                                                                                                                                                                                                                                                            |
| **Cache/Lock**   | ![Redis](https://img.shields.io/badge/REDIS-DC382D?style=for-the-badge&logo=redis&logoColor=white) ![Redisson](https://img.shields.io/badge/REDISSON-DC382D?style=for-the-badge&logo=redis&logoColor=white) |
| **Infra/CI/CD**  | ![Nginx](https://img.shields.io/badge/NGINX-009639?style=for-the-badge&logo=nginx&logoColor=white) ![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white) ![Docker](https://img.shields.io/badge/DOCKER-2496ED?style=for-the-badge&logo=docker&logoColor=white) ![GitHub Actions](https://img.shields.io/badge/GITHUB_ACTIONS-2088FF?style=for-the-badge&logo=githubactions&logoColor=white) |
| **Storage**      | ![Amazon S3](https://img.shields.io/badge/AMAZON_S3-569A31?style=for-the-badge&logo=amazons3&logoColor=white)                                                                                                                                                                                                                                                                                                                             |   


### 🤖 AI & Data
| 역할 | 종류                                                                                                                                                                                                                             |
| :--- |:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Framework** | ![FastAPI](https://img.shields.io/badge/FASTAPI-005571?style=for-the-badge&logo=fastapi&logoColor=white) ![PyTorch](https://img.shields.io/badge/PYTORCH-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white)              |
| **Models** | ![Whisper](https://img.shields.io/badge/WHISPER-412991?style=for-the-badge&logo=openai&logoColor=white) ![NLLB](https://img.shields.io/badge/NLLB_TRANSLATION-4285F4?style=for-the-badge&logo=google-translate&logoColor=white)|
| **Pipeline** | ![Redis Queue](https://img.shields.io/badge/REDIS_QUEUE-DC382D?style=for-the-badge&logo=redis&logoColor=white) ![WebSocket](https://img.shields.io/badge/WEBSOCKET-010101?style=for-the-badge&logo=socket.io&logoColor=white)  |

---


## 💻 주요 구현 코드 
![](../image/stt.jpg)


---

## 👥 팀원 소개
| [**권효재**](https://github.com/Khyojae) | [**박민정**](https://github.com/emfpdlzj) |
| :---: | :---: |
| <a href="https://github.com/Khyojae"><img src="https://github.com/Khyojae.png" width="300"></a> | <a href="https://github.com/emfpdlzj"><img src="https://github.com/emfpdlzj.png" width="300"></a> |
| **Backend & Infra** | **Frontend & AI** |

![](../image/team.png)
