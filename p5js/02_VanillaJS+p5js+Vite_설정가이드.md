## 사전 준비
Node.js와 npm이 설치된 상태에서 진행합니다.
```bash
node -v
npm -v
```
터미널에서 위 명령으로 버전을 확인하고 필요하면 최신 버전으로 업데이트합니다.
개발 폴더를 정합니다. 

---
## 1단계: Vite Vanilla JS 프로젝트 생성
React나 Vue가 아닌, 순수 JavaScript(vanilla) 템플릿을 사용해 프로젝트를 생성합니다.
### 1. 프로젝트 생성
터미널에서 다음 명령어를 실행하세요.
`my-vanilla-p5-app` 부분은 **원하는 프로젝트 이름**으로 바꿀 수 있습니다.
```bash
npm create vite@latest my-vanilla-p5-app -- --template vanilla
```
### 2. 프로젝트 폴더로 이동 후 의존성 설치
생성된 폴더로 이동한 후, 필요한 패키지를 설치합니다.
```bash
cd my-vanilla-p5-app
npm install
```
이제 기본적인 Vanilla JavaScript 개발 환경이 준비되었습니다.

---
## 2단계: p5.js 설치
p5.js를 npm으로 설치합니다. 모듈 방식으로 사용할 수 있도록 합니다.
```bash
npm install p5
```
설치 후, `main.js`나 별도의 sketch 파일에서 p5를 `import`해 사용할 수 있습니다.
(예: `import p5 from 'p5';` 또는 `import 'p5';` — 사용 방식은 선호하는 패턴에 따라 달라질 수 있음)

---
## 3단계: 보일러플레이트 코드
### 1. `src/main.js`
```javascript
import p5 from "p5";
import "./style.css";

const sketch = (p) => {
  let t = 0; // 시간 변수

  p.setup = () => {
    p.createCanvas(400, 400).parent("p5-container");
    p.noStroke();
  };

  p.draw = () => {
    p.background(0);
    p.fill(255);

    // 사인함수를 이용해 반지름 계산
    const baseRadius = 80; // 기본 크기
    const amplitude = 40;  // 크기 변화 폭
    const speed = 0.05;    // 속도 (값이 클수록 빠름)

    const radius = baseRadius + amplitude * p.sin(t);

    // 화면 중앙에 원 그리기
    p.circle(p.width / 2, p.height / 2, radius * 2);

    // 시간 증가
    t += speed;
  };
};

new p5(sketch);
```


### 2. `index.html`
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>P5.js v2.0.5 + Vite</title>
  </head>
  <body>
    <div id="p5-container"></div>
    <script type="module" src="/src/main.js"></script>
  </body>
</html>
```

### 3. `style.css`
```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  width: 100%;
  height: 100%;
  overflow: hidden;
  background: #000000;
}

#p5-container {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

canvas {
  display: block;
}
```

---

## 4단계: 개발 서버 실행
터미널에서 아래의 명령으로 개발 서버를 실행하여 결과를 확인합니다.
```bash
npm run dev
```
터미널에 `Local: http://localhost:5173/` 과 같은 주소가 나타납니다. 이걸 `Cmd+클릭` 해서 웹 브라우저로 엽니다.

---
### 프로젝트의 구조
> ```
my-vanilla-p5-app/
├── .gitignore
├── index.html
├── package.json
├── package-lock.json
├── public/
└── src/
    ├── main.js      <-- p5.js 코드가 들어간 메인 파일
    └── style.css    <-- CSS 파일

이 환경은 p5.js로 프로토타이핑을 빠르게 하거나, 복잡한 프레임워크 없이 인터랙티브 아트워크를 제작하기에 이상적인 선택입니다.

---
## p5.js 2.0 개발 방식 변경

https://github.com/processing/p5.js/wiki/Global-and-instance-mode
### 1. ES Modules의 등장
```javascript
// v1.x: UMD 방식 (전역에 자동 등록)
<script src="p5.js"></script>
// setup(), draw() 함수가 자동으로 전역에서 인식됨

// v2.x: ES Modules 방식
import p5 from 'p5';
// setup(), draw()이 전역에 자동 등록되지 않음
```
### 2. 번들러의 모듈 격리
Vite/Webpack 등의 모듈 시스템에서는 각 파일이 독립된 스코프를 가짐 → 전역 함수가 자동으로 등록되지 않음

### 3. p5.js의 디자인 철학 변화
- `v1.x`: 쉬운 시작을 위한 글로벌 폴리시
- `v2.x`: 모던 자바스크립트와 모듈 시스템 준수

### 왜 `p.` 접두사가 필요한가?
```javascript
// 모듈 환경에서는 이게 안되는 이유:
function setup() {   	  // ⛔ 이 함수는 p5 인스턴스와 연결되지 않음
  createCanvas(400, 400); // ⛔ createCanvas가 어떤 p5 인스턴스인지 모름
}

// 대신 이렇게 해야함:
new p5((p) => {      		  // ✅ p5 인스턴스 생성
  p.setup = () => {  		  // ✅ 이 setup은 p 인스턴스와 연결됨
    p.createCanvas(400, 400); // ✅ p 인스턴스의 createCanvas 사용
  };
});
```
---