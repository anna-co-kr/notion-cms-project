# 개인 개발 블로그 MVP PRD

## 핵심 정보

**목적**: Notion을 CMS로 활용하여 별도의 관리 도구 없이 글 작성만으로 블로그에 자동 반영되는 개인 기술 블로그 구축  
**사용자**: Notion을 이미 사용 중인 개인 개발자로, 별도의 CMS 없이 블로그를 운영하고 싶은 1인 개발자

---

## 라우팅 구조

| URL | 페이지 | 설명 |
|-----|--------|------|
| `/` | 홈 | 전체 글 목록, 검색, 카테고리 필터 |
| `/posts/[slug]` | 글 상세 | Notion 블록 콘텐츠 렌더링 |
| `/categories/[category]` | 카테고리 | 카테고리별 필터링된 글 목록 |

> `slug`는 글 제목(Title)을 kebab-case로 변환하여 사용 (예: `next-js-15-app-router-guide`)

---

## 사용자 여정

```
1. 블로그 홈 페이지 (최초 진입)
   ↓ ISR로 캐싱된 Notion 데이터베이스 쿼리 → 발행됨 상태인 글 목록 조회

2. 글 목록 표시
   ↓ 사용자 행동 선택

   [카테고리 선택] → /categories/[category] → 해당 카테고리 글 목록 표시
   [검색 입력]     → 홈 페이지 내 클라이언트 사이드 필터링 (제목+태그+카테고리)
   [글 클릭]       → /posts/[slug] → 본문 렌더링

3. 글 상세 페이지
   ↓ notion-to-md로 Markdown 변환 → react-markdown으로 렌더링

4. 완료 → [홈으로 이동] 또는 [카테고리 페이지로 이동]
```

---

## 기능 명세

### 1. MVP 핵심 기능

| ID | 기능명 | 설명 | MVP 필수 이유 | 관련 페이지 |
|----|--------|------|--------------|------------|
| **F001** | Notion 글 목록 조회 | Notion 데이터베이스에서 `발행됨` 상태인 글 목록을 가져와 표시 | 블로그의 핵심 콘텐츠 진입점 | 홈, 카테고리 |
| **F002** | 글 상세 조회 | `notion-to-md`로 Notion 블록을 Markdown 변환 후 `react-markdown`으로 렌더링 | 블로그의 핵심 목적인 글 읽기 기능 | 글 상세 |
| **F003** | 카테고리 필터링 | Category(select) 속성 기준으로 Notion API 서버 사이드 필터링 | 콘텐츠 탐색 편의성 제공 | 홈, 카테고리 |
| **F004** | 글 검색 | 제목·태그·카테고리 기준 클라이언트 사이드 검색 (debounce 300ms, 대소문자 무시) | 콘텐츠가 늘어날수록 필수적인 탐색 수단 | 홈 |

### 2. MVP 필수 지원 기능

| ID | 기능명 | 설명 | MVP 필수 이유 | 관련 페이지 |
|----|--------|------|--------------|------------|
| **F010** | Notion API 연동 | `@notionhq/client`로 데이터베이스 쿼리 및 페이지 블록 조회. `unstable_cache`로 캐싱 | 모든 기능의 데이터 소스 | 전체 |
| **F011** | 태그 표시 | Tag(multi_select) 속성을 shadcn/ui Badge로 렌더링 | 글 주제를 한눈에 파악하는 메타 정보 | 홈, 글 상세 |
| **F012** | 반응형 레이아웃 | 모바일(1열) / 태블릿(2열) / 데스크톱(3열) 카드 그리드 | 다양한 디바이스 접근성 확보 | 전체 |
| **F013** | 에러 및 로딩 상태 처리 | `loading.tsx`, `error.tsx`, `not-found.tsx`로 사용자 피드백 제공 | API 오류 또는 존재하지 않는 글 접근 시 대응 | 전체 |

### 3. MVP 이후 기능 (제외)

- 댓글 기능
- 다크 모드 토글
- RSS 피드
- SNS 공유 버튼
- 이전/다음 글 네비게이션
- 목차(TOC) 자동 생성
- 조회수 카운트

---

## 메뉴 구조

