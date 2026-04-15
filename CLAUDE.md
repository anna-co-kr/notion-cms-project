# Claude Code 개발 지침

**개인 개발 블로그**는 Notion을 CMS로 활용하여 글 작성만으로 블로그에 자동 반영되는 개인 기술 블로그입니다.

상세 프로젝트 요구사항은 @/docs/PRD.md 참조

## 핵심 기술 스택

- **Framework**: Next.js 16.2.3 (App Router + Turbopack)
- **Runtime**: React 19.1.0 + TypeScript 5
- **Styling**: TailwindCSS v4 + shadcn/ui (new-york style)
- **CMS**: @notionhq/client v5 (서버 전용, NEXT*PUBLIC* 금지)
- **콘텐츠**: notion-to-md + react-markdown + rehype-highlight
- **Forms**: React Hook Form + Zod
- **UI Components**: Radix UI + Lucide Icons
- **Development**: ESLint + Prettier + Husky + lint-staged

## 개발 가이드

- **개발 로드맵**: `@/docs/ROADMAP.md`
- **프로젝트 요구사항**: `@/docs/PRD.md`
- **프로젝트 구조**: `@/docs/guides/project-structure.md`
- **스타일링 가이드**: `@/docs/guides/styling-guide.md`
- **컴포넌트 패턴**: `@/docs/guides/component-patterns.md`
- **Next.js 16.2.3 전문 가이드**: `@/docs/guides/nextjs-16.md`
- **폼 처리 완전 가이드**: `@/docs/guides/forms-react-hook-form.md`

## 자주 사용하는 명령어

```bash
# 개발
npm run dev         # 개발 서버 실행 (Turbopack)
npm run build       # 프로덕션 빌드
npm run check-all   # 모든 검사 통합 실행 (권장)

# UI 컴포넌트
npx shadcn@latest add button    # 새 컴포넌트 추가
```

## 중요 보안 규칙

- `NOTION_TOKEN`은 서버 전용 환경 변수 — `NEXT_PUBLIC_` 접두사 절대 사용 금지
- `NOTION_DATABASE_ID`도 동일하게 서버 전용으로 관리

## Notion API 규칙 (@notionhq/client v5)

- 데이터베이스 쿼리: `notion.dataSources.query({ data_source_id: DATABASE_ID, ... })`
- 페이지 필터링: `isFullPage()` 헬퍼 함수로 전체 페이지 응답만 추출
- ISR 캐싱: `unstable_cache` 사용, `revalidate: 3600` (1시간)
- 빌드 타임 안전성: 환경 변수 미설정 시 빈 데이터 반환으로 빌드 오류 방지

## 작업 완료 체크리스트

```bash
npm run check-all   # 모든 검사 통과 확인
npm run build       # 빌드 성공 확인
```

상세 규칙은 위 개발 가이드 문서들을 참조하세요
