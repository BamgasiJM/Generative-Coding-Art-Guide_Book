# 원·선·색을 만드는 두 가지 사고법

[[Generative Art Concepts]]

**초록**

코딩 아트에서 원을 그리고, 두 점을 선으로 연결하고, 영역에 색을 부여하는 행위는 가장 단순한 출발점처럼 보인다. 그러나 이 행위가 코드로 번역되는 방식은 도구에 따라 근본적으로 다르다. Processing, p5.js, openFrameworks, nannou, Quil 같은 크리에이티브 코딩 라이브러리는 대체로 창작자가 도형 명령을 호출하면 런타임이 기하 생성과 래스터화를 처리하는 드로잉 API의 계보에 속한다. 반면 GLSL, HLSL, OSL은 좌표·벡터·거리·광학적 속성을 계산해 각 셰이딩 지점의 값을 정하는 셰이딩 언어다. 이 글은 두 계보를 ‘원을 어떻게 만드는가’, ‘선을 무엇으로 정의하는가’, ‘색이 어디에 존재하는가’라는 세 질문으로 비교한다. 분석 결과, 드로잉 API는 객체를 장면에 배치하는 명령형 모델을 통해 빠른 시각 실험과 상호작용 설계를 돕고, 셰이딩 언어는 공간 자체를 함수로 기술하여 변형·반복·조명·재질을 연속적인 계산으로 다룬다. 특히 OSL은 GLSL/HLSL과 문법적으로 유사하더라도, 화면의 최종 픽셀 색보다 표면의 산란 특성을 기술하는 데 중심을 둔다는 점에서 구분된다. 본고는 각 도구의 최소 예제와 실행 모델을 함께 제시하여, 코딩 아트의 도구 선택을 기능 목록이 아닌 공간을 사고하는 방식의 선택으로 재해석한다.

**주제어:** creative coding, Processing, p5.js, openFrameworks, nannou, Quil, GLSL, HLSL, OSL, signed distance field

---

## 1. 서론: 왜 원·선·색을 다시 질문하는가

코딩 아트 입문에서 가장 먼저 만나는 문장은 대개 `circle(...)`, `line(...)`, `fill(...)`과 비슷하다. 이 문장들은 직관적이다. 중심 좌표와 반지름을 주면 원이 생기고, 두 점을 주면 선이 생기며, 색을 지정하면 도형 내부가 채워진다. 이 간결함은 중요한 장점이다. 창작자는 렌더링 파이프라인의 세부를 몰라도 반복, 무작위성, 시간, 입력 장치를 빠르게 시각 형식으로 바꿀 수 있다.

하지만 같은 ‘원’이 모든 환경에서 같은 존재는 아니다. Processing의 `ellipse()`는 도형을 화면에 그리라는 고수준 명령이다. p5.js의 `circle()`도 같은 계열의 명령이지만 브라우저의 이벤트, DOM, 캔버스와 직접 만난다. openFrameworks의 `ofDrawCircle()`은 C++ 애플리케이션 안에서 하드웨어, 카메라, 오디오, 셰이더 파이프라인으로 이어질 수 있다. nannou의 `draw.ellipse()`는 Rust의 타입 및 빌더 패턴과 결합해 도형 속성을 조합한다. Quil에서는 Processing 계열의 드로잉 동사가 Clojure의 불변 데이터와 함수 변환 흐름 속에 놓인다.

반대로 GLSL이나 HLSL의 프래그먼트/픽셀 셰이더에는 보통 `circle()`이라는 표준 원 명령이 없다. 대신 현재 픽셀 좌표에서 중심까지의 거리를 계산하고, 그 거리가 반지름보다 작은지 판정한다. 원은 ‘그려진 객체’라기보다 거리 함수가 만드는 영역이다. OSL에서도 원형 패턴을 만들 수 있으나, 그것은 대개 2차원 프레임버퍼 위의 도형이 아니라 3차원 물체 표면의 좌표에서 계산되는 마스크이며, 이후 Base Color, Roughness, Emission 또는 셰이더 closure에 연결되는 재질 신호가 된다.

따라서 이 글의 목적은 특정 도구의 우열을 가리는 데 있지 않다. 첫째, 드로잉 API와 셰이딩 언어가 원·선·색을 어떤 계산 모델로 다루는지 구분한다. 둘째, Processing, p5.js, openFrameworks, nannou, Quil의 API 설계가 언어 및 창작 워크플로우와 어떻게 결합하는지 살핀다. 셋째, GLSL·HLSL·OSL에서 거리장, 프래그먼트 출력, 재질 closure가 만드는 차이를 설명한다. 이 비교는 ‘어떤 명령을 외울 것인가’보다 ‘어떤 종류의 공간을 만들고 싶은가’를 먼저 질문하기 위한 토대다.

---

## 2. 분석 틀: 객체를 그리는 명령과 공간을 계산하는 함수

### 2.1 드로잉 API: 장면에 도형을 배치하는 모델

드로잉 API의 전형적인 흐름은 다음과 같다.

1. 캔버스 또는 윈도우를 만든다.
2. 배경, 채우기, 외곽선, 선 두께 같은 스타일 상태를 정한다.
3. 원·선·사각형 같은 기본 도형을 호출한다.
4. 프레임 루프에서 이 과정을 갱신하거나 반복한다.

여기에서 창작자가 작성하는 문장은 대체로 ‘무엇을 어디에 그릴 것인가’다. `ellipse(x, y, w, h)`는 위치와 크기를 인자로 받고, `line(x1, y1, x2, y2)`는 끝점을 받는다. 내부적으로는 라이브러리와 그래픽스 백엔드가 도형을 적절한 기하 또는 픽셀 작업으로 바꾼다. 즉, 사용자는 도형의 내부를 판정하는 매 픽셀의 식을 직접 작성하지 않아도 된다.

이 모델의 장점은 즉시성이다. 원 100개를 만들려면 원의 중심과 반지름을 생성하는 루프를 쓴 뒤 원 명령을 반복 호출하면 된다. 레이어는 보통 코드의 호출 순서로 정해진다. 반면 도형 수가 커지면 반복 호출, CPU-GPU 전송, 드로우콜, 버퍼 구성 같은 문제를 의식해야 한다. 특히 실시간 설치나 대규모 입자 작업에서는 ‘도형 명령을 얼마나 많이 호출했는가’가 성능 설계의 일부가 된다.

