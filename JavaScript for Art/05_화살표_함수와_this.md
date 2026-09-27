[[00_CreativeCoding_핵심문법]]
## 개념 설명

화살표 함수(`() => {}`)는 단순히 타이핑을 줄여주는 것이 아닙니다. 핵심은 `this` 바인딩입니다.

* 일반 함수: 호출하는 위치에 따라 `this`가 변합니다. (이벤트 리스너 등에서 자주 바뀜)
* 화살표 함수: 자신이 선언된 곳의 `this`를 그대로 가져다 씁니다(Lexical this). 즉, `this`가 바뀌지 않습니다.

## p5.js 예시 : 타이머와 콜백

상황: `setTimeout`을 사용하여 일정 시간 뒤에 스케치 내부의 변수를 바꾸고 싶을 때, 일반 함수를 쓰면 `this`가 윈도우 객체를 가리키거나 `undefined`가 되어 오류가 납니다.

```javascript
let x = 0;

function setup() {
  createCanvas(400, 400);
  
  // 1초 뒤에 x를 100으로 이동시키는 코드
  setTimeout(() => {
    // 화살표 함수를 썼으므로, 이 안의 코드는 바깥쪽(전역/스케치) 스코프를 그대로 공유합니다.
    x = 100; 
    console.log("Moved!");
  }, 1000);
}

function draw() {
  ellipse(x, 200, 50, 50);
}
```

***

## Three.js 예시 : 클래스 내부의 애니메이션 루프

상황: Three.js 코드를 클래스로 구조화할 때 가장 많이 겪는 문제입니다. `requestAnimationFrame`에 메서드를 넘길 때 `this`를 잃어버리는 현상을 막습니다.

```javascript
class App {
  constructor() {
    this.scene = new THREE.Scene();
    // ... 카메라, 렌더러 설정 생략 ...
    
    // [중요] 화살표 함수로 메서드를 정의하거나
    // constructor에서 .bind(this)를 해야 합니다.
    this.animate(); 
  }

  // 메서드를 화살표 함수로 정의하면 'this'는 항상 App 인스턴스(나 자신)를 가리킵니다.
  animate = () => {
    requestAnimationFrame(this.animate); // 다음 프레임 요청
    
    // 여기서 'this.scene'에 접근 가능!
    // 일반 함수였다면 this가 undefined이거나 window라서 에러 발생
    this.renderer.render(this.scene, this.camera);
  }
}

new App();
```

***
