# CLAUDE.md

이 파일은 Claude Code(claude.ai/code)가 이 저장소에서 작업할 때 참고하는 가이드입니다.

## 프로젝트 개요

HTML/CSS로 작성한 개인 이력서. 브라우저 인쇄(`Cmd+P` → "PDF로 저장")로 A4 사이즈 PDF를 생성한다. PDF 안의 링크(`mailto:`, `tel:`, 외부 URL)가 그대로 클릭 가능하도록 표준 `<a href>` 태그만 사용한다.

## 기술 스택

- **빌드**: Vite (vanilla 템플릿)
- **언어**: HTML5, CSS3 (JS 없음 — 라이브 리로드 용도로만 Vite 사용)
- **폰트**: Pretendard Variable (로컬, `public/fonts/PretendardVariable.woff2`)
- **PDF 출력**: Chrome 브라우저 인쇄

## 폴더 구조

```
.
├── index.html              # 이력서 본문 (단일 페이지, semantic markup)
├── package.json
├── vite.config.js
├── public/
│   ├── fonts/
│   │   └── PretendardVariable.woff2
│   └── images/             # 회사·학교 로고 (tripleauth.png, bgzt.png, ryencatchers.png, inu_univ.png)
└── src/styles/
    ├── reset.css           # modern-normalize 간략화
    ├── main.css            # 폰트·타이포·레이아웃 (화면 + 인쇄 공통)
    └── print.css           # @page, @media print 전용
```

## 개발 워크플로

### 일반 디자인 작업

```bash
npm run dev    # http://localhost:5173/
```

HMR로 즉시 반영. 화면에서 A4 비율(210mm × 297mm)이 시뮬레이션되어 보임.

### PDF 출력 검증 ⚠️ 중요

**dev 서버의 HMR/캐시가 print.css 변경을 안 반영하는 경우가 있다.** PDF 추출 시 변경이 안 보이면 99% 이 케이스. 빌드 결과를 별도 서버로 띄워서 검증:

```bash
npm run build && npm run preview   # http://localhost:4173/
```

이 경로는 CSS 파일에 hash가 붙어(`index-XXXXXXXX.css`) 캐시 우회가 자동으로 됨.

## 스타일 아키텍처

CSS 로드 순서: `reset.css → main.css → print.css`. 후행 파일이 우선권을 갖는다.

### `break-*` 정책 — **가장 중요**

페이지 break 관련 속성(`break-inside`, `break-after`, `page-break-*`)은 **반드시 `print.css`에서만 관리**한다.

> 이전에 main.css에 `break-inside: avoid`가 일반 규칙(미디어 쿼리 밖)으로 박혀 있어 화면·인쇄 양쪽에 적용된 결과, 인쇄 시 큰 단위가 통째로 다음 페이지로 밀려서 빈 공간이 생기는 문제가 있었다. main.css에 break 규칙을 절대 추가하지 말 것.

#### 현재 정책 (페이지 꽉 채움 + 의미 단위 보호)

| 단위                                     | 처리                  | 이유                                |
| ---------------------------------------- | --------------------- | ----------------------------------- |
| `.psr li` (PSR 개별 항목)                | `break-inside: avoid` | 항목 한 줄만 떨어지면 가독성 ↓      |
| `.project-meta` (회색 메타박스)          | `break-inside: avoid` | 자르면 어색                         |
| `table.simple tr`                        | `break-inside: avoid` | 테이블 행 자르면 어색               |
| 제목 (`h1~h5`)                           | `break-after: avoid`  | 제목만 페이지 끝에 외로이 안 남도록 |
| `.topic-title` (형광펜)                  | `break-after: avoid`  | 형광펜 라인 다음 PSR과 함께         |
| `.psr` 묶음·그 외 (`.project-block` 등)  | 자연 흐름             | 페이지를 꽉 채우도록 경계 가로지름  |

### 디자인 토큰 (main.css의 `:root`)

- 페이지: `--page-width: 210mm`, `--page-height: 297mm`, padding 18mm × 16mm
- 색상: `--color-text: #1a1a1a` (본문), `--color-muted: #6b6b6b`, `--color-hairline: #e5e5e5`
  - 프로젝트 제목(h3)만 `#0a0a0a`로 더 진하게
