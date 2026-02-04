# DOCKin — 조선소 현장용 AR·AI 안전/업무 보조 앱
## 2025 K-조선 해커톤 프로젝트 **대상**
#### 산업 통상자원부 주최

--- 
### 개발 기간
- 기획 기간(예선): 2025.09 ~ 2025.10
- 본선 개발 기간: 2025.10 ~ 2025.11

---

## 🔍 프로젝트 개요
DOCKin은 조선소 근로자를 위한 AR 기반 작업 안내, AI 음성 인식, 다국어 번역, 안전·근태 관리를 통합한 모바일 앱입니다.  
실제 조선 현장에서 자주 발생하는 문제(언어 장벽, 보고 누락, 위치 혼선)를 기술로 해결하는 것을 목표로 합니다.

---

## 🏗️ 전체 개발 구조
![](../image/Slide%2016_9%20-%209.jpg)

## 🎥 시연 영상 
![](../image/%ED%99%98%EC%8A%B9%EC%84%B0%EB%B0%95.gif)

---

## ✨ 핵심 기능 
![](../image/스크린샷%202025-11-17%2022.56.59.png)
![](../image/스크린샷%202025-11-17%2022.58.35.png)
![](../image/스크린샷%202025-11-17%2022.57.14.png)
![](../image/스크린샷%202025-11-17%2022.57.19.png)
![](../image/스크린샷%202025-11-17%2022.57.25.png)

---

## 🛠️ 기술 스택

| 분야 | 핵심 기술 및 라이브러리 | 상세 구현 내용 |
| :--- | :--- | :--- |
| **Mobile** | ![React Native](https://img.shields.io/badge/React_Native-61DAFB?style=flat-square&logo=react&logoColor=black) ![Zustand](https://img.shields.io/badge/Zustand-443E38?style=flat-square) | `React Native` 기반 전환, `Zustand` & `React Query` 상태 관리, `Axios` 통신 |
| **Real-time** | ![WebSocket](https://img.shields.io/badge/WebSocket-010101?style=flat-square&logo=socket.io) ![Firebase](https://img.shields.io/badge/FCM-FFCA28?style=flat-square&logo=firebase&logoColor=black) | 실시간 채팅(WS), 중요 이벤트 알림(FCM), JWT 연결 보안 |
| **Backend** | ![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=flat-square&logo=springboot&logoColor=white) ![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=flat-square&logo=fastapi) | `Spring Boot` 메인 서버, `FastAPI` AI 전용 서버, `Swagger` API 명세 |
| **AI & Queue** | ![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white) ![OpenAI](https://img.shields.io/badge/OpenAI-412991?style=flat-square&logo=openai) | `Redis Queue` 기반 비동기 작업(STT/번역/챗봇), Job ID 폴링 구조 |
| **Infra/DB** | ![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat-square&logo=mysql&logoColor=white) ![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker) ![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat-square&logo=amazon-aws) | `Docker` 가상환경, `AWS S3` 대용량 업로드, `Vercel` 프론트 배포 |
| **Offline** | ![SQLite](https://img.shields.io/badge/SQLite-003B57?style=flat-square&logo=sqlite) ![Storage](https://img.shields.io/badge/AsyncStorage-3DDC84?style=flat-square&logo=android) | `WatermelonDB/SQLite` 대용량 데이터 관리, `AsyncStorage` 경량 저장 |

---

## 💻 주요 구현 코드
![](../image/Slide%2016_9%20-%2046.jpg)
![](../image/Slide%2016_9%20-%2061.jpg)
![](../image/Slide%2016_9%20-%2062.jpg)

---

## 👥 팀원 소개

| [**권효재**](https://github.com/Khyojae) | [**박민정**](https://github.com/emfpdlzj) |
| :---: | :---: |
| <a href="https://github.com/Khyojae"><img src="https://github.com/Khyojae.png" width="300"></a> | <a href="https://github.com/emfpdlzj"><img src="https://github.com/emfpdlzj.png" width="300"></a> |
| **Backend & Infra** | **Frontend & AI  & Infra*** |
