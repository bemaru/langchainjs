# LangChain.js — Overview

## 프로젝트 철학

LangChain.js는 **LLM 기반 애플리케이션의 복잡성을 추상화하는 오케스트레이션 프레임워크**다. 핵심 철학은 다음과 같다:

- **Interoperability First**: 표준 인터페이스로 벤더 락인을 최소화. 개발자가 LLM 모델을 쉽게 교체 가능
- **Composition over Inheritance**: 거의 모든 기능이 Runnable 조합으로 구현. 계층적 상속 최소화
- **Future-Proof Abstractions**: 기술 변화에 빠르게 대응하기 위한 추상화 중심 설계
- **Production-Ready**: LangSmith 통합으로 monitoring, evaluation, debugging 기본 내장
- **Community-Driven**: 통합은 standalone 패키지로 분산, 커뮤니티 기여 적극 장려

## 해결하는 문제

LLM이 주변 생태계(도구, 데이터, 메모리)와 상호작용하는 과정을 체계적으로 관리한다:
- 서로 다른 LLM 제공자(Anthropic, OpenAI, Groq 등)를 동일한 인터페이스로 사용
- 스트리밍, 배치, 단일 호출을 통일된 API로 지원
- 도구 호출과 에이전트 상태를 프레임워크 차원에서 관리
- 실행 흐름 추적과 디버깅을 위한 관찰성(Observability) 제공

**기존 대안 대비 차별점:**

| 관점 | LangChain.js | 기존 대안 |
|------|-------------|----------|
| 통합 제공자 | 35+ 공식 + 커뮤니티 | 단일 제공자 또는 제한적 |
| 표현력(LCEL) | `prompt \| model \| parser` 선언적 | 명령형 코드 필요 |
| 환경 지원 | Node.js, Browser, Edge, Workers, Deno, Bun | 주로 Node.js 또는 Python |
| 타입 안전성 | 강타입 TypeScript, 제네릭 기반 | 약타입 또는 동적 타입 |
| 스트리밍 | 1급 시민, 모든 Runnable 기본 지원 | 종종 사후 고려 |
| 관찰성 | 계층적 콜백 + LangSmith 내장 | 별도 설정 필요 |

## 기술 스택과 선택 이유

### 핵심 의존성

| 의존성 | 선택 이유 | 트레이드오프 |
|--------|-----------|------------|
| **langsmith** (>=0.5.0) | 트레이싱/모니터링/평가 — 운영 철학의 핵심 | 기본 의존성이지만 비활성화 가능 |
| **zod** (^3.25 \|\| ^4) | Tool 스키마, structured output 정의에 필수 | v3/v4 양쪽 지원으로 interop 복잡성 |
| **p-queue** (^6.6.2) | Batch 처리와 rate limiting 동시성 제어 | 추가 의존성 |
| **js-tiktoken** (^1.0.12) | OpenAI 호환 토큰 카운팅 | Context window 관리용 |
| **uuid** (^10.0.0) | Run ID, trace ID 생성 | 분산 추적 유일성 보장 |

### Monorepo 도구

| 도구 | 역할 | 이유 |
|------|------|------|
| **pnpm workspaces** | 패키지 관리 | 엄격한 의존성 격리, 디스크 효율 |
| **Turborepo** | 빌드 오케스트레이션 | 병렬 빌드, 증분 빌드, 선택적 필터링 |
| **tsdown** | TypeScript 빌드 | ESM/CJS dual build 지원 |

### 환경 호환성 전략

하나의 코드가 다양한 환경에서 동작:
- Node.js (ESM/CJS), Cloudflare Workers, Vercel/Next.js, Supabase Edge Functions, Browser, Deno, Bun
- `getRuntimeEnvironment()` 자동 감지
- Subpath exports로 환경별 최적화된 번들 제공

### Peer Dependencies 전략

```
@langchain/core ← peerDependency로 모든 provider가 공유
  ↑
@langchain/openai, @langchain/anthropic, ... ← 각각 독립 패키지
```

**이유**: 모든 패키지가 동일 버전의 core를 공유하도록 강제하여 호환성 보장.

---

## 배울 점

1. **Universal Interface 설계**: `invoke/stream/batch/transform` 4방향 프로토콜로 모든 컴포넌트를 통일. 임의의 조합이 가능해지면서 프레임워크의 표현력이 기하급수적으로 증가
2. **Peer Dependencies로 버전 통일**: core를 peerDependency로 설정하여 여러 provider 패키지가 동일 core 인스턴스를 공유. 타입 불일치와 런타임 오류 방지
3. **관찰성 기본 내장**: 별도 설정 없이 모든 실행 경로에 콜백이 자동 연결. 프로덕션 운영을 "나중에 추가"가 아닌 "처음부터 포함"으로 설계
4. **Zod Interop Layer**: 주요 의존성의 메이저 버전 전환기에 양쪽을 동시 지원하는 interop 계층을 만들어 사용자 마이그레이션 부담 제거

## 적용 아이디어

| LangChain.js 패턴 | EDR AI 적용 |
|-------------------|-------------|
| Runnable 통일 인터페이스 | AI 분석 컴포넌트(탐지, 분류, 요약, 추천)를 동일 인터페이스로 통일하여 파이프라인 조합 |
| LCEL 파이프라인 | `이벤트수집.pipe(위협분류).pipe(대응추천)` 같은 선언적 분석 체인 구성 |
| Provider 분리 | LLM 제공자(Claude, GPT 등)를 독립 패키지로 관리하여 모델 교체 비용 최소화 |
| 관찰성 내장 | AI 분석 파이프라인의 모든 단계를 자동 추적하여 판단 근거와 성능을 실시간 모니터링 |
| Peer Dependencies | AI 공통 인프라(@edr/ai-core)를 peerDependency로 설정하여 기능 모듈 간 버전 통일 |
| Config 병합 전략 | 기본 설정(전역) + 런타임 설정(사용자별)을 계층적으로 병합하는 설정 관리 |
