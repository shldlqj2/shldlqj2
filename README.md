<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/shldlqj2/shldlqj2/main/assets/banner-dark.svg?v=2">
  <img alt="유지호 · Ryu Jiho — Cloud · Infra · Embedded" src="https://raw.githubusercontent.com/shldlqj2/shldlqj2/main/assets/banner-light.svg?v=2" width="100%">
</picture>

<p align="center">
  <samp>
    <a href="#-castle-coder">castle coder</a> ·
    <a href="#-일조매">일조매</a> ·
    <a href="#-그-밖의-작업">side projects</a> ·
    <a href="#-tech">tech</a> ·
    <a href="mailto:shldlqj2@naver.com">mail</a>
  </samp>
</p>

요청이 어떤 경로로 처리되고 상태가 어디서 바뀌는지 직접 따라가며 문제를 찾습니다.
기능 구현에서 멈추지 않고 비용·성능·운영 환경까지 함께 보고, 고친 뒤에는 문제가 났던 조건 그대로 다시 검증합니다.

**서울시립대학교 컴퓨터과학부** (2026.02 졸업) · **SSAFY** 16기 (2026.07 –) · **AWS SAA-C03** (2025.05)

<br>

## 🏰 Castle Coder

**보안 중심 AI 코드 에이전트** · 캡스톤디자인 · 2025.03 – 06 · DevOps / Infra (6인)
🏆 **캡스톤 프로젝트 우수 작품상**

VS Code 확장에서 요청하면 백엔드가 RunPod Serverless 위의 vLLM을 호출해, OWASP Top 10 취약점을 줄인 코드를 스트리밍으로 돌려줍니다. 그 추론 경로의 인프라와 AI 모델 서버를 맡았습니다.