### 2.2 셰이딩 언어: 각 지점의 값을 계산하는 모델

셰이딩 언어는 다른 질문에서 시작한다. 프래그먼트 셰이더라면 화면에 생성된 각 프래그먼트에서, OSL이라면 렌더러가 셰이딩하는 표면 또는 볼륨 지점에서 코드가 실행된다. 창작자는 특정 지점의 좌표 `p`에 대해 다음을 계산한다.

- 이 지점은 원의 안쪽인가 바깥쪽인가?
- 이 지점은 선분에서 얼마나 떨어져 있는가?
- 이 지점의 색, 투명도, 거칠기, 발광은 무엇인가?

원을 만들 때 핵심은 중심 `c`와 현재 지점 `p`의 거리다.

$$d(p, c) = \lVert p - c \rVert$$

반지름이 `r`일 때, $d < r$인 지점의 색을 바꾸면 원이 나타난다. 경계를 매끄럽게 하려면 `step` 대신 `smoothstep` 같은 보간 함수를 사용한다. 이 방식은 단순한 원을 구현하는 데는 장황해 보이지만, 원을 좌표 왜곡으로 휘거나, 노이즈로 침식하거나, 여러 원의 거리장을 합치거나 빼는 순간 큰 장점이 된다. 도형은 더 이상 고정된 객체가 아니라 공간 함수의 결과다.

### 2.3 비교의 세 축

본고는 다음 세 축으로 도구를 읽는다.

| 축 | 드로잉 API | 셰이딩 언어 |
|---|---|---|
| 원 | 기본 도형 명령 또는 빌더가 생성 | 중심까지의 거리와 임계값으로 판정 |
| 선 | 두 끝점과 스타일을 가진 기하 명령 | 점에서 선분까지의 최소 거리로 구성 |
| 색 | 전역 스타일 상태 또는 도형 속성 | 지점마다 계산되는 출력 혹은 재질 신호 |

이 표는 양자를 완전히 분리하려는 것이 아니다. p5.js와 openFrameworks는 셰이더를 호출할 수 있고, GLSL/HLSL도 메시 기하와 함께 사용된다. 다만 기본 추상화가 어디에 있는지를 구분해야 혼합 워크플로우의 역할 분담도 명확해진다.

---

## 3. 드로잉 API 계열

## 3.1 Processing: 시각 스케치의 상태 기반 문법

Processing은 시각 작업을 위한 단순한 스케치 모델을 제시한다. 공식 레퍼런스에서 `ellipse()`는 2D primitive, `line()`은 두 점을 잇는 직접 경로로 분류되며, `fill()`은 이후 도형을 채울 색을 설정한다. `fill()`의 영향이 ‘이후 도형’에 적용된다는 사실은 Processing의 중요한 설계 원리다. 색은 원 객체를 만들 때마다 반드시 넣는 인자가 아니라, 현재 드로잉 컨텍스트에 저장된 스타일 상태가 된다.[^processing-fill]

```java
void setup() {
  size(640, 360);
  smooth();
}

void draw() {
  background(18, 20, 28);

  // fill과 stroke는 이후에 호출되는 도형의 스타일 상태를 바꾼다.
  fill(255, 180, 60);
  stroke(255);
  strokeWeight(3);
  ellipse(width * 0.35, height * 0.5, 150, 150);

  stroke(90, 220, 255);
  strokeWeight(8);
  line(width * 0.55, height * 0.72, width * 0.88, height * 0.28);
}
```

문법적으로 `ellipse(x, y, w, h)`는 기본 모드에서 중심 `x, y`와 폭·높이를 받는다. 폭과 높이가 같으면 원이 된다. `line(x1, y1, x2, y2)`는 두 끝점을 받는다. `fill(r, g, b)`와 `stroke(r, g, b)`는 각각 내부와 외곽선의 색을 정하며, `strokeWeight(n)`은 선 두께를 설정한다. Processing은 기본적으로 RGB 0–255 범위를 사용하지만 `colorMode()`로 HSB 등 다른 해석을 선택할 수 있다.[^processing-color]

이 모델은 도형의 기하와 스타일을 의도적으로 분리한다. 이는 초보자에게는 ‘색연필을 고르고 그린다’는 직관을 주며, 숙련자에게는 반복 루프 바깥에서 스타일을 정해 간결한 코드를 쓰게 한다. 단, 상태는 암묵적으로 이어진다. 한 함수가 `noStroke()`를 호출하면 이후 도형도 외곽선이 사라질 수 있으므로, 큰 스케치에서는 `pushStyle()`과 `popStyle()`로 상태 범위를 제한하는 습관이 유용하다.

Processing의 철학은 그래픽스 파이프라인의 복잡함을 숨기는 데 있다. 이것은 한계이기도 하다. 대량 도형, 커스텀 버퍼, 정교한 GPU 병렬 계산이 중심이 되는 작업에서는 기본 primitive 호출만으로 설계를 끝내기 어렵다. 그러나 빠른 알고리즘 드로잉, 교육, 데이터 기반 스케치에서는 이 간결함 자체가 표현의 속도다.

## 3.2 p5.js: 웹 캔버스와 공유 가능한 드로잉 제스처

p5.js는 Processing의 스케치 문법을 JavaScript와 브라우저 환경으로 옮긴다. `circle(x, y, d)`는 중심과 지름으로 원을 만들고, `ellipse(x, y, w, h)`는 타원을 만든다. 공식 레퍼런스는 `ellipse()`의 `x, y`를 중심 위치, `w, h`를 폭과 높이로 설명하며, 높이를 생략하면 폭을 함께 사용한다고 명시한다.[^p5-ellipse]

```javascript
function setup() {
  createCanvas(640, 360);
}

function draw() {
  background(18, 20, 28);

  // p5.js도 Processing처럼 현재 fill/stroke 상태를 사용한다.
  fill(255, 180, 60, 220);
  stroke(255);
  strokeWeight(3);
  circle(width * 0.35, height * 0.5, 150);

  stroke(90, 220, 255);
  strokeWeight(8);
  line(width * 0.55, height * 0.72, width * 0.88, height * 0.28);
}
```

