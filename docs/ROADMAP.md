# 개인 개발 블로그 개발 로드맵

Notion을 CMS로 활용하여 글 작성만으로 블로그에 자동 반영되는 1인 개발자용 기술 블로그

## 개요

개인 개발 블로그는 Notion을 이미 사용 중인 1인 개발자를 위한 **무(無)관리 CMS 블로그 솔루션**으로 다음 기능을 제공합니다:

- **Notion CMS 연동**: Notion 데이터베이스에 글을 작성하면 자동으로 블로그에 반영
- **빠른 콘텐츠 탐색**: 카테고리 필터링 및 제목/태그/카테고리 기반 실시간 검색
- **최적화된 읽기 경험**: Markdown 렌더링, 코드 하이라이팅, 반응형 카드 그리드 레이아웃

## 개발 워크플로우

1. **작업 계획**

- 기존 코드베이스를 학습하고 현재 상태를 파악
- 새로운 작업을 포함하도록 `ROADMAP.md` 업데이트
- 우선순위 작업은 마지막 완료된 작업 다음에 삽입

2. **작업 생성**

- 기존 코드베이스를 학습하고 현재 상태를 파악
- `/tasks` 디렉토리에 새 작업 파일 생성
- 명명 형식: `XXX-description.md` (예: `001-setup.md`)
- 고수준 명세서, 관련 파일, 수락 기준, 구현 단계 포함
- **API/비즈니스 로직 작업 시 "## 테스트 체크리스트" 섹션 필수 포함 (Playwright MCP 테스트 시나리오 작성)**
- **테스트 체크리스트에는 반드시 포함**: 정상 케이스, 에러 케이스, 경계값 케이스, E2E 사용자 플로우
- **각 구현 단계 항목 옆에 "→ Playwright MCP 테스트 후 다음 단계 진행" 명시**
- 예시를 위해 `/tasks` 디렉토리의 마지막 완료된 작업 참조
- 새 작업의 경우, 문서에는 빈 박스와 변경 사항 요약이 없어야 함. 초기 상태의 샘플로 `000-sample.md` 참조

3. **작업 구현**

- 작업 파일의 명세서를 따름
- 기능과 기능성 구현
- **🚨 Notion API 연동 및 콘텐츠 렌더링 구현 시 반드시 Playwright MCP로 테스트 수행 (선택 아닌 필수)**
- **🚨 구현 완료 = 코드 작성 + 테스트 통과. 테스트 없이 완료 처리 절대 금지**
- 구현 순서: 코드 작성 → `mcp__playwright__browser_navigate`로 앱 접속 → 기능 동작 검증 → 통과 시 다음 단계
- 각 단계 후 작업 파일 내 단계 진행 상황 업데이트
- 구현 완료 후 Playwright MCP를 사용한 E2E 전체 플로우 테스트 실행
- **테스트 통과 확인 후에만** 다음 단계로 진행 (실패 시 수정 후 재테스트)
- 각 단계 완료 후 중단하고 추가 지시를 기다림

4. **로드맵 업데이트**

- 로드맵에서 완료된 작업을 ✅로 표시

## 개발 단계

### Phase 1: 프로젝트 골격 ✅

- **예상 소요 시간**: 1-2일
- **Phase 목표**: Next.js 16 기반의 견고한 프로젝트 골격과 Notion API 연동을 위한 기반 환경을 구축한다.
- **왜 이 순서인가?**: 라우트 구조, 환경 변수, 타입 정의가 없으면 어떤 기능도 만들 수 없다. 모든 개발의 출발점이므로 가장 먼저 완성해야 한다.
- **완료 기준 (Done Criteria)**:
  - Next.js 16 App Router 기반 라우트 구조 및 공통 레이아웃이 배치되어 있다.
  - 도메인 타입(Post, Category, Tag)과 Notion 응답 타입이 정의되어 있다.
  - `NOTION_TOKEN`, `NOTION_DATABASE_ID` 등 서버 전용 환경 변수 검증 로직이 구성되어 있다.
  - `npm run dev`로 빈 골격 페이지가 정상 렌더링된다.