- 폰트: `--font-body: 'Pretendard Variable', ...`
- 타이포: `--fs-body: 10pt`, `--lh-body: 1.55`

### 디자인 톤

- **미니멀 / 클래식**: 흑백 위주, 단일 산세리프 폰트
- **형광펜 강조**: 토픽 타이틀(소제목)에만 `<mark>` 태그로 옅은 노랑(`#fdf5d3`) — 면접관 시선 유도
- **좌측 막대**: 토픽 타이틀에 검은 세로 막대(3px) + `padding-left: 12px`
- **외부 링크 표시**: 옅은 밑줄(`text-decoration: underline; color: #d0d0d0`) + ↗ SVG 아이콘 (h3 내부 `a[href^="http"]` 만)
- **회사·학교 로고**: 22×22px, `border-radius: 5px`, 흰 배경 + hairline 보더
  - 어두운 로고(번개장터)는 `.org-logo--dark` 클래스로 검은 배경
- **PSR 마커**: 짧은 가로선(―) 대신 작은 점(•) 4×4px

## PDF 출력 가이드 (Chrome)

1. http://localhost:4173/ 또는 5173/ 접속
2. `Cmd+P`
3. 옵션:
   - 대상: **PDF로 저장**
   - 용지: **A4** (Letter 아님)
   - 여백: **기본값** (CSS `@page` margin 18mm × 16mm 적용되게)
   - 배율: 기본값 (100%)
   - 옵션 → **배경 그래픽: ✅ 켜기** (필수 — 안 켜면 형광펜·로고 배경·메타박스 회색 빠짐)
   - 옵션 → 머리글 및 바닥글: ☐ 끄기

## 자주 마주칠 문제

| 증상                                          | 원인                                         | 해결                                                               |
| --------------------------------------------- | -------------------------------------------- | ------------------------------------------------------------------ |
| 페이지 사이 빈 공간 큼                        | `break-inside: avoid`가 너무 큰 단위에 걸림  | print.css에서 작은 단위만 유지. main.css에는 break 규칙 두지 말 것 |
| 형광펜·로고 색 PDF에서 빠짐                   | Chrome 인쇄에서 "배경 그래픽" 꺼짐           | 인쇄 다이얼로그에서 켜기                                           |
| 링크 클릭 안 됨                               | CSS pseudo로만 텍스트를 만들었거나 형식 잘못 | 표준 `<a href>` 태그만 사용                                        |
| `getComputedStyle()`로 확인 시 의도와 다른 값 | 화면(default media) 기준 결과임              | DevTools "Emulate CSS Media Type: print"로 전환 후 확인            |

## 콘텐츠 수정 시 패턴

### 새 프로젝트 추가

```html
<article class="project-block">
  <h3>
    <a href="https://..." target="_blank" rel="noopener noreferrer"
      >프로젝트명 — 부제</a
    >
  </h3>
  <dl class="project-meta">
    <dt>설명</dt>
    <dd>한 줄 설명</dd>
    <dt>진행 기간</dt>
    <dd>YYYY.MM &ndash; YYYY.MM</dd>
    <dt>기술 스택</dt>
    <dd>
      <span class="tech-stack"><code>React</code><code>Next.js</code>...</span>
    </dd>
  </dl>

  <div class="topic">
    <p class="topic-title"><mark>토픽 한 줄 요약</mark></p>
    <div class="psr">
      <h5>문제점</h5>
      <ul>
        <li>...</li>
      </ul>
    </div>
    <div class="psr">
      <h5>해결 과정</h5>
      <ul>
        <li>...</li>
      </ul>
    </div>
    <div class="psr">
      <h5>결과</h5>
      <ul>
        <li>...</li>
      </ul>
    </div>
  </div>
</article>
```

### 새 회사 로고 추가 (경력 테이블)

- 이미지 파일을 `public/images/`에 배치
- `<img src="/images/파일명.png" alt="회사명 로고" class="org-logo" />` (또는 어두운 톤이면 `class="org-logo org-logo--dark"`)
- `(주)` 같은 prefix는 `org-name` span 안에 텍스트로

## 한국어 표기

- "콘텐츠" (O) / "컨텐츠" (X) — 외래어 표기법 기준