위 코드는 Processing과 매우 닮았지만, p5.js의 중요한 맥락은 웹이다. 마우스·터치·키보드 이벤트, HTML 요소, 웹캠과 마이크 입력, URL 배포가 스케치와 같은 환경에서 만난다. 따라서 원과 선은 정적인 조형 단위이면서 동시에 사용자 입력에 반응하는 인터페이스의 단위가 되기 쉽다. 예를 들어 `mouseX`, `mouseY`를 원의 중심에 연결하면 즉시 상호작용이 된다.

p5.js는 2D 캔버스에 국한되지 않는다. WebGL 모드에서는 도형의 채움과 외곽선이 셰이더를 사용해 그려지며, 공식 아키텍처 문서는 각 shape가 fill과 stroke를 준비해야 하고 각각에 단일 셰이더가 사용된다고 설명한다.[^p5-webgl] 즉 p5.js는 표면적으로는 드로잉 API지만, 필요하면 사용자를 GLSL 기반의 계산 모델로 이동시킬 수 있는 경계 도구이기도 하다.

다만 브라우저의 편의성과 고성능은 동일한 문제가 아니다. 수만 개의 객체를 매 프레임 고수준 호출로 그리는 방식은 병목이 될 수 있다. 이때는 `createGraphics`, 이미지 버퍼, WebGL 인스턴싱, 프래그먼트 셰이더 등으로 전략을 바꿔야 한다. p5.js의 강점은 모든 문제를 하나의 API로 해결하는 데 있지 않고, 빠른 프로토타입에서 웹 기반 전시와 셰이더 실험까지 이어지는 연속성에 있다.

## 3.3 openFrameworks: 네이티브 미디어 시스템으로 확장되는 드로잉

openFrameworks는 C++ 기반의 크리에이티브 코딩 툴킷이다. 기본 드로잉의 모양은 Processing 계열과 친숙하다. `ofSetColor()`로 현재 색을 정하고, `ofDrawCircle()`·`ofDrawLine()` 같은 함수를 호출한다. 공식 ofBook은 이를 색 마커를 꺼내고 기본 도형을 그리는 과정으로 설명하며, `ofSetColor(r, g, b, a)`가 RGB와 알파를 받을 수 있음을 보여 준다.[^ofbook]

```cpp
void ofApp::draw() {
    ofBackground(18, 20, 28);

    // 현재 드로잉 색을 설정한다.
    ofSetColor(255, 180, 60);
    ofFill();
    ofDrawCircle(ofGetWidth() * 0.35f, ofGetHeight() * 0.5f, 75.0f);

    // 선의 색과 두께를 변경한다.
    ofSetColor(90, 220, 255);
    ofSetLineWidth(8.0f);
    ofDrawLine(
        ofGetWidth() * 0.55f, ofGetHeight() * 0.72f,
        ofGetWidth() * 0.88f, ofGetHeight() * 0.28f
    );
}
```

`ofFill()`과 `ofNoFill()`은 내부 채움 여부를 바꾸며, `ofSetLineWidth()`는 선 폭을 설정한다. openFrameworks 문서 목록에는 `ofDrawCircle`, `ofDrawLine`, `ofSetColor`, `ofFill`, `ofEnableAlphaBlending`, `ofEnableDepthTest` 등이 함께 존재한다.[^of-docs] 이 목록이 보여 주듯 이 도구의 드로잉 API는 단순 2D 스케치보다 넓은 렌더링 상태로 열려 있다.

그 확장성은 창작 워크플로우를 바꾼다. Processing/p5.js에서 원 1,000개를 그리는 것은 우선 루프 문제지만, openFrameworks에서는 `ofVboMesh`, 텍스처, FBO, GLSL 셰이더, 카메라 캡처, 애드온을 통한 하드웨어 통신으로 설계를 옮길 수 있다. 즉 동일한 `ofDrawCircle()`은 빠른 초안의 언어인 동시에, 더 낮은 그래픽스 계층으로 내려가기 전의 진입점이다.

그 대가도 분명하다. C++의 컴파일, 프로젝트 설정, 포인터와 자원 수명, 플랫폼별 빌드 문제는 스케치의 즉시성을 줄인다. 그러나 대형 화면 설치, 실시간 영상, 센서와 조명, 오디오비주얼처럼 작품이 운영체제와 장치에 깊이 연결될 때, 이 복잡성은 통제권으로 전환된다.

## 3.4 nannou: Rust의 타입과 빌더로 조합하는 도형

nannou는 Rust 생태계의 크리에이티브 코딩 프레임워크다. 공식 가이드는 2D shape 튜토리얼에서 선, 타원·사각형 같은 단순 폴리곤, 복잡한 폴리곤을 다룬다고 명시한다.[^nannou-guide] nannou의 핵심적인 체감 차이는 도형을 호출하는 문법이 단순 명령 나열이라기보다, 빌더 체인으로 속성을 조합하는 데 있다.

```rust
use nannou::prelude::*;

fn view(app: &App, _model: &Model, frame: Frame) {
    let draw = app.draw();
    draw.background().color(rgb8(18, 20, 28));

    // ellipse 빌더에 위치, 반지름, 색, 외곽선을 조합한다.
    draw.ellipse()
        .xy(pt2(-96.0, 0.0))
        .radius(75.0)
        .color(rgb8(255, 180, 60))
        .stroke(WHITE)
        .stroke_weight(3.0);

    draw.line()
        .start(pt2(32.0, -80.0))
        .end(pt2(250.0, 80.0))
        .weight(8.0)
        .color(rgb8(90, 220, 255));

    draw.to_frame(app, &frame).unwrap();
}
```

이 코드에서 `draw.ellipse()`는 타원 객체를 구성하는 출발점이다. 이어지는 `.xy()`, `.radius()`, `.color()`는 같은 도형에 속성을 붙인다. `pt2(x, y)`는 2차원 점을 만들고, `rgb8(r, g, b)`는 8비트 RGB 색을 만든다. 마지막의 `draw.to_frame(...)`은 구성한 드로잉을 프레임에 제출한다.

