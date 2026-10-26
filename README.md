## 유지호 · Ryu Jiho

요청이 어떤 경로로 처리되고, 상태가 어디서 바뀌는지 직접 따라가며 문제를 찾는 개발자입니다.
기능을 만드는 데서 끝내지 않고 비용·성능·운영 환경까지 함께 보고, 고친 뒤에는 문제가 났던 조건 그대로 다시 검증합니다.

- 🎓 서울시립대학교 컴퓨터과학부 (2020.03 – 2026.02)
- 🏫 삼성청년SW아카데미(SSAFY) (2026.07 – )
- ☁️ AWS Certified Solutions Architect – Associate (SAA-C03, 2025.05)
- 🔎 관심 분야: Private Cloud 개발·운영, 인프라, 임베디드 SW
- 📫 shldlqj2@naver.com

<br>

## 🏰 Castle Coder — 보안 중심 AI Code Agent

> 2025.03 – 2025.06 · 서울시립대학교 캡스톤디자인 · **🏆 캡스톤 프로젝트 우수 작품상 (2025.07)**
> 역할: **DevOps / Infra** (6인 팀)

VS Code 확장에서 코드를 생성·보안 리팩토링하면, 백엔드가 RunPod Serverless 위의 vLLM 추론 서버를 호출해
OWASP Top 10 취약점을 줄인 코드를 스트리밍으로 돌려주는 AI 코딩 보조 도구입니다.

```
VS Code Extension ──SSE──▶ Backend (AWS EC2 · Docker) ──gRPC streaming──▶ RunPod Serverless (vLLM · Qwen2.5-Coder)
                                  │
                                  ├── AWS RDS (대화 기록) · S3 (이미지)
                                  └── GitHub Actions CI/CD
```

