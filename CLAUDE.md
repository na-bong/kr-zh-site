# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

AI 하이브리드 한중 언어문화 교육 통합 웹플랫폼
- 한국어-중국어 양방향 언어 학습
- AI 기반 맞춤형 학습 및 문화 콘텐츠 제공

## Tech Stack

**Frontend:**
- Next.js 14+ (App Router)
- TypeScript
- Tailwind CSS
- Zustand (상태 관리)

**Backend:**
- Python FastAPI
- Pydantic (데이터 검증)

**Database:**
- Supabase (PostgreSQL + Auth + Storage + Realtime)

**AI Services:**
- LLM: OpenAI GPT / Anthropic Claude API
- OCR: 한자/한글 필기 인식
- 이미지 생성: 문화 콘텐츠 시각화
- 자체 모델: 어휘 추출, 등급 분류, 문화 맥락 분석

## Project Structure

```
/
├── frontend/                 # Next.js 앱
│   ├── app/                  # App Router 페이지
│   │   ├── (auth)/           # 인증 관련 페이지
│   │   ├── (dashboard)/      # 대시보드
│   │   ├── learn/            # 학습 모듈
│   │   │   ├── vocabulary/   # 어휘 학습
│   │   │   ├── grammar/      # 문법 학습
│   │   │   ├── reading/      # 읽기 학습
│   │   │   └── culture/      # 문화 학습
│   │   └── api/              # Next.js API routes (BFF)
│   ├── components/
│   │   ├── ui/               # 공통 UI 컴포넌트
│   │   ├── learning/         # 학습 관련 컴포넌트
│   │   └── ai/               # AI 인터랙션 컴포넌트
│   ├── lib/
│   │   ├── supabase/         # Supabase 클라이언트
│   │   └── api/              # API 클라이언트
│   └── hooks/                # Custom React hooks
│
├── backend/                  # FastAPI 서버
│   ├── app/
│   │   ├── api/
│   │   │   ├── v1/
│   │   │   │   ├── ai/       # AI 엔드포인트
│   │   │   │   ├── learning/ # 학습 엔드포인트
│   │   │   │   └── content/  # 콘텐츠 엔드포인트
│   │   ├── core/             # 설정, 보안
│   │   ├── models/           # Pydantic 모델
│   │   ├── services/
│   │   │   ├── llm/          # LLM 서비스
│   │   │   ├── ocr/          # OCR 서비스
│   │   │   ├── vocab/        # 어휘 분석 서비스
│   │   │   └── culture/      # 문화 맥락 분석
│   │   └── db/               # DB 연결
│   └── tests/
│
└── shared/                   # 공유 타입, 상수
    └── types/
```

## Development Commands

**Frontend (Next.js):**
```bash
cd frontend
npm install
npm run dev              # 개발 서버 (http://localhost:3000)
npm run build            # 프로덕션 빌드
npm run lint             # ESLint 실행
npm run test             # Jest 테스트
npm run test -- --watch  # 테스트 watch 모드
```

**Backend (FastAPI):**
```bash
cd backend
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
uvicorn app.main:app --reload  # 개발 서버 (http://localhost:8000)
pytest                         # 테스트 실행
pytest -k "test_name"          # 특정 테스트 실행
```

## Architecture Guidelines

### Frontend-Backend 통신
- Next.js API Routes를 BFF(Backend For Frontend)로 사용
- FastAPI는 AI/ML 처리 전담
- Supabase는 프론트엔드에서 직접 접근 (인증, 실시간 데이터)

### AI 서비스 통합
```
[사용자] → [Next.js] → [FastAPI] → [AI Services]
                 ↓
            [Supabase]
```

- LLM 호출은 반드시 FastAPI 백엔드를 통해 수행 (API 키 보호)
- 스트리밍 응답 사용 시 Server-Sent Events 활용
- 어휘 분석 결과는 Supabase에 캐싱

### 다국어 처리
- 모든 UI 텍스트는 i18n 키로 관리
- 언어 코드: `ko` (한국어), `zh` (중국어), `en` (영어)
- 콘텐츠 DB 스키마에 language 필드 필수

### 학습 데이터 모델
- 어휘는 HSK/TOPIK 등급 기준으로 분류
- 학습 진도는 사용자별로 Supabase에 저장
- 문화 콘텐츠는 태그 기반 분류 (음식, 명절, 역사 등)

## Supabase 테이블 구조 (핵심)

- `users`: 사용자 프로필, 학습 설정
- `vocabulary`: 어휘 데이터 (한자, 병음, 뜻, 등급)
- `user_vocabulary`: 사용자별 어휘 학습 상태
- `learning_sessions`: 학습 세션 기록
- `culture_content`: 문화 콘텐츠
- `ai_conversations`: AI 대화 기록

## Code Conventions

- 컴포넌트: PascalCase (`VocabularyCard.tsx`)
- 훅: camelCase with use prefix (`useVocabulary.ts`)
- API 엔드포인트: snake_case (`/api/v1/vocab_analysis`)
- 한중 텍스트 필드명: `text_ko`, `text_zh`, `text_en`