이 문법은 상태 기반 API와 다른 강조점을 준다. Processing에서는 `fill()`을 호출한 뒤 여러 도형이 그 상태를 공유할 수 있다. nannou에서는 색과 선의 속성이 특정 도형 체인 안에 명시되는 경우가 많아, 코드의 지역성(locality)이 높다. 이는 도형 하나의 스타일을 추적하기 쉽다는 뜻이다. 물론 배경과 변환, blend 등 전역적 맥락도 존재하지만, Rust의 타입과 소유권 규칙은 데이터가 어디서 생성되고 소비되는지 더 명확하게 만든다.

창작적으로 nannou는 ‘안전한 언어가 아름다운 그림을 만든다’고 단순화할 수는 없다. 소유권과 차용은 초기에 학습 부담이 된다. 그러나 장기 실행 시뮬레이션, 대규모 상태, 병렬 계산, 명시적인 데이터 모델이 필요한 프로젝트에서는 오류 가능성을 줄이는 구조가 강점이 될 수 있다. 여기에서 원과 선은 즉석 명령이면서 동시에 명확한 타입을 가진 구성 요소다.

## 3.5 Quil: Processing 동사와 함수형 상태의 결합

Quil은 Clojure/ClojureScript에서 Processing 계열의 시각 API를 사용할 수 있게 하는 라이브러리다. API 문서는 `(ellipse x y width height)`가 display window에 타원을 그리고, 폭과 높이가 같으면 원이 된다고 설명한다.[^quil-api] 문법은 Processing과 유사하지만, 함수와 불변 데이터가 작업의 조직 방식을 바꾼다.

```clojure
(ns circle-line.core
  (:require [quil.core :as q]
            [quil.middleware :as m]))

(defn setup []
  {:t 0.0})

(defn update-state [state]
  (update state :t + 0.02))

(defn draw-state [{:keys [t]}]
  (q/background 18 20 28)

  (q/fill 255 180 60)
  (q/stroke 255)
  (q/stroke-weight 3)
  (q/ellipse 224 180 150 150)

  (q/stroke 90 220 255)
  (q/stroke-weight 8)
  (q/line 352 260 563 100))

(q/defsketch circles-and-lines
  :title "Circle and Line"
  :size [640 360]
  :setup setup
  :update update-state
  :draw draw-state
  :middleware [m/fun-mode])
```

`q/defsketch`는 스케치를 선언하며 창 크기, 초기화 함수, 업데이트 함수, 드로잉 함수를 연결한다. `m/fun-mode`를 사용하면 `setup`은 상태 맵을 반환하고, `update-state`는 이전 상태를 받아 새 상태를 반환하며, `draw-state`는 그 상태를 읽어 렌더링한다. 위 예제에서 `:t`는 아직 드로잉에 쓰이지 않지만, 애니메이션을 위해 시간 상태를 안전하게 축적하는 자리를 보여 준다. 실제 작품에서는 이 값을 위치, 색상, 노이즈 위상에 연결한다.

Quil의 `q/fill`, `q/stroke`, `q/ellipse`, `q/line`은 명령형 드로잉의 친숙함을 유지한다. 다만 도형을 만들 규칙을 `map`, `reduce`, lazy sequence, 재귀, 고차 함수로 먼저 만들고 마지막에 그릴 수 있다는 점이 중요하다. 예컨대 ‘원의 목록’을 불변 시퀀스로 생성하고 `doseq`로 렌더링하면, 생성 규칙과 부수 효과를 분리해 사고하기 쉬워진다.

Quil은 Processing의 기능적 사본이 아니다. Clojure의 REPL 중심 탐색, 데이터 우선 설계, 함수 합성은 패턴 생성과 상태 전이를 다루는 별도의 장점을 준다. 반면 JVM 및 Clojure의 생태계에 익숙하지 않은 창작자에게는 라이브러리의 단순함보다 언어의 낯섦이 먼저 느껴질 수 있다.

---

## 4. 드로잉 API 계열의 종합 비교

다섯 도구는 원·선·색을 고수준 API로 제공한다는 점에서 공통적이다. 그러나 같은 코드 모양이 같은 창작 경험을 보장하지는 않는다.

| 도구 | 원·선의 기본 추상화 | 색의 주된 위치 | 강점 | 전환 지점 |
|---|---|---|---|---|
| Processing | 즉시 실행되는 primitive 명령 | 현재 스타일 상태 | 교육, 빠른 스케치 | PGraphics, PShape, 셰이더 |
| p5.js | 브라우저 캔버스 primitive | 현재 스타일 상태 | 웹 공유, 입력·DOM 결합 | WebGL 및 custom shader |
| openFrameworks | C++ 드로잉 함수 | 그래픽스 상태 | 설치, 장치, 네이티브 성능 | VBO/FBO/GLSL |
| nannou | builder로 조합되는 도형 | 도형 체인 속성 | 타입 명시성, 구조화 | wgpu/셰이더·데이터 파이프라인 |
| Quil | Processing primitive + 함수형 상태 | 드로잉 상태 | 규칙·상태·시퀀스의 조합 | Processing/Java 및 별도 GPU 경로 |

Processing, p5.js, openFrameworks, Quil은 모두 어느 정도 ‘현재 스타일’을 갖는다. 이것은 펜과 물감을 바꾼 뒤 연속해서 그리는 방식과 닮아 있다. nannou는 색을 도형 체인에 붙이는 문법이 두드러져, 스타일이 객체에 더 가까이 위치한다. 하지만 이 차이는 절대적인 분류가 아니다. 어느 도구든 함수로 스타일을 감싸거나, 데이터 구조로 도형 속성을 관리할 수 있다.

더 중요한 차이는 창작자가 언제 하위 계층을 만나게 되는가다. Processing은 스케치 수준의 속도를 우선한다. p5.js는 배포와 상호작용에 유리하다. openFrameworks는 하드웨어와 고성능 그래픽스로 진입하는 길을 넓힌다. nannou는 Rust의 구조적 제약 안에서 장기적 시스템 설계를 장려한다. Quil은 ‘화면에 무엇을 그리는가’보다 ‘그릴 데이터가 어떻게 변환되는가’를 전면에 놓기 쉽다.

---

