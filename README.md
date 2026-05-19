# huurray resume

> Personal HTML/CSS resume — A4 PDF-ready via browser print

HTML/CSS로 작성한 개인 이력서. 브라우저 인쇄(`Cmd+P` → "PDF로 저장")로 A4 PDF를 생성하며, PDF 안의 모든 링크가 그대로 클릭 가능하다.

## 기술 스택

- **Vite** (vanilla 템플릿, 라이브 리로드 용도)
- **HTML5 / CSS3** — JS 없음
- **Pretendard Variable** (로컬 폰트 임베드)

## 시작하기

### 개발 서버 (디자인 작업용)

```bash
npm install
npm run dev
# http://localhost:5173/
```

### PDF 출력용 빌드 + 미리보기 (권장)

```bash
npm run build
npm run preview
# http://localhost:4173/
```

`Cmd+P` → "PDF로 저장":
- 용지: **A4**
- 여백: **기본값** (CSS `@page` 18mm × 16mm 적용)
- 옵션 → **배경 그래픽: 켜기** ← 필수 (형광펜·로고 배경 보존)
- 옵션 → 머리글 및 바닥글: 끄기

## 폴더 구조

```
.
├── index.html              # 이력서 본문 (semantic markup)
├── public/
│   ├── fonts/              # Pretendard Variable
│   └── images/             # 회사·학교 로고
└── src/styles/
    ├── reset.css           # 브라우저 기본 스타일 정리
    ├── main.css            # 폰트·타이포·레이아웃 (화면 + 인쇄 공통)
    └── print.css           # @page, @media print
```

## 디자인 특징

- **A4 시뮬레이션**: 화면에서도 210mm × 297mm 비율로 미리보기
- **미니멀 / 클래식 톤**: 흑백 위주, 단일 산세리프(Pretendard)
- **형광펜 강조**: 핵심 토픽 라인에 옅은 노랑 `<mark>` 태그 — 면접관 시선 유도용
- **외부 링크 표시**: 옅은 밑줄 + ↗ SVG 아이콘 (PDF에서 클릭 가능)
- **회사·학교 로고**: 22×22px 둥근 사각형, 어두운 로고는 검은 배경으로 자동 대응
- **자연 페이지 흐름**: 의미 단위(문제점/해결/결과, 메타박스)만 통째로 유지, 그 외엔 페이지 경계를 자연스럽게 가로지름 → 빈 공간 최소화

## 콘텐츠 구성

총 5페이지 A4:
1. 헤더 · 자기소개 · 경력 · 첫 프로젝트
2. ~ 4. 프로젝트 4개 + 오픈소스 1개 (문제점 → 해결 과정 → 결과 구조)
5. 참여 프로젝트 목록 · 학력 · 링크

## License

Personal use only.
