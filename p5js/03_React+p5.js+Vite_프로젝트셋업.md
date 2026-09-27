## 사전 준비
Node.js와 npm이 설치된 상태에서 진행합니다.
```bash
node -v
npm -v
```
터미널에서 위 명령으로 버전을 확인하고 필요하면 최신 버전으로 업데이트합니다.
개발 폴더를 정합니다. 

---
## 1단계: Vite + React 프로젝트 생성
먼저 Vite를 이용해 React 프로젝트의 뼈대를 만듭니다.
### 1. 프로젝트 생성
터미널에서 아래의 명령어를 입력하세요. `my-p5-app`를 원하는 프로젝트 이름으로 바꿉니다.
```bash
npm create vite@latest my-p5-app -- --template react
```
### 2. 프로젝트 폴더로 이동한 후 패키치 설치
생성된 폴더로 이동한 후, React에 필요한 기본 패키지를 설치합니다.
```bash
cd my-p5-app
npm install
```
이렇게 하면 기본 폴더가 생성되어 React의 준비는 끝납니다.

---
## 2단계: p5.js와 React Wrapper 설치
이제 p5.js를 React에서 사용할 수 있도록 연결해주는 라이브러리를 설치합니다.
### p5.js 코어 라이브러리와 react-p5 설치
`p5`는 p5.js의 본체이고, `react-p5`는 React 컴포넌트에서 p5.js를 쉽게 사용할 수 있도록 도와주는 래퍼 라이브러리입니다.
```bash
npm install p5 react-p5
```
---
## 3단계: p5.js 스케치 컴포넌트 만들기
### 1. `src` 폴더 안에 `components` 폴더 생성
이 폴더에 컴포넌트들을 모아둡니다.
### 2. `Sketch.js` 파일을 생성하고 코드 작성
`src/components` 폴더 안에 `Sketch.js` 파일을 생성하고, 아래 코드를 붙여넣으세요. 이 코드가 바로 사용자님의 p5.js 로직을 React 컴포넌트로 감싼 형태입니다.
```javascript
// src/components/Sketch.js
import React from 'react';
import dynamic from 'next/dynamic'; // Next.js가 아닌 경우 이 줄은 필요 없습니다.
import Sketch from 'react-p5';

// window 객체에 접근하기 위해 dynamic import를 사용할 수도 있지만,
// Vite 환경에서는 일반 import로도 잘 동작합니다.
// 아래 코드는 Vite/Create React App 환경에 맞춘 일반적인 형태입니다.

export default function SketchComponent() {
  // p5.js의 setup 함수
  const setup = (p5, canvasParentRef) => {
    p5.createCanvas(p5.windowWidth, p5.windowHeight).parent(canvasParentRef);
  };

  // p5.js의 draw 함수
  const draw = (p5) => {
    p5.background(30, 30, 30);
    p5.fill(90, 200, 200);
    p5.noStroke();
    p5.ellipse(p5.width / 2, p5.height / 2, 500, 500);
  };

  // p5.js의 windowResized 함수
  const windowResized = (p5) => {
    p5.resizeCanvas(p5.windowWidth, p5.windowHeight);
  };

  return (
    <Sketch setup={setup} draw={draw} windowResized={windowResized} />
  );
}
```
- `react-p5`의 `<Sketch>` 컴포넌트를 사용합니다.
- `setup`, `draw`, `windowResized` 같은 p5.js 함수들을 `<Sketch>` 컴포넌트의 `props`로 넘겨줍니다.
- 각 함수의 첫 번째 인자로 p5 객체가 들어옵니다. 이 객체를 통해 `p5.createCanvas`, `p5.background` 등 모든 p5.js 기능에 접근해야 합니다. (이것이 바로 인스턴스 모드의 핵심입니다!)
- `setup` 함수의 두 번째 인자인 `canvasParentRef`는 p5.js 캔버스가 그려질 부모 요소를 지정해줍니다.
---

## 4단계: 메인 App 컴포넌트에 스케치 추가하기
이제 방금 만든 `SketchComponent`를 애플리케이션의 메인 화면에 띄워보겠습니다.

### `src/App.jsx` 파일 수정
```javascript
// src/App.jsx
import React from 'react';
import SketchComponent from './components/Sketch'; // 방금 만든 스케치 컴포넌트 import
import './css/style.css'; // 사용자님의 CSS 파일 import

function App() {
  return (
    <div className="App">
      <SketchComponent />
    </div>
  );
}

export default App;
```
- 기존에 있던 Vite 로고나 텍스트들은 모두 지우고, `<SketchComponent />`만 남깁니다.
- `import './css/style.css';` 경로가 실제 파일 위치와 맞는지 확인하세요. (만약 `src/css` 폴더 안에 있다면 `import './css/style.css';`가 맞습니다.)

---
## 5단계: 개발 서버 실행
터미널에서 아래의 명령으로 개발 서버를 실행하여 결과를 확인합니다.
```bash
npm run dev
```
터미널에 `Local: http://localhost:5173/` 과 같은 주소가 나타납니다. 이걸 `Cmd+클릭` 해서 웹 브라우저로 엽니다.

---
### 프로젝트의 구조
> ```
my-p5-app/
├── .gitignore
├── index.html
├── package.json
├── package-lock.json
├── public/
├── src/
│   ├── components/
│   │   └── Sketch.js  <-- 우리가 만든 p5.js 컴포넌트
│   ├── css/
│   │   └── style.css
│   ├── App.jsx        <-- 스케치를 렌더링하는 메인 컴포넌트
│   └── main.jsx       <-- React 앱을 시작하는 파일
└── vite.config.js

이제 이 환경에서 자유롭게 React의 상태 관리나 다른 컴포넌트들과 p5.js를 연동할 수 있습니다.