- **Task 001: 프로젝트 구조 및 라우팅 설정** ✅ - 완료
  - ✅ Next.js 16 App Router 기반 라우트 구조 생성
  - ✅ 루트 레이아웃 및 홈 페이지 골격 구현
  - ✅ 에러/로딩/404 페이지 파일 생성
  - ✅ Header/Footer, MainNav/MobileNav 등 공통 레이아웃 컴포넌트 골격 배치
  - ✅ TailwindCSS v4 + shadcn/ui(new-york) 초기 설정

- **Task 002: 타입 정의 및 환경 변수 구조 설계** ✅ - 완료
  - ✅ Post, Category, Tag 등 도메인 타입 정의
  - ✅ Notion API 응답 타입 및 변환 결과 타입 인터페이스 정의
  - ✅ 서버 전용 환경 변수 검증 로직 구성 (NOTION_TOKEN, NOTION_DATABASE_ID)
  - ✅ NEXT*PUBLIC* 접두사 금지 규칙 반영 및 빌드 타임 안전성 확보

### Phase 2: 공통 모듈

- **예상 소요 시간**: 2-3일
- **Phase 목표**: 모든 기능에서 재사용되는 Notion API 공통 함수, UI 컴포넌트, 더미 데이터 유틸리티를 먼저 구축하여 중복을 방지한다.
- **왜 이 순서인가?**: `fetchPages`, `PostCard`, `CategoryBadge` 같은 공통 코드를 기능별로 따로 만들면 중복이 생기고 수정 시 여러 곳을 고쳐야 한다. 공통 모듈을 먼저 만들어두면 Phase 3~4 기능 개발 속도가 크게 빨라진다.
- **완료 기준 (Done Criteria)**:
  - `fetchPages`, `fetchPageContent` 등 Notion API 공통 함수 뼈대가 `src/lib/notion/`에 구현되어 있다.
  - Header, Footer, PostCard, CategoryBadge, TagList 등 핵심 공통 컴포넌트가 더미 데이터로 렌더링된다.
  - 더미 Post 데이터 유틸리티가 구축되어 UI 개발이 API 없이도 가능하다.
  - 디자인 토큰 및 반응형 그리드(모바일 1열 / 태블릿 2열 / 데스크톱 3열) 기준이 확정되어 있다.

- **Task 003: 공통 컴포넌트 라이브러리 구현** - 우선순위
  - shadcn/ui 기반 Badge, Card, Input, Skeleton, Button 컴포넌트 추가
  - PostCard, PostList, CategoryBadge, TagList 구현
  - 더미 Post 데이터 생성 유틸리티 작성
  - 디자인 토큰 및 타이포그래피 스타일 가이드 적용

- **Task 006: Notion API 클라이언트 및 데이터 레이어 구축 (공통 함수 골격)**
  - `src/lib/notion/`에 client.ts, queries.ts, mappers.ts 구성
  - `fetchPages`(목록), `fetchPageContent`(상세) 공통 함수 시그니처 확정 및 구현
  - `notion.dataSources.query` 기반 조회 함수 구현
  - `isFullPage()` 헬퍼로 안전한 페이지 추출, Notion 속성 → Post 타입 매핑
  - `unstable_cache`로 `revalidate: 3600` 캐싱 전략 적용
  - 환경 변수 미설정 시 빈 데이터 반환으로 빌드 타임 안전성 보장
  - **[테스트 필수]** Playwright MCP로 공통 함수 호출 결과(목록/상세) 페이지 연동 동작 검증

### Phase 3: 핵심 기능

