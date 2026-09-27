### Phase 1 — 프로젝트 초기화


```
# 1) 프로젝트 폴더 만들고 진입
mkdir r3f-study
cd r3f-study

# 2) package.json 자동 생성
#    -y 는 모든 질문에 yes 디폴트로 답하라는 옵션
npm init -y
```

`npm init -y`을 치고 나면 폴더 안에 `package.json`이 생겨. 열어보면 이런 모양일 거야:


```
{
  "name": "r3f-study",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

여기에 직접 손으로 두 가지를 추가/수정해줘:

1.**맨 아래에 `"type": "module"` 한 줄 추가** — Vite 설정 파일과 소스에서 `import` 문법을 쓰려면 ESM 모드여야 해.

2.**`"main": "index.js"` 는 삭제** (우리는 브라우저로 띄울 거라 entry가 따로 없음).

이렇게:


```
{
  "name": "r3f-study",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  }
}
```

### Phase 2 — 의존성 설치 (3번에 나눠서)

> **왜 나눠서?** `dependencies`는 런타임에 필요하고, `devDependencies`는 빌드할 때만 필요해. 의미가 다르니까 분리해서 설치해보는 게 좋아.


```
# 3) 빌드 도구 (개발할 때만 필요 → --save-dev)
npm install --save-dev vite @vitejs/plugin-react

# 4) React 런타임
npm install --save react react-dom

# 5) Three.js + R3F + 편의 라이브러리(drei)
npm install --save three @react-three/fiber @react-three/drei
```

실제로는 npm이 자동으로 버전을 잡아줄 거야. `package.json`이 이렇게 바뀌었는지 확인해봐:


```
"dependencies": {
  "react": "^18.x",
  "react-dom": "^18.x",
  "three": "^0.169.x",
  "@react-three/fiber": "^8.x",
  "@react-three/drei": "^9.x"
},
"devDependencies": {
  "vite": "^5.x",
  "@vitejs/plugin-react": "^4.x"
}
```

💡 **`--save` vs `--save-dev` 헷갈리면 이렇게 외우면 돼:**

- `--save` → `dependencies` → 앱이 **돌아가는 데** 필요 (브라우저로 같이 가야 함)
- `--save-dev` → `devDependencies` → **빌드/개발할 때만** 필요 (서버에서만 쓰임)

### Phase 3 — npm scripts 추가

`package.json`의 `scripts` 부분을 직접 손으로 채워줘:

```
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "preview": "vite preview"
}
```

- `npm run dev` → 개발 서버 (HMR 켜진 채로 코드 수정 즉시 반영)
- `npm run build` → `dist/` 폴더에 프로덕션 번들 생성
- `npm run preview` → 빌드 결과물 로컬에서 미리보기

### Phase 4 — 설정/엔트리 파일 만들기

직접 텍스트 에디터(VS Code 등)로 파일 4개를 만들어줘. 빈 폴더부터 시작하니까 천천히 한 번씩:

```
# 4-1) Vite 설정 파일 (이전 턴에서 보낸 vite.config.js 내용 복붙)
touch vite.config.js
```

```
# 4-2) HTML 엔트리 (이전 턴에서 보낸 index.html 내용 복붙)
touch index.html
```

```
# 4-3) src 폴더와 진입 JS
mkdir src
touch src/main.jsx
```

```
# 4-4) 스타일 (비워두고 시작)
touch src/index.css
```

> 💡 **왜 `main.jsx`인가?** React에서 `jsx` 확장자는 Vite가 자동으로 변환해줘. 진입점이 `.jsx`인 건 R3F/Vite 프로젝트 관례야. `.js`로 해도 되지만 React 컴포넌트가 들어가는 파일은 관례상 `.jsx`로 써.

### Phase 5 — 실행 & 확인

아직 `App.jsx`/`Shape.jsx`가 비어있으니 dev 서버는 빈 화면일 거야. 그게 정상이야. 그래도 일단 실행되는지 봐두자:

```
npm run dev
```

콘솔에 이런 게 뜨면 셋업 끝:


```
  VITE v5.x  ready in 407 ms
  ➜  Local:   http://localhost:5173/
```

이 상태에서 **다음 턴에 `src/main.jsx` → `src/App.jsx` → `src/Shape.jsx` → `src/index.css` 순서로 한 파일씩 타이핑하면서 채워나가자.** 그게 더 체감이 클 거야.

### 📝 요약 한눈에

|#|명령어|의미|
|---|---|---|
|1|`mkdir r3f-study && cd r3f-study`|작업 폴더|
|2|`npm init -y`|package.json 생성|
|2.5|`"type": "module"` 추가|ESM 모드 활성화|
|3|`npm i -D vite @vitejs/plugin-react`|빌드 도구|
|4|`npm i react react-dom`|React|
|5|`npm i three @react-three/fiber @react-three/drei`|3D 라이브러리|
|6|`scripts` 채우기|dev/build/preview|
|7|빈 파일 4개 생성|설정 + 엔트리|