```
개인 개발 블로그 내비게이션

헤더 메뉴 (모든 페이지 공통)
├── 블로그명/로고 → / (홈)
├── 홈 → / (F001)
└── 카테고리 → /categories/[category] (F003)
    ├── [카테고리 A]
    ├── [카테고리 B]
    └── [카테고리 C]

홈 페이지 내 요소
├── 검색 입력창 → 클라이언트 사이드 필터 (F004)
└── 글 카드 목록 → 제목, 카테고리, 태그, 날짜 (F001, F011)

글 상세 페이지 내 요소
├── 글 메타 정보 → 카테고리, 태그, 작성일 (F011)
└── 본문 콘텐츠 → notion-to-md + react-markdown 렌더링 (F002)
```

---

## 페이지별 상세 기능

### 홈 페이지 (`/`)

> **구현 기능:** `F001`, `F003`, `F004`, `F010`, `F011`, `F012`, `F013` | **인증:** 불필요

| 항목 | 내용 |
|------|------|
| **역할** | 블로그 메인 진입점으로 전체 글 목록을 표시하고 검색 및 카테고리 탐색을 지원 |
| **진입 경로** | 최초 URL 접속, 헤더 로고/홈 메뉴 클릭 |
| **사용자 행동** | 최신 글 목록 탐색 → 검색어 입력 또는 카테고리 선택으로 필터링 → 글 카드 클릭하여 상세 이동 |
| **주요 기능** | - `unstable_cache` 적용 Notion API로 `발행됨` 글 목록 조회 (F010, F001)<br>- 제목·태그·카테고리 기준 debounce 300ms 클라이언트 검색 (F004)<br>- 카테고리 선택 시 해당 카테고리 글만 필터링 (F003)<br>- 글 카드에 제목, 카테고리, 태그 Badge, 작성일 표시 (F011)<br>- `loading.tsx`로 로딩 스켈레톤 표시 (F013) |
| **다음 이동** | 글 카드 클릭 → `/posts/[slug]`, 카테고리 클릭 → `/categories/[category]` |

---

### 글 상세 페이지 (`/posts/[slug]`)

> **구현 기능:** `F002`, `F010`, `F011`, `F013` | **인증:** 불필요

| 항목 | 내용 |
|------|------|
| **역할** | Notion 페이지 블록을 Markdown으로 변환하여 개별 글을 읽는 전용 페이지 |
| **진입 경로** | 홈 또는 카테고리 페이지에서 글 카드 클릭 |
| **사용자 행동** | 제목·메타 정보(카테고리, 태그, 작성일) 확인 → 본문 읽기 → 헤더 메뉴로 다른 페이지 이동 |
| **주요 기능** | - `slug`로 Notion 페이지 조회 (F010, F002)<br>- `notion-to-md`로 블록 → Markdown 변환 후 `react-markdown`으로 렌더링 (F002)<br>- 코드 블록 신택스 하이라이팅 (`react-markdown` + `rehype-highlight` 또는 `shiki`) (F002)<br>- 글 상단 카테고리, 태그 Badge, 작성일 표시 (F011)<br>- `generateMetadata`로 동적 OG 메타태그 생성<br>- `not-found.tsx`로 존재하지 않는 slug 처리 (F013) |
| **다음 이동** | 헤더 홈 클릭 → `/`, 헤더 카테고리 클릭 → `/categories/[category]` |

---

### 카테고리 페이지 (`/categories/[category]`)

> **구현 기능:** `F001`, `F003`, `F010`, `F011`, `F012`, `F013` | **인증:** 불필요

| 항목 | 내용 |
|------|------|
| **역할** | 특정 카테고리에 속한 글 목록만 표시하는 필터링된 글 목록 페이지 |
| **진입 경로** | 홈 페이지 카테고리 Badge 클릭, 헤더 카테고리 메뉴 클릭 |
| **사용자 행동** | 선택한 카테고리의 글 목록 탐색 → 원하는 글 카드 클릭하여 상세 이동 |
| **주요 기능** | - URL `[category]` 파라미터로 Notion API 서버 사이드 필터링 쿼리 (F010, F003)<br>- `발행됨` 글 목록 카드 형태로 표시 (F001)<br>- 글 카드에 제목, 태그 Badge, 작성일 표시 (F011)<br>- 현재 카테고리명 페이지 상단 표시 (F003)<br>- `not-found.tsx`로 존재하지 않는 카테고리 처리 (F013) |
| **다음 이동** | 글 카드 클릭 → `/posts/[slug]`, 헤더 홈 클릭 → `/` |

---

## 데이터 모델

### Post (Notion 데이터베이스 레코드)