- **예상 소요 시간**: 3-4일
- **Phase 목표**: 블로그의 가장 기본이 되는 글 목록/상세 페이지와 Notion 콘텐츠 렌더링 파이프라인을 완성한다.
- **왜 이 순서인가?**: 글 목록과 상세 페이지가 없으면 블로그 자체가 성립하지 않는다. 검색·카테고리·SEO는 이 핵심 기능이 동작하는 것을 전제로 하기 때문에, 부가 기능보다 반드시 먼저 완성해야 한다.
- **완료 기준 (Done Criteria)**:
  - 홈 페이지에서 Notion 데이터 기반 글 목록이 반응형 카드 그리드로 표시된다.
  - `/posts/[slug]` 상세 페이지에서 Notion 콘텐츠가 Markdown + 코드 하이라이팅으로 렌더링된다.
  - 로딩/에러/404 상태 UI가 모든 주요 경로에서 정상 동작한다.
  - Playwright MCP로 목록 → 상세 진입 E2E 플로우가 통과한다.

- **Task 004: 홈/카테고리/상세 페이지 UI 완성**
  - 홈 페이지: 카드 그리드(모바일 1열 / 태블릿 2열 / 데스크톱 3열) + 검색 입력 + 카테고리 필터 UI
  - 카테고리 페이지: 필터링된 목록 UI
  - 글 상세 페이지: 커버 이미지, 제목, 메타 정보, 태그, 본문 영역 골격
  - 로딩/에러/404 상태 UI 완성
  - 반응형 및 접근성 검증

- **Task 007: Notion 블록 → Markdown 렌더링 파이프라인**
  - notion-to-md로 페이지 블록을 Markdown으로 변환
  - react-markdown + rehype-highlight로 코드 하이라이팅 적용
  - 이미지, 인용구, 리스트, 표 등 주요 블록 렌더링 검증
  - 상세 페이지의 더미 데이터를 실제 Notion 콘텐츠로 교체
  - **[테스트 필수]** Playwright MCP로 대표 글 렌더링 결과 E2E 검증

- **Task 007-1: 글 목록/상세 실데이터 연결 및 통합 검증**
  - 홈 페이지의 더미 Post를 실제 `fetchPages` 결과로 교체
  - 상세 페이지 라우트를 `fetchPageContent` 기반으로 연결
  - `generateStaticParams`로 글 상세 정적 경로 사전 생성
  - **[테스트 필수]** Playwright MCP로 목록 → 상세 진입 정상/에러(존재하지 않는 slug) 시나리오 검증

### Phase 4: 추가 기능

- **예상 소요 시간**: 2-3일
- **Phase 목표**: 핵심 기능 위에 카테고리 필터링, 검색, SEO 등 부가 기능을 추가해 블로그 완성도를 높인다.
- **왜 이 순서인가?**: 검색·카테고리 필터링·SEO는 글 목록과 상세 페이지가 완성된 후에야 의미가 있다. 핵심 기능 없이 검색을 먼저 만들면 연결할 데이터가 없고, SEO도 렌더링된 콘텐츠가 있어야 적용할 수 있다.
- **완료 기준 (Done Criteria)**:
  - `/categories/[category]` 경로에서 서버 사이드 필터링된 글 목록이 표시된다.
  - 홈 페이지 검색이 300ms debounce로 제목/태그/카테고리를 대소문자 무시하고 필터링한다.
  - 모든 주요 페이지에 `generateMetadata` 기반 SEO 메타 태그(OpenGraph, Twitter Card 포함)가 적용된다.
  - Playwright MCP로 카테고리 이동, 검색, 검색 결과 없음 시나리오가 모두 통과한다.

- **Task 005: 검색 UX 및 커스텀 훅 구현**
  - useDebounce, useSearchPosts 훅 구현
  - 검색 debounce 300ms, 대소문자 무시, 제목/태그/카테고리 대상 필터링 로직
  - 검색 결과 없음 상태 UI 처리
  - 더미 데이터 기준 동작 검증 후 Phase 3의 실데이터로 자연스럽게 연결

- **Task 008: 카테고리 필터링 및 검색 실데이터 연결**
  - `/categories/[category]` 서버 사이드 필터링 구현
  - 홈 페이지 검색을 실제 Post 데이터 기반 클라이언트 사이드 필터로 전환
  - `generateStaticParams`로 카테고리 정적 경로 사전 생성
  - **[테스트 필수]** Playwright MCP로 카테고리 이동/검색/존재하지 않는 카테고리 플로우 테스트