## 5. 셰이딩 언어 계열: 도형이 아니라 거리와 재질을 계산하기

## 5.1 원: 중심에서의 거리로 만드는 마스크

프래그먼트 셰이더에서 정규화된 좌표 `uv`가 $[0,1]^2$ 범위에 있다고 하자. 가운데를 원점으로 옮기려면 `uv - 0.5`를 계산한다. 그 길이 `length(p)`가 반지름보다 작으면 원 내부다. 가장 단순한 마스크는 다음과 같다.

$$m = 1 - \operatorname{step}(r, \lVert uv - c \rVert)$$

그러나 픽셀 격자에서 `step`은 경계를 계단처럼 만들 수 있다. `smoothstep(r + a, r - a, d)`는 경계 폭 `a` 안에서 부드러운 전이를 만들어 안티앨리어싱에 유리하다. 여기에서 원은 삼각형 메시나 원 명령이 아니라, 모든 좌표에 적용된 하나의 부등식이다.

## 5.2 선: 점에서 선분까지의 최소 거리

두 점 `a`, `b`가 만드는 선분과 점 `p` 사이의 거리도 벡터 투영으로 구할 수 있다. 선분 방향을 `ba = b - a`, 점 방향을 `pa = p - a`라 할 때, 투영 매개변수는 다음과 같다.

$$h = \operatorname{clamp}\left(\frac{pa \cdot ba}{ba \cdot ba}, 0, 1\right)$$

가장 가까운 점은 $a + hba$이고, 선분까지의 거리는 $\lVert pa - hba \rVert$다. 이 거리가 선 폭의 절반보다 작은 곳을 채우면 선이 된다. 이 계산은 둥근 cap을 자연스럽게 만들 수 있으며, 거리값을 발광·투명도·노이즈 왜곡에 다시 사용할 수 있다는 점에서 단순 `line()`보다 표현적이다.

## 5.3 GLSL: 화면의 프래그먼트마다 색을 쓰기

GLSL은 OpenGL 계열의 셰이딩 언어다. Khronos의 GLSL 명세는 프래그먼트 셰이더가 계산한 값이 framebuffer 또는 texture memory를 갱신하는 데 사용된다고 설명한다.[^glsl-spec] 다음 예제는 전면 사각형에 적용되는 프래그먼트 셰이더를 가정한다. `vUv`는 정점 셰이더에서 전달된 0–1 범위의 UV 좌표다.

```glsl
#version 330 core

in vec2 vUv;
out vec4 fragColor;

float circleMask(vec2 p, float radius, float feather) {
    float d = length(p);
    return 1.0 - smoothstep(radius - feather, radius + feather, d);
}

float segmentMask(vec2 p, vec2 a, vec2 b, float halfWidth, float feather) {
    vec2 pa = p - a;
    vec2 ba = b - a;
    float h = clamp(dot(pa, ba) / dot(ba, ba), 0.0, 1.0);
    float d = length(pa - ba * h);
    return 1.0 - smoothstep(halfWidth - feather, halfWidth + feather, d);
}

void main() {
    // 화면 중심을 원점으로 옮기고 종횡비 보정을 적용한다.
    vec2 p = vUv - 0.5;
    p.x *= 16.0 / 9.0;

    float circle = circleMask(p - vec2(-0.25, 0.0), 0.22, 0.003);
    float line = segmentMask(p, vec2(0.05, -0.22), vec2(0.55, 0.24), 0.012, 0.003);

    vec3 background = vec3(0.071, 0.078, 0.110);
    vec3 circleColor = vec3(1.0, 0.71, 0.24);
    vec3 lineColor = vec3(0.35, 0.86, 1.0);

    vec3 color = mix(background, circleColor, circle);
    color = mix(color, lineColor, line);
    fragColor = vec4(color, 1.0);
}
```

`vec2`와 `vec3`는 각각 2차원·3차원 벡터이며, `dot(a, b)`는 내적, `length(v)`는 벡터 길이다. `mix(a, b, t)`는 `a`와 `b`를 `t`만큼 선형 보간한다. 이 코드에서 `circle`과 `line`은 색이 아니라 0에서 1 사이의 마스크다. 마스크를 색과 합성하는 방식은 매우 일반적이다. 같은 마스크를 알파, 발광 세기, 변위량, 텍스처 혼합 비율에 쓸 수 있기 때문이다.

GLSL의 창작적 핵심은 화면을 픽셀 배열이 아니라 좌표의 장(field)으로 보는 데 있다. `p`에 시간에 따른 회전 행렬을 적용하면 원 전체가 회전한다. `p`에 노이즈 벡터를 더하면 원의 경계가 흔들린다. `min`으로 여러 거리장을 합치거나 `max`로 교차를 만들면 복잡한 도형 조합이 가능하다. 기본 도형 명령이 없는 것은 결핍이 아니라, 도형의 생성 규칙을 열어 둔 설계다.

## 5.4 HLSL: DirectX 파이프라인의 픽셀 계산

HLSL은 DirectX의 programmable shader를 작성하는 C 계열 고수준 언어다. Microsoft 문서는 HLSL로 vertex shader와 pixel shader 등을 작성하여 Direct3D 렌더러에 사용할 수 있다고 설명한다.[^hlsl-docs] 픽셀 셰이더의 수학은 GLSL 프래그먼트 셰이더와 매우 가깝다. 차이는 주로 타입 표기, 시맨틱, 리소스 바인딩, 엔진 통합 문맥에서 생긴다.

```hlsl
struct PixelInput {
    float2 uv : TEXCOORD0;
};

float CircleMask(float2 p, float radius, float feather) {
    float d = length(p);
    return 1.0 - smoothstep(radius - feather, radius + feather, d);
}

float SegmentMask(float2 p, float2 a, float2 b, float halfWidth, float feather) {
    float2 pa = p - a;
    float2 ba = b - a;
    float h = saturate(dot(pa, ba) / dot(ba, ba));
    float d = length(pa - ba * h);
    return 1.0 - smoothstep(halfWidth - feather, halfWidth + feather, d);
}

float4 PSMain(PixelInput input) : SV_Target {
    float2 p = input.uv - 0.5;
    p.x *= 16.0 / 9.0;

    float circle = CircleMask(p - float2(-0.25, 0.0), 0.22, 0.003);
    float line = SegmentMask(p, float2(0.05, -0.22), float2(0.55, 0.24), 0.012, 0.003);

    float3 background = float3(0.071, 0.078, 0.110);
    float3 circleColor = float3(1.0, 0.71, 0.24);
    float3 lineColor = float3(0.35, 0.86, 1.0);

    float3 color = lerp(background, circleColor, circle);
    color = lerp(color, lineColor, line);
    return float4(color, 1.0);
}
```

