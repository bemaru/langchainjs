# LangChain.js 분석

## 메타 정보

| 항목 | 내용 |
|------|------|
| 저장소 | [langchain-ai/langchainjs](https://github.com/langchain-ai/langchainjs) |
| 주요 언어 | TypeScript |
| 라이선스 | MIT — 완전 자유 사용, 수정, 재배포 가능 |
| Stars | 17K+ |
| 분석일 | 2026-02-12 |
| 성격 | LLM 오케스트레이션 프레임워크 |

## 핵심 인사이트

1. **Runnable 통일 인터페이스** — 모든 컴포넌트(LLM, 프롬프트, 도구, 파서)가 동일한 `invoke/stream/batch/transform` 4방향 프로토콜을 구현. 이를 통해 `pipe()` 한 줄로 임의의 컴포넌트를 조합 가능
2. **LCEL(LangChain Expression Language)** — `prompt.pipe(model).pipe(parser)` 같은 선언적 파이프라인 문법. RunnableSequence, RunnableParallel, RunnableBranch 등으로 복잡한 워크플로우를 직관적으로 표현
3. **Provider 분리 아키텍처** — `@langchain/core`를 peerDependency로 공유하고 30+ provider를 독립 패키지로 분리. 벤더 락인 없이 모델 교체 가능
4. **관찰성 내장 설계** — CallbackManager + LangSmith 트레이싱이 기본 내장. 모든 Runnable의 실행/에러/완료 이벤트를 계층적으로 추적
5. **Zod v3/v4 Interop Layer** — 1,028줄의 상호운용 계층으로 사용자의 Zod 버전 선택 자유를 보장하면서 Tool 스키마 검증의 타입 안전성 유지

## 문서 구성

| 문서 | 내용 |
|------|------|
| [overview.md](./overview.md) | 프로젝트 철학, 차별점, 기술 스택 선택 이유 |
| [core-logic.md](./core-logic.md) | Runnable 실행 흐름, Agent 루프, LCEL 파이프라인 상세 |
| [architecture.md](./architecture.md) | Monorepo 구조, 계층 아키텍처, 확장 모델 |