- **Task 009: SEO 및 메타데이터 최적화**
  - 각 페이지 `generateMetadata` 구현 (title, description, OpenGraph, Twitter Card)
  - 홈/카테고리/상세별 메타 태그 분기 처리
  - 시맨틱 HTML 구조 점검 및 Lighthouse SEO 점수 검증

- **Task 009-1: 핵심 기능 통합 테스트**
  - **[테스트 필수]** Playwright MCP로 전체 사용자 플로우 E2E 테스트
  - Notion API Rate Limit(3 req/s) 대응 및 캐시 적중 검증
  - 빈 데이터, 네트워크 실패, 존재하지 않는 slug 등 엣지 케이스 처리 확인

### Phase 5: 최적화 및 배포

- **예상 소요 시간**: 1-2일
- **Phase 목표**: 완성된 기능의 성능/반응형 품질을 높이고 Vercel에 안정적으로 배포한다.
- **왜 이 순서인가?**: 기능이 완성되기 전에 성능을 최적화하면 이후 기능 변경 시 최적화가 무효화될 수 있다. 모든 기능이 확정된 마지막 단계에 최적화와 배포를 묶어 진행하는 것이 효율적이다.
- **완료 기준 (Done Criteria)**:
  - Lighthouse Performance 90+, SEO 90+ 점수를 달성한다.
  - 모바일/태블릿/데스크톱 뷰포트에서 주요 페이지가 레이아웃 깨짐 없이 동작한다.
  - Vercel 프로덕션 도메인에서 HTTPS로 정상 접속되며 환경 변수가 설정되어 있다.
  - `npm run check-all`과 `npm run build`가 CI에서 통과한다.

- **Task 010: ISR 및 이미지 최적화**
  - ISR revalidate 전략 재검토 및 페이지별 최적 값 설정
  - `next/image`로 커버 이미지 최적화 및 Notion 이미지 도메인 허용 처리
  - 폰트 최적화(`next/font`) 및 번들 사이즈 점검
  - Lighthouse Performance 90+ 목표 달성

- **Task 010-1: 반응형 디자인 최종 점검**
  - 모바일(375px) / 태블릿(768px) / 데스크톱(1280px+) 3개 뷰포트 검증
  - 터치 타겟 크기, 가독성, 네비게이션 동작 점검
  - **[테스트 필수]** Playwright MCP 뷰포트 변경 시나리오로 주요 페이지 회귀 테스트

- **Task 011: Vercel 배포 및 운영 환경 구성**
  - Vercel 프로젝트 연결 및 환경 변수 설정(`NOTION_TOKEN`, `NOTION_DATABASE_ID`)
  - 프로덕션 도메인 연결 및 HTTPS 확인
  - `npm run check-all` 기반 CI 체크 및 Husky pre-commit 훅 검증
  - Vercel Analytics / 기본 모니터링 연결

## MVP 이후 확장 기능

MVP 출시 이후 사용자 피드백과 운영 데이터를 바탕으로 우선순위를 재조정하여 진행한다.

- **Task 012: 다크 모드 지원**
  - `next-themes` 기반 테마 토글 구현 및 디자인 토큰 다크 팔레트 확장

- **Task 013: RSS 피드 및 사이트맵 생성**
  - `/rss.xml`, `/sitemap.xml` 동적 생성 및 검색 엔진 등록

- **Task 014: SNS 공유 및 목차(TOC)**
  - 상세 페이지 공유 버튼 및 헤딩 기반 목차(스크롤 스파이) 추가

- **Task 015: 댓글 시스템 연동**
  - Giscus 등 GitHub Discussions 기반 댓글 시스템 연동

- **Task 016: 방문 통계 및 조회수**
  - Vercel Analytics 확장 또는 Plausible 연동, 글별 조회수 표시