`float2`, `float3`, `float4`는 GLSL의 `vec2`, `vec3`, `vec4`에 대응한다. `lerp(a, b, t)`는 GLSL의 `mix`와 같은 선형 보간이며, `saturate(x)`는 값을 0과 1 사이로 제한한다. `: SV_Target`는 이 함수의 반환값이 렌더 타깃으로 출력됨을 나타내는 시맨틱이다.

HLSL에서 원과 선은 게임 엔진의 material, post-process, UI shader, VFX 안에서 자주 사용된다. 여기에서 색은 단순 화면 색일 수도 있고, G-buffer의 일부 값, 마스크, 노멀 변형, 발광 채널일 수도 있다. 다시 말해 HLSL의 특징은 거리 함수 그 자체보다, 그 거리 함수가 DirectX 렌더링 파이프라인의 여러 단계와 자원에 결합된다는 점이다.

## 5.5 OSL: 표면 위에서 계산되는 패턴과 산란

OSL(Open Shading Language)은 고급 렌더링 엔진을 위한 programmable shading system이며 Blender Cycles는 이를 C 계열 스크립트 언어로 custom shader를 작성하는 방식으로 소개한다.[^blender-osl] GLSL/HLSL과 같은 벡터 수학을 쓸 수 있지만, 기본적인 질문은 다르다. GLSL 프래그먼트 셰이더가 대개 ‘이 화면 위치에 무슨 색을 쓸까’를 묻는다면, OSL은 ‘이 표면 위치가 어떤 재질적 성질을 가질까’를 묻는다.

다음 코드는 입력 좌표의 XY 평면에서 원형 마스크를 계산해 색 출력을 만든다. Blender의 OSL Script Node에서 색상 출력으로 사용할 수 있는 단순 패턴 예시다.

```c
shader circle_pattern(
    point Coord = P,
    float Radius = 0.25,
    float Feather = 0.01,
    color Inside = color(1.0, 0.71, 0.24),
    color Outside = color(0.071, 0.078, 0.110),
    output color Color = color(0.0)
)
{
    // 표면 좌표의 XY 성분만 사용해 2D 패턴 공간을 만든다.
    point q = point(Coord[0], Coord[1], 0.0);
    float d = distance(q, point(0.0, 0.0, 0.0));

    // Radius 부근에서 부드럽게 전이하는 원 마스크.
    float mask = 1.0 - smoothstep(Radius - Feather, Radius + Feather, d);
    Color = mix(Outside, Inside, mask);
}
```

여기에서 `Coord`를 `P`로 두면 셰이딩 지점의 위치를 기본 입력으로 쓴다. 그러나 실무에서는 Object, World, UV, Generated 좌표 또는 Mapping 노드에서 전달한 벡터를 선택하는 일이 더 중요하다. 같은 원 식도 어떤 좌표계를 쓰는지에 따라 물체에 고정된 무늬, 월드 공간을 가로지르는 무늬, UV를 따르는 무늬가 된다.

OSL의 표면·볼륨 셰이더는 원칙적으로 최종 색이 아니라 radiance closure를 계산한다. OSL 공식 문서는 surface/volume shader가 surface 또는 volume이 빛을 산란하는 방식을 나타내는 closure를 계산하며, integrator가 이를 평가한다고 설명한다.[^osl-intro] 따라서 위의 `Color`는 완성된 재질 전체가 아니라, 보통 Base Color 또는 다른 재질 입력으로 연결될 패턴 신호로 이해하는 편이 정확하다. 예컨대 `mask`를 diffuse 색에 연결할 수도 있고, emission 강도나 roughness에 연결할 수도 있다.

OSL에서 ‘색을 칠한다’는 말은 두 층을 가진다. 첫째는 위 코드처럼 절차적 색 또는 마스크를 계산하는 층이다. 둘째는 그 값을 diffuse, glossy, emission 같은 closure 혹은 Blender Principled BSDF의 입력에 연결하여 빛과 상호작용하게 하는 층이다. 이 때문에 OSL은 화면 그래픽을 위한 GLSL과 유사한 수학 도구를 갖고도, 궁극적으로는 3D 표면의 물질성을 설계하는 언어에 가깝다.

---

## 6. 색의 의미: 스타일 상태에서 재질 신호까지

색은 세 계열에서 서로 다른 의미를 가진다.

첫째, Processing·p5.js·openFrameworks·Quil에서 색은 흔히 현재 드로잉 스타일이다. `fill()`은 이후 도형 내부의 색을, `stroke()`는 윤곽의 색을 정한다. `background()`는 프레임을 지우거나 바탕을 칠하는 명령으로 쓰인다. 이때 색은 조형의 직접적인 속성이다.

둘째, nannou에서는 색이 `draw.ellipse().color(...)`처럼 도형 구성 체인에 붙어 나타날 수 있다. 이는 상태를 전혀 사용하지 않는다는 뜻이 아니라, 스타일의 소유 범위를 도형 가까이에 둔다는 뜻이다. 장면이 데이터 중심으로 커질수록 이 방식은 속성의 추적을 돕는다.

셋째, GLSL/HLSL에서 색은 각 프래그먼트/픽셀의 계산 결과다. 좌표를 그대로 RGB에 매핑하면 그라데이션이 되고, 시간·노이즈·텍스처·거리장·조명 값을 조합하면 애니메이션이나 복잡한 패턴이 된다. 색은 더 이상 선행 상태가 아니라 함수의 출력이다.

넷째, OSL에서 색은 재질 네트워크를 구성하는 값 중 하나다. 표면 반사율, 투명도, 거칠기, 발광과 결합해 최종 광학적 결과에 기여한다. OSL 명세의 closure는 단순 색상 벡터보다 넓은 의미를 갖는다. 예를 들어 diffuse closure는 표면 법선과 입사 조명에 따른 Lambertian 반사를 표현한다.[^osl-spec]