- **gRPC 동시 요청 버그 해결** — 서로 다른 요청에 같은 답이 나오던 입력 경로 오류와 서버 조기 종료를, 요청별 입력 분리와 활성 요청 수 기반 종료로 수정 · [`7fb5572`](https://github.com/castle-coder/castle-coder-runpod/commit/7fb5572)
- **GPU 운영 방식 결정** — 추론 서버 구성 4가지를 비교해 RunPod Serverless 선택, 상시 구동 대비 시간당 비용 약 1/4 추정
- **인프라 · CI/CD** — AWS EC2 · Docker 배포, 백엔드 [GitHub Actions 파이프라인](https://github.com/castle-coder/castle-coder-be/blob/main/.github/workflows/CICD_Pipeline.yml) 구성, [WebSocket 워커](https://github.com/shldlqj2/worker-websocket)에서 gRPC 스트리밍으로 전환
- **팀 부하 테스트** — p95 44.41초 · 실패율 0% · 동시 20건 5분 안정 처리 (최대 50건)

<details>
<summary><b>트러블슈팅</b> — gRPC 전환 후 동시 요청 오류와 서버 조기 종료</summary>
<br>

| | |
|:--|:--|
| **증상** | 1초 안에 2~3개 요청을 보내면 서로 다른 질문에 같은 답변이 생성됨. 처리 중인 요청이 끝나기 직전에 새 요청이 들어오면 작업이 남았는데도 서버가 종료됨 |
| **원인** | 로그에는 요청별 프롬프트가 찍혔지만, 입력 경로를 단계별로 대조해 보니 실제 추론에는 서버 시작 시 저장된 초기 입력이 반복 사용되고 있었음. 종료 문제는 개별 요청의 완료 시점이 공유 서버의 종료와 묶여 있던 구조 때문 |
| **해결** | 요청별 프롬프트를 해당 요청의 추론 입력으로 쓰도록 분리. 종료 조건을 전체 활성 요청 수 기준으로 바꾸고 Lock으로 카운트를 보호, 예외 시에도 `finally`에서 정리. 활성 요청이 0이 돼도 0.5초 대기 후 다시 확인하고 종료 |
| **검증** | 오류를 재현했던 조건(연속 요청, 종료 직전 요청 추가, 다중 클라이언트 동시 연결)에서 반복 테스트해 요청별 응답과 종료 시점 정상 확인 |
| **코드** | [`7fb5572` gRPC 동시성 처리](https://github.com/castle-coder/castle-coder-runpod/commit/7fb5572) · [PR #6](https://github.com/castle-coder/castle-coder-runpod/pull/6) |

</details>

<details>
<summary><b>GPU 운영 방식 결정 과정</b> — RunPod Serverless를 고른 이유</summary>
<br>

- 백엔드(CPU 인스턴스)는 고정하고 추론 서버 구성 4가지를 비교
  1. GPT API 파인튜닝
  2. GPU 인스턴스 상시 구동 (RunPod Pod, AWS 온디맨드)
  3. AWS Inf2
  4. 추론 전용 Serverless (AWS Bedrock, RunPod Serverless)
- 직접 파인튜닝한 모델을 Docker 이미지로 올릴 수 있고, GPU가 동작한 시간만 초 단위로 과금되는 **RunPod Serverless** 선택
- 실측: FlashBoot를 켜도 첫 요청은 cold start로 delay 33.85초, 이후 1~2분 안에 들어온 요청은 지연이 거의 없음 · 요청 4회 비용 약 $0.04
- 1분 간격 warm-up 요청을 가정하면 시간당 약 $0.2 → A6000 Ada Pod 상시 구동($0.77/h)의 약 1/4. 사용 시간대(09~18시)에만 warm-up을 보내는 방식을 제안

</details>

```
VS Code Extension ──SSE──▶ Backend (AWS EC2 · Docker) ──gRPC──▶ RunPod Serverless (vLLM · Qwen2.5-Coder)
                                  ├── AWS RDS (대화 기록) · S3 (이미지)
                                  └── GitHub Actions CI/CD
```

[Organization](https://github.com/castle-coder) · [castle-coder-runpod](https://github.com/castle-coder/castle-coder-runpod) · [castle-coder-be](https://github.com/castle-coder/castle-coder-be) · [OWASPCodeEval](https://github.com/castle-coder/OWASPCodeEval) · [castle-coder-vscode](https://github.com/castle-coder/castle-coder-vscode) · [프로젝트 위키](https://capstone.uos.ac.kr/cdc/index.php/2%EB%B6%84%EB%B0%98-%EC%BA%90%EC%8A%AC%EC%BD%94%EB%8D%94)

<br>

## 📅 일조매

**팀 프로젝트 협업 보조 앱** (Small-Work-Everyday) · 소프트웨어공학 팀 프로젝트 · 2024 2학기 · Back-end (6인)

팀원별 Task 관리, 재촉 알림, 회의 가능 시간 계산을 제공하는 웹·모바일(React Native) 앱입니다. REST API 서버를 맡았습니다.

- **REST API 서버** — Node.js · Express, JWT 인증 포함 6개 영역 20여 개 API, Oracle 연동, AWS EC2 배포
- **API 정리** — 스케줄 등록·수정 통합, 수정 계열 API를 `PUT`으로 정리, 팀장 위임·미팅 조회 추가
- **협업 방식 개선** — API 명세를 구현 중에도 수시로 갱신하도록 바꿔 프론트 연동 병목 해소, **계획보다 3일 일찍 마무리**

<details>
<summary><b>협업 개선</b> — 무엇이 문제였고 어떻게 바꿨는지</summary>
<br>

- 기능을 다 구현한 뒤 API 변경을 한꺼번에 공유하던 방식 때문에, 팀장에게서 백엔드 진행 상황을 알기 어려워 프론트엔드 개발에 차질이 생긴다는 피드백을 받음
- DB 연동 중 나 역시 테이블 설계 정보를 제때 받지 못해 막히면서, 문제는 개인 속도가 아니라 작업이 이어지는 시점의 정보 공유라는 걸 이해함
- [Notion API 명세](https://eastern-silence-5cf.notion.site/API-For-14bb4dbdee4e80c49405e2e55c094f37)와 개발 현황을 구현 중에도 갱신(날짜별 변경 이력)하고, 다음 작업과 선행 작업을 미리 공유하는 방식으로 변경

</details>

[Small-Work-Everyday](https://github.com/2024-UOS-Software-Engineer-3-9team/Small-Work-Everyday) · [API 명세](https://eastern-silence-5cf.notion.site/API-For-14bb4dbdee4e80c49405e2e55c094f37) · [시연 영상](https://youtu.be/xPJ7QnxGDP4)

<br>

## 🔧 그 밖의 작업

- **[MiniMCWS](https://github.com/shldlqj2/MiniMCWS)** — ATmega128에서 무장 제어를 모사한 FSM, 10ms 타이머 제어 루프와 Fail-Safe, UART 패킷 CRC 검증, Python 간이 SIL · Fault Injection `C`
- **[MiniOFP](https://github.com/shldlqj2/MiniOFP)** — 비행 소프트웨어(OFP) 핵심 개념 학습, 대권 거리(Haversine) 등 항법 계산과 단위 테스트 `C++`
- **[SSAFY STM32 스터디](https://github.com/0731chae/ssafy_stm32_study)** — SSAFY 16기 임베디드 스터디, STM32F446RE 주차별 실습 `C`
- **[SM-T500-LINUX](https://github.com/shldlqj2/SM-T500-LINUX)** — *진행 중* · Galaxy Tab A7에 Debian 네이티브 부팅을 목표로 한 커널 포팅 학습, 현재 기기 조사 도구 구현 단계

<br>

## 🛠 Tech

![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat-square)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=flat-square&logo=linux&logoColor=black)
![vLLM](https://img.shields.io/badge/vLLM-30A2FF?style=flat-square)
![gRPC](https://img.shields.io/badge/gRPC-244C5A?style=flat-square)
<br>
![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![C++](https://img.shields.io/badge/C++-00599C?style=flat-square&logo=cplusplus&logoColor=white)
![C](https://img.shields.io/badge/C-A8B9CC?style=flat-square&logo=c&logoColor=black)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat-square&logo=javascript&logoColor=black)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=nodedotjs&logoColor=white)
![Express](https://img.shields.io/badge/Express-000000?style=flat-square&logo=express&logoColor=white)
![STM32](https://img.shields.io/badge/STM32-03234B?style=flat-square&logo=stmicroelectronics&logoColor=white)