| 필드 | 설명 | 타입 |
|------|------|------|
| `id` | Notion 페이지 UUID | `string` |
| `slug` | URL용 식별자 (Title → kebab-case 변환) | `string` |
| `title` | 글 제목 | `string` |
| `category` | 카테고리 | `string` (select) |
| `tags` | 태그 목록 | `string[]` (multi_select) |
| `published` | 발행 상태 | `"초안" \| "발행됨"` |
| `excerpt` | 글 요약 (카드 미리보기용) | `string \| null` |
| `coverImage` | 커버 이미지 URL (Notion 이미지, 1시간 만료 주의) | `string \| null` |
| `createdAt` | 생성일 | `Date` |
| `updatedAt` | 최종 수정일 | `Date` |

> **Notion 이미지 URL 주의**: Notion이 반환하는 이미지 URL은 1시간 후 만료됩니다.  
> `next.config.ts`에 `remotePatterns`를 등록하고, ISR `revalidate` 주기를 1시간 이하로 설정하거나 이미지를 별도 저장소에 프록시해야 합니다.

### Block (참고용 추상 모델)

실제 구현 시 `@notionhq/client`의 `BlockObjectResponse` 타입을 직접 사용합니다.  
`notion-to-md`가 블록 → Markdown 변환을 담당합니다.

| 필드 | 설명 | 타입 |
|------|------|------|
| `id` | 블록 UUID | `string` |
| `type` | 블록 타입 (`paragraph`, `heading_1`, `code`, `image` 등) | `string` |
| `pageId` | 소속 페이지 ID | `→ Post.id` |

---

## 기술 스택

### 프론트엔드 프레임워크

- **Next.js 15.5.3** (App Router + Turbopack) — ISR, SSG, `generateStaticParams`, `generateMetadata` 활용
- **TypeScript 5** — 타입 안전성 보장, `any` 타입 사용 금지
- **React 19.1.0** — UI 라이브러리

### 스타일링 & UI

- **TailwindCSS v4** — 유틸리티 CSS 프레임워크
- **shadcn/ui** (new-york style) — Badge, Card, Input, Skeleton 등 활용
- **Lucide React** — 아이콘 라이브러리

### CMS 연동 및 콘텐츠 렌더링

- **@notionhq/client** — Notion 공식 API 클라이언트 (서버 전용, `NEXT_PUBLIC_` 접두사 금지)
- **notion-to-md** — Notion 블록 → Markdown 변환
- **react-markdown** — Markdown → React 컴포넌트 렌더링
- **rehype-highlight 또는 shiki** — 코드 블록 신택스 하이라이팅

### 캐싱 전략

- **`unstable_cache`** — `@notionhq/client` SDK 메서드 래핑 (`fetch` 옵션 직접 전달 불가)
- 글 목록 페이지: `revalidate: 3600` (1시간, Notion 이미지 URL 만료 주기 고려)
- 글 상세 페이지: `generateStaticParams` + `revalidate: 3600`

### 배포 & 호스팅

- **Vercel** — Next.js 15 최적화 배포 플랫폼, 환경 변수 관리

---

## 비기능 요구사항

### 성능

- Notion API Rate Limit: **3 req/s** — `unstable_cache`로 요청 최소화, 빌드 시 `generateStaticParams`로 사전 생성
- 글 목록: ISR `revalidate: 3600`
- 글 상세: 빌드 시 정적 생성(SSG), `generateStaticParams` 활용

### 반응형 디자인

- 모바일 (320px~): 1열 카드 레이아웃
- 태블릿 (768px~): 2열 카드 레이아웃
- 데스크톱 (1024px~): 3열 카드 레이아웃

### SEO

- 글 상세 페이지: `generateMetadata`로 글 제목/설명 동적 메타태그 적용
- OpenGraph 태그 포함 (title, description, og:image)
- 시맨틱 HTML 태그 사용 (`article`, `header`, `main` 등)

### 에러 처리

- `loading.tsx` — 데이터 로딩 중 스켈레톤 UI
- `error.tsx` — Notion API 오류 시 사용자 안내
- `not-found.tsx` — 존재하지 않는 slug/카테고리 접근 시 404 처리

### 보안

- `NOTION_TOKEN`은 서버 전용 환경 변수 — `NEXT_PUBLIC_` 접두사 절대 사용 금지
- 환경 변수는 Vercel 대시보드에서 관리

### 환경 변수

```bash
NOTION_TOKEN=          # Notion Integration API 키 (서버 전용)
NOTION_DATABASE_ID=    # Notion 데이터베이스 ID (서버 전용)
```
