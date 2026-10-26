# 안녕하세요. 클라우드·인프라 개발자 유지호입니다.

<a href="https://www.credly.com/badges/3071fb07-36a7-4366-af76-46cde0d2f8fa/public_url"><img align="right" width="110" alt="AWS Certified Solutions Architect – Associate" src="https://images.credly.com/size/340x340/images/0e284c3f-5164-4b21-8660-0d84737941bc/image.png"></a>

요청이 처리되는 흐름을 직접 따라가며 문제의 원인을 찾고, 고친 뒤에는 문제가 났던 조건에서 다시 확인하는 것을 중요하게 생각합니다.

- 서울시립대학교 컴퓨터과학부 졸업 (2026.02)
- SSAFY 16기 (2026.07 ~)
- AWS Certified Solutions Architect – Associate (2025.05)
- shldlqj2@naver.com

<br>

## Tech Stack

| Category | Technologies |
| :--- | :--- |
| **Languages** | <img src="https://img.shields.io/badge/python-3776AB?style=for-the-badge&logo=python&logoColor=white"> <img src="https://img.shields.io/badge/c++-00599C?style=for-the-badge&logo=cplusplus&logoColor=white"> <img src="https://img.shields.io/badge/c-A8B9CC?style=for-the-badge&logo=c&logoColor=black"> <img src="https://img.shields.io/badge/javascript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black"> |
| **Infrastructure** | <img src="https://img.shields.io/badge/aws-232F3E?style=for-the-badge&logoColor=white"> <img src="https://img.shields.io/badge/docker-2496ED?style=for-the-badge&logo=docker&logoColor=white"> <img src="https://img.shields.io/badge/github_actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white"> <img src="https://img.shields.io/badge/linux-FCC624?style=for-the-badge&logo=linux&logoColor=black"> |
| **Backend** | <img src="https://img.shields.io/badge/node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white"> <img src="https://img.shields.io/badge/express-000000?style=for-the-badge&logo=express&logoColor=white"> <img src="https://img.shields.io/badge/grpc-244C5A?style=for-the-badge&logoColor=white"> |
| **AI Serving** | <img src="https://img.shields.io/badge/runpod-673AB7?style=for-the-badge&logoColor=white"> <img src="https://img.shields.io/badge/vllm-30A2FF?style=for-the-badge&logoColor=white"> |
| **Embedded** | <img src="https://img.shields.io/badge/stm32-03234B?style=for-the-badge&logo=stmicroelectronics&logoColor=white"> <img src="https://img.shields.io/badge/atmega128-5A5A5A?style=for-the-badge&logoColor=white"> |

<br>

## Projects

### Castle Coder | 보안 중심 AI 코드 에이전트

2025.03 ~ 2025.06 · 서울시립대학교 캡스톤디자인 **우수 작품상** · DevOps/Infra 담당

- AWS EC2, Docker로 서버를 구성하고 GitHub Actions로 CI/CD 구축
- RunPod Serverless 위에 vLLM 추론 서버 개발 (WebSocket에서 gRPC 스트리밍으로 전환)
- gRPC 전환 후 동시에 요청하면 같은 답변이 나오고 서버가 먼저 종료되던 문제 해결 ([커밋](https://github.com/castle-coder/castle-coder-runpod/commit/7fb5572))
- GPU 운영 방식을 비교해 RunPod Serverless 선택 (상시 구동 대비 시간당 약 1/4 비용으로 추정)

[Organization](https://github.com/castle-coder) · [castle-coder-runpod](https://github.com/castle-coder/castle-coder-runpod) · [castle-coder-be](https://github.com/castle-coder/castle-coder-be) · [OWASPCodeEval](https://github.com/castle-coder/OWASPCodeEval) · [프로젝트 위키](https://capstone.uos.ac.kr/cdc/index.php/2%EB%B6%84%EB%B0%98-%EC%BA%90%EC%8A%AC%EC%BD%94%EB%8D%94)

### 일조매 | 팀 프로젝트 협업 보조 앱

2024 2학기 · 서울시립대학교 소프트웨어공학 팀 프로젝트 · 백엔드 담당

- Node.js, Express로 REST API 개발 및 AWS EC2 배포 (JWT 인증 포함 20여 개 API)
- API 명세를 Notion에 수시로 갱신해 공유하는 방식으로 바꿔 프론트엔드 연동 병목을 줄이고, 계획보다 3일 일찍 마무리

[Repository](https://github.com/2024-UOS-Software-Engineer-3-9team/Small-Work-Everyday) · [API 명세](https://eastern-silence-5cf.notion.site/API-For-14bb4dbdee4e80c49405e2e55c094f37) · [시연 영상](https://youtu.be/xPJ7QnxGDP4)

<br>

## Side Projects

| Project | Description |
| :--- | :--- |
| [MiniMCWS](https://github.com/shldlqj2/MiniMCWS) | ATmega128 보드로 무장 제어 FSM과 UART 통신(CRC) 구현 |
| [MiniOFP](https://github.com/shldlqj2/MiniOFP) | 비행 소프트웨어(OFP) 핵심 개념을 C++로 구현하며 학습 |
| [SSAFY STM32 스터디](https://github.com/0731chae/ssafy_stm32_study) | STM32F446RE 보드 주차별 실습 |
| [SM-T500-LINUX](https://github.com/shldlqj2/SM-T500-LINUX) | 갤럭시 탭 A7에 리눅스 포팅 (진행 중) |