**내가 한 일**
- AWS 기반 서버·네트워크 인프라 구성, 백엔드 [GitHub Actions CI/CD 파이프라인](https://github.com/castle-coder/castle-coder-be/blob/main/.github/workflows/CICD_Pipeline.yml) 구성
- RunPod `worker-vllm` 이미지를 커스터마이징한 AI 모델 서버 개발 — [WebSocket 통신](https://github.com/shldlqj2/worker-websocket) 구현 후 gRPC 스트리밍으로 전환
- OWASP Top 10 기반 코드 보안 평가 벤치마크 **OWASPCodeEval** 개발 참여

**GPU 운영 방식 결정**
- 백엔드(CPU 인스턴스)는 고정하고, 추론 서버 구성 4가지를 비교: GPT API 파인튜닝 / GPU 인스턴스 상시 구동(RunPod Pod, AWS 온디맨드) / AWS Inf2 / 추론 전용 Serverless(AWS Bedrock, RunPod Serverless)
- 직접 파인튜닝한 모델을 Docker 이미지로 올릴 수 있고, GPU가 동작한 시간만 초 단위로 과금되는 **RunPod Serverless** 선택
- 실측: FlashBoot를 켜도 첫 요청은 cold start로 delay 33.85초, 이후 1~2분 안에 들어온 요청은 지연이 거의 없음 · 요청 4회 비용 약 $0.04
- 1분 간격 warm-up 요청을 가정하면 시간당 약 $0.2로, A6000 Ada Pod 상시 구동($0.77/h)의 약 1/4 수준 → 사용 시간대(09~18시)에만 warm-up을 보내는 방식을 제안

**트러블슈팅 — gRPC 전환 후 동시 요청 오류와 서버 조기 종료**
- **증상** — 1초 안에 2~3개 요청을 보내면 서로 다른 질문에 같은 답변이 생성됨. 처리 중인 요청이 끝나기 직전에 새 요청이 들어오면 작업이 남았는데도 서버가 종료됨
- **원인** — 로그에는 요청별 프롬프트가 찍혔지만, 입력 경로를 단계별로 대조해 보니 실제 추론에는 서버 시작 시 저장된 초기 입력이 반복 사용되고 있었음. 종료 문제는 개별 요청의 완료 시점이 공유 서버의 종료와 묶여 있던 구조 때문
- **해결** — 요청별 프롬프트를 해당 요청의 추론 입력으로 쓰도록 분리. 종료 조건을 "전체 활성 요청 수" 기준으로 바꾸고 잠금으로 카운트를 보호, 예외 발생 시에도 카운트를 정리. 활성 요청이 0이 돼도 잠시 대기 후 종료
- **검증** — 오류를 재현했던 조건(연속 요청, 종료 직전 요청 추가, 다중 클라이언트 동시 연결)에서 반복 테스트해 요청별 응답과 종료 시점 정상 확인
- **코드** — [`7fb5572` gRPC 동시성 처리](https://github.com/castle-coder/castle-coder-runpod/commit/7fb5572) · [PR #6](https://github.com/castle-coder/castle-coder-runpod/pull/6)

**팀 결과** — 부하·스트레스·스파이크 테스트 p95 44.41초 · 실패율 0% · 동시 20건 5분 안정 처리 (최대 50건)

`AWS EC2` `S3` `RDS` `Docker` `GitHub Actions` `RunPod Serverless` `vLLM` `gRPC` `SSE` `Python`

🔗 [castle-coder (Organization)](https://github.com/castle-coder) · [castle-coder-runpod](https://github.com/castle-coder/castle-coder-runpod) · [castle-coder-be](https://github.com/castle-coder/castle-coder-be) · [OWASPCodeEval](https://github.com/castle-coder/OWASPCodeEval) · [castle-coder-vscode](https://github.com/castle-coder/castle-coder-vscode) · [프로젝트 위키](https://capstone.uos.ac.kr/cdc/index.php/2%EB%B6%84%EB%B0%98-%EC%BA%90%EC%8A%AC%EC%BD%94%EB%8D%94)

<br>

## 📅 일조매 (Small-Work-Everyday) — 팀 프로젝트 협업 보조 앱

> 2024 2학기 · 서울시립대학교 소프트웨어공학 팀 프로젝트
> 역할: **Back-end** (6인 팀)

팀원별 Task 관리, 재촉 알림, 회의 가능 시간 계산 기능을 제공하는 웹·모바일(React Native) 크로스 플랫폼 앱입니다.

**내가 한 일**
- Node.js / Express 기반 REST API 서버 개발 및 AWS EC2 배포 — JWT 인증, 프로젝트·Task, 스케줄, 알림 등 6개 영역 20여 개 엔드포인트
- 스케줄 등록·수정 API 통합, 수정 계열 API를 `PUT`으로 정리, 팀장 위임·미팅 조회 기능 추가
- Oracle DB 연동, CORS 설정 등 프론트엔드 연동 지원

**협업 개선**
- 기능을 다 구현한 뒤 API 변경을 한꺼번에 공유하던 방식 때문에 프론트엔드 연동이 막힌다는 피드백을 받음
- [Notion API 명세](https://eastern-silence-5cf.notion.site/API-For-14bb4dbdee4e80c49405e2e55c094f37)와 개발 현황을 구현 중에도 수시로 갱신(날짜별 변경 이력 기록)하고, 다음 작업과 선행 작업을 미리 공유하는 방식으로 변경
- 프론트·백엔드 연동 병목이 줄어 **계획보다 3일 일찍 마무리**

`Node.js` `Express` `Oracle` `AWS EC2` `REST API`

🔗 [Small-Work-Everyday](https://github.com/2024-UOS-Software-Engineer-3-9team/Small-Work-Everyday) · [API 명세](https://eastern-silence-5cf.notion.site/API-For-14bb4dbdee4e80c49405e2e55c094f37) · [시연 영상](https://youtu.be/xPJ7QnxGDP4)

<br>

## 🔧 그 밖의 작업

| 프로젝트 | 내용 | 기술 |
|:--|:--|:--|
| [MiniMCWS](https://github.com/shldlqj2/MiniMCWS) | ATmega128 보드에서 무장 제어를 모사한 FSM(SAFE / SAFE_LOCK / ARMED / FIRE), 10ms 타이머 기반 제어 루프와 Fail-Safe, UART 패킷 CRC 검증, Python 간이 SIL·Fault Injection | `C` `ATmega128` `UART` `Python` |
| [MiniOFP](https://github.com/shldlqj2/MiniOFP) | 비행 소프트웨어(OFP) 핵심 개념 학습용 프로젝트 — 대권 거리(Haversine) 등 항법 계산 구현, 단위 테스트 | `C++` `CMake` `GoogleTest` |
| [SSAFY STM32 스터디](https://github.com/0731chae/ssafy_stm32_study) | SSAFY 16기 임베디드 스터디, STM32F446RE 대상 주차별 실습 | `C` `STM32` |
| [SM-T500-LINUX](https://github.com/shldlqj2/SM-T500-LINUX) | *(진행 중)* Galaxy Tab A7에 Debian 네이티브 부팅을 목표로 한 커널 포팅 학습 — 현재 기기 조사 도구 구현 단계 | `Linux Kernel` `Python` |

<br>

## 🛠 Tech Stack

| 분야 | 기술 |
|:--|:--|
| Language | Python · C++ · C · JavaScript |
| Cloud / Infra | AWS (EC2, S3, RDS) · Docker · GitHub Actions · Linux |
| AI Serving | RunPod Serverless · vLLM |
| Backend | Node.js · Express · gRPC · SSE |
| Embedded | ATmega128 · STM32 · UART |
