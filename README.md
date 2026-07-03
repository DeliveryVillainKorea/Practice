# mori

모바일 웹 기반 콘텐츠/상점 소개 사이트. 에피소드 콘텐츠와 연계된 상점 정보를 보여주고, 관리자 페이지에서 배너·에피소드·상점 데이터를 관리할 수 있다.

배포 주소: https://mori-silk.vercel.app

## 폴더 구조

```
.
├── home.html        # 메인 페이지 (배너, 에피소드/상점 목록)
├── store.html        # 상점 상세 페이지
├── content.html      # 에피소드 콘텐츠 페이지
├── admin.html        # 관리자 페이지 (배너/에피소드/상점 CRUD)
├── api/
│   └── og.js         # Vercel 서버리스 함수 — URL의 OG 메타데이터 스크래핑
├── assets/            # 이미지, 아이콘, svg 등 정적 리소스
└── stores.json        # 초기 시드용 샘플 데이터 (현재 코드에서는 미사용)
```

각 HTML 페이지는 Supabase REST API를 직접 호출해 데이터를 읽고 쓰며, `api/og.js`는 관리자 페이지에서 배너/상점 URL을 입력할 때 미리보기(OG 이미지·제목 등)를 가져오는 데 쓰인다.

## 사전 준비

- Node.js 24.x (package.json의 `engines` 기준)
- Vercel CLI (서버리스 함수인 `api/og.js`를 로컬에서 테스트하려면 필요)

```bash
npm install
```

## 실행 방법

### 1. 정적 페이지만 확인할 때

`api/og.js`를 쓰지 않는 화면(관리자 페이지의 미리보기 기능 제외)은 정적 파일 서버만으로 충분하다.

```bash
npx serve .
# 또는
python3 -m http.server 5500
```

브라우저에서 `http://localhost:5500/home.html` 등으로 접속.

### 2. API(og.js)까지 포함해서 실행할 때

`api/` 폴더는 Vercel 서버리스 함수 컨벤션을 따르므로, Vercel CLI로 로컬 개발 서버를 띄워야 `/api/og` 엔드포인트가 동작한다.

```bash
npm install -g vercel   # 최초 1회
vercel dev
```

기본적으로 `http://localhost:3000`에서 정적 페이지와 `/api/og` 엔드포인트를 함께 확인할 수 있다.

> 주의: 현재 `home.html`, `admin.html` 등에는 배포된 주소(`https://mori-silk.vercel.app/api/og`)가 하드코딩되어 있어, 로컬에서 `vercel dev`로 띄워도 실제 요청은 프로덕션 API로 나간다. 로컬 API를 직접 테스트하려면 해당 상수를 `http://localhost:3000/api/og`로 임시 수정해야 한다.

## 데이터베이스

Supabase 프로젝트에 직접 REST API로 접근한다 (`home.html`, `admin.html` 내 `SUPABASE_URL`, `SUPABASE_KEY` 상수 참고). 별도의 백엔드 서버 없이 클라이언트에서 바로 조회/수정한다.

## 배포

Vercel에 연결되어 있으며, `api/` 폴더 아래 파일이 자동으로 서버리스 함수로 배포된다.
