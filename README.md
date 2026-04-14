# 개인 개발 블로그

Notion을 CMS로 활용한 개인 기술 블로그입니다. Notion에서 글을 작성하면 자동으로 블로그에 반영됩니다.

## 기술 스택

- **Framework**: Next.js 15.5.3 (App Router + Turbopack)
- **Runtime**: React 19.1.0 + TypeScript 5
- **Styling**: TailwindCSS v4 + shadcn/ui (new-york style)
- **CMS**: Notion API (`@notionhq/client`)
- **Deployment**: Vercel

## 주요 기능

- Notion 데이터베이스에서 블로그 글 목록 조회
- 개별 글 상세 페이지 (notion-to-md + react-markdown 렌더링)
- 카테고리별 필터링
- 제목·태그·카테고리 통합 검색
- 반응형 디자인 (모바일 / 태블릿 / 데스크톱)

## 시작하기

### 1. 의존성 설치

```bash
npm install
```

### 2. 환경 변수 설정

`.env.local` 파일을 생성하고 아래 값을 입력하세요.

```bash
NOTION_TOKEN=           # Notion Integration API 키
NOTION_DATABASE_ID=     # Notion 데이터베이스 ID
```

> Notion Integration 생성 방법: [Notion 개발자 문서](https://developers.notion.com/docs/getting-started)

### 3. Notion 데이터베이스 구조

아래 속성을 가진 Notion 데이터베이스를 생성하세요.

| 속성명 | 타입 | 설명 |
|--------|------|------|
| Title | title | 글 제목 |
| Category | select | 카테고리 |
| Tag | multi_select | 태그 |
| Published | select | 상태 (`초안` / `발행됨`) |

### 4. 개발 서버 실행

```bash
npm run dev
```

[http://localhost:3000](http://localhost:3000) 에서 확인하세요.

## 스크립트

```bash
npm run dev        # 개발 서버 실행 (Turbopack)
npm run build      # 프로덕션 빌드
npm run check-all  # 타입 검사 + 린트 + 포맷 통합 실행
```

## 프로젝트 구조

```
src/
├── app/
│   ├── page.tsx                    # 홈 (글 목록)
│   ├── posts/[slug]/page.tsx       # 글 상세
│   └── categories/[category]/page.tsx  # 카테고리
├── components/                     # 재사용 컴포넌트
└── lib/
    └── notion.ts                   # Notion API 유틸리티
docs/
├── PRD.md                          # 프로젝트 요구사항
└── guides/                         # 개발 가이드
```

## 배포

Vercel에 배포 시 환경 변수(`NOTION_TOKEN`, `NOTION_DATABASE_ID`)를 Vercel 대시보드에서 설정하세요.