이 구분은 코딩 아트의 색 설계에도 실질적 영향을 준다. 화면에서 바로 보이는 팔레트를 조절하고 싶다면 드로잉 API의 `fill()` 또는 fragment output이 직접적이다. 반대로 3D 모델 표면에서 빛에 따라 변하는 색과 질감을 만들고 싶다면, 절차적 마스크를 OSL 재질 파라미터로 보내야 한다.

---

## 7. 좌표계: 캔버스의 픽셀에서 표면의 위치까지

원을 정의하는 식은 짧지만, 좌표계가 바뀌면 결과의 의미가 바뀐다.

- **캔버스 좌표:** Processing과 p5.js의 기본 2D 작업은 흔히 좌상단을 원점으로 하고, 픽셀 단위로 위치를 다룬다. `width`, `height`로 화면 크기에 대응한다.
- **윈도우 중심 좌표:** nannou의 2D 예제는 중심을 `(0, 0)`으로 잡는 경우가 많다. 대칭과 회전에는 편하지만, 웹 캔버스의 좌표 감각과는 다르다.
- **월드·뷰·클립 공간:** openFrameworks의 3D 및 GLSL/HLSL 파이프라인은 모델, 월드, 카메라, 투영 변환을 거친다. 화면에 보이는 원도 실제로는 메시 또는 후처리 공간에서 정의될 수 있다.
- **UV 좌표:** GLSL/HLSL에서 `uv`는 보통 0–1 범위의 텍스처 공간이다. 해상도와 독립적인 패턴을 만들기 쉽지만, 종횡비 보정을 잊으면 원이 타원으로 보일 수 있다.
- **표면 좌표:** OSL의 `P`, `N`, UV, Object, World 좌표는 표면 위의 패턴이 어디에 고정되는지를 결정한다. 특히 프로시저럴 텍스처에서 좌표 선택은 색 선택만큼 중요하다.

도구 선택보다 먼저 좌표계를 의식해야 하는 이유가 여기에 있다. ‘원이 찌그러졌다’는 문제는 반지름 값의 문제가 아니라, 비정사각 좌표 공간을 정사각으로 가정한 문제일 수 있다. ‘패턴이 물체를 따라 움직이지 않는다’는 문제는 패턴 식이 아니라 Object/World 공간의 선택 문제일 수 있다.

---

## 8. 성능과 규모: 객체 수의 비용, 해상도의 비용

드로잉 API와 셰이더의 비용 구조도 다르다.

드로잉 API에서 많은 원과 선을 그릴 때는 객체 수와 호출 수가 먼저 문제 된다. 10만 개의 원을 루프에서 각각 호출하면 CPU 측 반복, 드로우콜, 기하 생성, 상태 변경이 누적될 수 있다. 해결책은 도구마다 다르지만, 공통적으로는 스타일 변경을 줄이고, 도형 데이터를 버퍼에 모으며, 인스턴싱이나 텍스처/FBO를 사용하고, 반복 패턴을 GPU에 넘기는 방향으로 간다.

프래그먼트 셰이더에서 화면 전체의 원형 패턴을 계산하면, 코드 한 번이 화면의 모든 프래그먼트에서 실행된다. 객체 수는 적을 수 있지만 해상도가 비용을 결정한다. 4K 화면에서 복잡한 루프, 다수의 텍스처 샘플, 조건 분기, 레이마칭을 사용하면 픽셀당 비용이 곧 전체 비용이 된다. 즉 셰이더는 ‘공짜로 빠른 마법’이 아니라, 병렬화된 계산을 해상도 단위로 지불하는 모델이다.

OSL은 실시간 GPU 프래그먼트 셰이더와 또 다르다. Cycles 같은 물리 기반 렌더러에서는 샘플 수, 광선 경로, 재질 closure의 복잡도, 텍스처/노이즈 평가 등이 렌더 시간에 영향을 준다. OSL의 장점은 복잡한 절차적 재질을 표현하는 데 있지만, 모든 효과를 실시간 화면 셰이더와 같은 비용 감각으로 다루어서는 안 된다.

따라서 도구를 고를 때는 ‘원 하나를 그릴 수 있는가’가 아니라 다음을 물어야 한다.

1. 나는 수천 개의 독립 객체를 움직이는가?
2. 나는 화면 전체에 적용되는 하나의 연속적 필드를 계산하는가?
3. 나는 3D 표면의 재질을 오프라인 렌더링하는가?
4. CPU, GPU, 렌더러 중 어디에 계산을 배치해야 하는가?

---

## 9. 표현 목적에 따른 선택 전략

### 9.1 빠른 알고리즘 드로잉과 교육

기하 알고리즘, 반복, 난수, 트레일, 간단한 인터랙션을 빠르게 탐색한다면 Processing과 p5.js가 적합하다. 특히 p5.js는 링크만으로 결과를 공유하기 쉬워 수업, 워크숍, 온라인 포트폴리오에 강점이 있다. Quil은 같은 목적에 함수형 데이터 변환을 더하고 싶을 때 유효하다.

### 9.2 웹 기반 생성 아트와 셰이더

브라우저에서 상호작용과 고밀도 픽셀 효과를 함께 원한다면 p5.js 또는 custom WebGL 환경에서 GLSL을 병행하는 구성이 좋다. p5.js는 입력, 캔버스, 기본 도형을 빠르게 만들고, GLSL은 노이즈 왜곡, 거리장, 피드백, 후처리, 대량 병렬 계산을 담당할 수 있다.

### 9.3 실시간 설치와 하드웨어 결합

카메라, 깊이 센서, 프로젝터, MIDI, OSC, 오디오 분석, 다중 화면 같은 조건에서는 openFrameworks의 네이티브 생태계가 강력하다. 여기에서 기본 원과 선 API는 테스트용 시각화로 시작하지만, 곧 FBO·VBO·GLSL·애드온을 조합한 시스템으로 성장할 수 있다.

### 9.4 구조적 시뮬레이션과 장기 유지

복잡한 에이전트, 물리 시뮬레이션, 상태 모델, 데이터 구조를 장기적으로 다루고자 한다면 nannou와 Rust의 타입 시스템은 유리할 수 있다. 초기의 학습 비용은 있지만, 도형·상태·이벤트·렌더링의 경계를 명시적으로 설계하게 한다.

### 9.5 3D 표면의 절차적 재질

Blender Cycles 기반의 3D 작업에서 모델 표면에 좌표 기반 패턴을 입히고, 그 패턴을 색·거칠기·발광·변위로 연결하려면 OSL이 적합하다. 이때 ‘원’은 화면에 떠 있는 아이콘이 아니라, 좌표계에 고정된 재질 마스크다. OSL은 GLSL의 화면 공간 생성 아트를 대체하기보다, 다른 렌더링 문맥에서 같은 수학적 사고를 확장한다.

---

## 10. 결론: 도형 명령과 공간 함수 사이를 오가기

원, 선, 색은 코딩 아트의 가장 작은 어휘지만, 도구가 세계를 조직하는 방식을 압축해서 보여 준다. Processing과 p5.js는 도형을 빠르게 배치하고 즉시 결과를 확인하는 스케치의 언어다. openFrameworks는 그 언어를 장치·실시간 미디어·GPU 시스템으로 확장한다. nannou는 도형을 타입과 빌더를 통해 구조적으로 조합하며, Quil은 익숙한 드로잉 동사를 함수형 상태와 데이터 변환의 흐름에 배치한다.

GLSL과 HLSL은 한 단계 다른 질문을 던진다. 원은 호출하는 객체가 아니라 거리 조건이며, 선은 선분까지의 거리이고, 색은 각 프래그먼트 또는 픽셀이 계산한 결과다. 이 관점에서 노이즈, 반복, 왜곡, 블렌딩, SDF 조합은 부가 효과가 아니라 도형 정의 자체를 바꾸는 연산이 된다. OSL은 같은 수학을 표면 셰이딩의 문맥으로 옮긴다. 여기에서 색은 최종 화면 출력에 앞서 재질과 광 transport에 참여하는 신호가 된다.

좋은 워크플로우는 이 둘 중 하나만 고르는 데 있지 않다. 드로잉 API로 창작의 구조, 입력, 시간, 장면을 구성하고, 셰이더로 공간의 미세한 규칙과 대규모 병렬 계산을 담당하게 할 수 있다. 예를 들어 p5.js는 사용자 인터랙션과 인터페이스를 맡고 GLSL은 화면의 거리장 패턴을 맡을 수 있다. openFrameworks는 카메라·센서 입력을 수집하고 GLSL은 영상 변형을 맡을 수 있다. Blender에서는 Geometry/Shader Node와 OSL이 표면 좌표 위의 절차적 규칙을 나눠 맡는다.

결국 도구 선택은 ‘어느 라이브러리에 원 함수가 있는가’의 문제가 아니다. 창작자가 객체를 배치하고 싶은지, 화면을 하나의 함수로 다루고 싶은지, 물체 표면의 성질을 설계하고 싶은지를 묻는 문제다. 원을 명령으로 그릴 수 있어야 하고, 필요해질 때 원을 거리 함수로 다시 발명할 수도 있어야 한다. 그 두 능력 사이를 오가는 일이 현대 코딩 아트의 중요한 기술적 문해력이다.

---

## 참고문헌 및 공식 문서

[^processing-fill]: Processing Foundation, “[fill() / Reference](https://processing.org/reference/fill_.html).” `fill()`이 이후 도형을 채우는 색을 설정하며 RGB/HSB 해석을 `colorMode()`에 따른다고 설명한다.

[^processing-color]: Processing Foundation, “[color() / Reference](https://processing.org/reference/color_.html).” 기본 RGB 범위와 `colorMode(HSB, ...)` 예제를 제공한다.

[^p5-ellipse]: p5.js, “[ellipse() Reference](https://p5js.org/reference/p5/ellipse).” 타원의 중심 위치와 폭·높이 인자를 설명한다.

[^p5-webgl]: p5.js, “[WebGL Mode Architecture](https://p5js.org/contribute/webgl_mode_architecture).” 2D/WebGL shape의 fill과 stroke 및 기본 셰이더 구조를 설명한다.

[^ofbook]: openFrameworks, “[ofBook: Graphics](https://openframeworks.cc/ofBook/chapters/intro_to_graphics.html).” `ofSetColor`, `ofDrawCircle`, `ofDrawLine`, RGB/alpha 예제를 설명한다.

[^of-docs]: openFrameworks, “[Documentation](https://openframeworks.cc/documentation).” 그래픽스 함수 및 렌더링 상태 API 목록.

[^nannou-guide]: nannou, “[Draw — 2D Shapes](https://guide.nannou.cc/tutorials/draw/drawing-2d-shapes.html).” 선, 타원, 사각형, 폴리곤을 다루는 공식 가이드.

[^quil-api]: Quil, “[quil.core API](https://cljdoc.org/d/quil/quil/4.3.1563/api/quil.core).” `ellipse`, `defsketch` 등 Quil API 문서.

[^glsl-spec]: Khronos Group, “[The OpenGL Shading Language 4.60 Specification](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.pdf).” 프래그먼트 셰이더 계산 결과와 framebuffer/texture memory 갱신에 대한 명세.

[^hlsl-docs]: Microsoft, “[High-level shader language (HLSL)](https://learn.microsoft.com/en-us/windows/win32/direct3dhlsl/dx-graphics-hlsl).” DirectX programmable shader를 위한 HLSL 개요.

[^blender-osl]: Blender Foundation, “[Open Shading Language — Blender 5.2 LTS Manual](https://docs.blender.org/manual/en/latest/render/cycles/osl/index.html).” OSL의 Blender Cycles 사용 문서.

[^osl-intro]: Open Shading Language, “[Introduction](https://open-shading-language.readthedocs.io/en/latest/intro.html).” surface/volume shader의 radiance closure 및 integrator 평가 방식을 설명한다.

[^osl-spec]: Open Shading Language, “[OSL Specification 1.11](https://docs.otoy.com/osl/assets/doc/osl-languagespec-1.11.pdf).” material closure 및 diffuse 반사 모델의 명세.
