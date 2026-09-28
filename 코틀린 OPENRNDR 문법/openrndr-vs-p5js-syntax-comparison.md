# OpenRNDR과 p5.js 비교

## 문법과 코드 구조를 중심으로

OpenRNDR과 p5.js는 모두 코드로 그래픽을 만드는 Creative Coding 프레임워크입니다. 두 도구 모두 화면에 도형을 그리고, 시간에 따라 값을 변화시키며, 마우스나 키보드 입력에 반응하는 작품을 만들 수 있습니다.

하지만 코드가 실행되는 환경과 언어의 성격이 다릅니다.

- **p5.js**는 JavaScript와 HTML Canvas를 기반으로 하는 웹 중심 프레임워크입니다.
- **OpenRNDR**은 Kotlin과 JVM을 기반으로 하는 데스크톱 중심 그래픽 프레임워크입니다.

문법적으로 보면 p5.js는 짧고 즉각적인 스케치에 강하고, OpenRNDR은 정적 타입, 객체지향 구조, Gradle 프로젝트, IntelliJ IDEA의 코드 분석 기능을 활용해 규모 있는 그래픽 프로그램을 작성하는 데 강합니다.

---

## 1. 핵심 차이 요약

| 항목 | p5.js | OpenRNDR |
|---|---|---|
| 프로그래밍 언어 | JavaScript | Kotlin |
| 실행 환경 | 브라우저 | JVM 데스크톱 애플리케이션 |
| 프로젝트 시작 | HTML과 JavaScript 파일 | Gradle 기반 Kotlin 프로젝트 |
| 렌더링 루프 | `setup()`, `draw()` | `application`, `program`, `extend` |
| 타입 시스템 | 동적 타입 | 정적 타입 |
| 좌표 처리 | 숫자와 객체를 자유롭게 사용 | `Double`, `Vector2`, `Vector2D` 등 명시적 타입 사용 |
| 색상 | 보통 0~255 범위 | 일반적으로 0.0~1.0 범위 |
| 상태 저장 | `push()`, `pop()` | `drawer.isolated {}` |
| 배열과 컬렉션 | JavaScript 배열 | Kotlin `List`, `MutableList`, `Sequence` 등 |
| 오류 발견 시점 | 주로 실행 시점 | 컴파일 시점과 실행 시점 |
| 배포 | 웹 페이지, JavaScript 파일 | JVM 애플리케이션 또는 패키징된 실행 파일 |
| 강점 | 빠른 실험, 웹 배포, 교육 | 구조화, 타입 안정성, 수학·그래픽 프로젝트 |

---

# 2. 같은 장면 그리기

두 프레임워크에서 같은 장면을 작성해보겠습니다.

장면의 구성은 다음과 같습니다.

- 800×600 크기의 화면
- 어두운 배경
- 마우스 위치를 중심으로 하는 큰 분홍색 원
- 중심을 공전하는 8개의 작은 청록색 원
- 시간에 따라 공전 애니메이션

---

## 2.1 p5.js 코드

```javascript
let particleCount = 8;
let orbitRadius = 150;
let particleRadius = 16;

function setup() {
  createCanvas(800, 600);
  angleMode(RADIANS);
}

function draw() {
  background(15, 20, 32);

  const centerX = mouseX;
  const centerY = mouseY;
  const time = millis() * 0.001;

  noStroke();

  // 중앙 원
  fill(255, 120, 180);
  circle(centerX, centerY, 100);

  // 공전하는 작은 원
  for (let i = 0; i < particleCount; i++) {
    const angle =
      time +
      i * TWO_PI / particleCount;

    const x =
      centerX +
      cos(angle) * orbitRadius;

    const y =
      centerY +
      sin(angle) * orbitRadius;

    fill(100, 220, 255);
    circle(x, y, particleRadius * 2);
  }
}
```

p5.js에서는 `setup()`이 한 번 호출되고, `draw()`가 매 프레임 반복 호출됩니다. `mouseX`, `mouseY`, `millis()`, `cos()`, `sin()`처럼 전역 함수와 전역 변수를 사용해 빠르게 장면을 구성할 수 있습니다.

---

## 2.2 OpenRNDR 코드

```kotlin
import kotlin.math.cos
import kotlin.math.sin
import org.openrndr.application
import org.openrndr.color.ColorRGBa

fun main() = application {
    configure {
        width = 800
        height = 600
    }

    program {
        val particleCount = 8
        val orbitRadius = 150.0
        val particleRadius = 16.0

        val backgroundColor = ColorRGBa.fromHex("#0F1420")
        val centerColor = ColorRGBa.fromHex("#FF78B4")
        val particleColor = ColorRGBa.fromHex("#64DCFF")

        extend {
            drawer.background(backgroundColor)
            drawer.stroke = null

            val centerX = mouse.position.x
            val centerY = mouse.position.y
            val time = seconds

            // 중앙 원
            drawer.fill = centerColor
            drawer.circle(
                centerX,
                centerY,
                50.0
            )

            // 공전하는 작은 원
            for (i in 0 until particleCount) {
                val angle =
                    time +
                    i * Math.PI * 2.0 / particleCount

                val x =
                    centerX +
                    cos(angle) * orbitRadius

                val y =
                    centerY +
                    sin(angle) * orbitRadius

                drawer.fill = particleColor
                drawer.circle(
                    x,
                    y,
                    particleRadius
                )
            }
        }
    }
}
```

OpenRNDR에서는 `fun main() = application {}`으로 애플리케이션을 시작합니다. 창 설정은 `configure {}` 안에 작성하고, 매 프레임 실행되는 그리기 코드는 `extend {}` 안에 배치합니다.

p5.js의 `circle(x, y, diameter)`와 달리 OpenRNDR의 `circle(x, y, radius)`는 반지름을 사용하므로 중앙 원의 지름 100은 반지름 50으로 작성했습니다.

---

# 3. 프로그램 생명주기 비교

## p5.js의 생명주기

```javascript
function setup() {
  // 한 번만 실행되는 초기화 코드
}

function draw() {
  // 매 프레임 실행되는 코드
}
```

p5.js의 구조는 매우 단순합니다.

- `setup()`에서 캔버스 크기와 초기 상태를 설정합니다.
- `draw()`에서 매 프레임 배경을 지우고 장면을 다시 그립니다.
- p5.js가 자동으로 애니메이션 루프를 실행합니다.

## OpenRNDR의 생명주기

```kotlin
fun main() = application {
    configure {
        width = 800
        height = 600
    }

    program {
        // 초기화 코드

        extend {
            // 매 프레임 실행되는 코드
        }
    }
}
```

OpenRNDR은 애플리케이션, 프로그램, Extension이라는 계층을 사용합니다.

- `application {}`: 애플리케이션 전체를 정의합니다.
- `configure {}`: 창 크기, 타이틀, 프레임레이트 등을 설정합니다.
- `program {}`: 프로그램의 상태와 초기화 로직을 작성합니다.
- `extend {}`: 렌더링 루프에 작업을 추가합니다.

p5.js가 교육용 스케치에 최적화된 단순한 구조라면, OpenRNDR은 여러 렌더링 단계와 Extension을 조합할 수 있는 구조입니다.

---

# 4. 변수 선언과 타입

## p5.js

```javascript
let radius = 100;
const speed = 0.02;

radius = 150;
// speed = 0.03; // const이므로 재대입 불가
```

JavaScript에서는 `let`과 `const`가 가장 많이 사용됩니다.

- `let`: 다른 값으로 변경할 수 있습니다.
- `const`: 변수 자체에 다른 값을 대입할 수 없습니다.
- 숫자 타입은 정수와 실수를 별도로 구분하지 않습니다.

```javascript
let integerValue = 10;
let decimalValue = 10.5;
```

## OpenRNDR

```kotlin
var radius = 100.0
val speed = 0.02

radius = 150.0
// speed = 0.03 // val이므로 재대입 불가
```

Kotlin에서는 `var`와 `val`을 사용합니다.

- `var`: 값을 변경할 수 있습니다.
- `val`: 다른 값을 재대입할 수 없습니다.

Kotlin은 숫자의 타입을 구분합니다.

```kotlin
val integerValue: Int = 10
val decimalValue: Double = 10.5
val enabled: Boolean = true
val title: String = "OpenRNDR"
```

OpenRNDR의 좌표와 크기 계산에는 `Double`이 자주 사용됩니다.

```kotlin
val x = 100.0
val y = 200.0
```

다음 코드는 타입이 다르기 때문에 오류가 발생합니다.

```kotlin
var value = 100.0
// value = "hello" // Double 변수에 String을 대입할 수 없음
```

정적 타입 시스템은 처음에는 코드가 조금 길어 보이게 만들지만, 큰 프로젝트에서는 잘못된 값의 전달을 컴파일 단계에서 발견할 수 있게 해줍니다.

---

# 5. 함수 문법 비교

## p5.js

```javascript
function calculatePosition(angle, radius) {
  const x = cos(angle) * radius;
  const y = sin(angle) * radius;

  return {
    x: x,
    y: y
  };
}
```

JavaScript는 매개변수와 반환값의 타입을 작성하지 않습니다.

## OpenRNDR

```kotlin
fun calculatePosition(
    angle: Double,
    radius: Double
): Pair<Double, Double> {
    val x = cos(angle) * radius
    val y = sin(angle) * radius

    return Pair(x, y)
}
```

Kotlin에서는 다음을 명시합니다.

- 함수 이름
- 매개변수 이름
- 매개변수 타입
- 반환 타입

Kotlin의 `Pair`와 `to`를 사용하면 다음처럼 줄일 수도 있습니다.

```kotlin
fun calculatePosition(
    angle: Double,
    radius: Double
): Pair<Double, Double> {
    return cos(angle) * radius to
        sin(angle) * radius
}
```

그러나 그래픽 프로그램에서는 `Pair<Double, Double>`보다 벡터 타입을 사용하는 편이 의미를 더 잘 드러냅니다.

```kotlin
import org.openrndr.math.Vector2

fun calculatePosition(
    angle: Double,
    radius: Double
): Vector2 {
    return Vector2(
        cos(angle) * radius,
        sin(angle) * radius
    )
}
```

사용할 때는 다음과 같습니다.

```kotlin
val position = calculatePosition(angle, orbitRadius)

drawer.circle(
    position.x,
    position.y,
    particleRadius
)
```

p5.js의 일반 객체는 자유롭지만, Kotlin의 벡터 타입은 `x`, `y` 성분과 관련 연산을 더 명확하게 다룰 수 있습니다.

---

# 6. 반복문과 범위 표현

## p5.js

```javascript
for (let i = 0; i < particleCount; i++) {
  const angle = i * TWO_PI / particleCount;
}
```

## OpenRNDR

```kotlin
for (i in 0 until particleCount) {
    val angle =
        i * Math.PI * 2.0 / particleCount
}
```

`0 until particleCount`는 마지막 값을 포함하지 않습니다.

```kotlin
0 until 8
```

위 표현은 다음 숫자를 만듭니다.

```text
0, 1, 2, 3, 4, 5, 6, 7
```

반면 `..`은 마지막 값을 포함합니다.

```kotlin
for (i in 0..particleCount) {
}
```

이는 다음과 같이 동작합니다.

```text
0, 1, 2, 3, 4, 5, 6, 7, 8
```

따라서 JavaScript의 일반적인 `i < count` 반복문과 같은 의미를 얻으려면 Kotlin에서는 `until`을 사용하는 것이 자연스럽습니다.

---

# 7. 컬렉션과 배열

## p5.js 배열

```javascript
const particles = [];

for (let i = 0; i < 10; i++) {
  particles.push({
    x: random(width),
    y: random(height),
    radius: random(5, 20)
  });
}

for (const particle of particles) {
  circle(
    particle.x,
    particle.y,
    particle.radius * 2
  );
}
```

JavaScript 배열은 다양한 타입의 값을 담을 수 있고, 객체 리터럴과 결합하기 쉽습니다.

## OpenRNDR 컬렉션

```kotlin
data class Particle(
    val position: Vector2,
    val radius: Double
)

val particles = List(10) {
    Particle(
        position = Vector2(
            Math.random() * width,
            Math.random() * height
        ),
        radius = 5.0 + Math.random() * 15.0
    )
}

for (particle in particles) {
    drawer.circle(
        particle.position.x,
        particle.position.y,
        particle.radius
    )
}
```

Kotlin에서는 데이터 구조를 `data class`로 명확하게 정의할 수 있습니다.

또는 컬렉션 함수를 사용할 수도 있습니다.

```kotlin
particles.forEach { particle ->
    drawer.circle(
        particle.position.x,
        particle.position.y,
        particle.radius
    )
}
```

Kotlin에서는 다음과 같은 컬렉션 처리도 자주 사용합니다.

```kotlin
val largeParticles = particles.filter {
    it.radius > 10.0
}

val positions = particles.map {
    it.position
}
```

p5.js에서는 일반적으로 `for` 루프와 `forEach()`를 사용하고, Kotlin에서는 타입이 보존되는 `map`, `filter`, `mapNotNull`, `fold` 등의 함수를 활용할 수 있습니다.

---

# 8. 클래스와 객체 구조

## p5.js 클래스

```javascript
class Particle {
  constructor(angle, radius) {
    this.angle = angle;
    this.radius = radius;
    this.x = 0;
    this.y = 0;
  }

  update(time, centerX, centerY) {
    this.x = centerX + cos(time + this.angle) * this.radius;
    this.y = centerY + sin(time + this.angle) * this.radius;
  }

  draw() {
    circle(this.x, this.y, 20);
  }
}
```

## OpenRNDR 클래스

```kotlin
import org.openrndr.draw.Drawer
import org.openrndr.math.Vector2

class Particle(
    private val angle: Double,
    private val radius: Double
) {
    private var position = Vector2.ZERO

    fun update(
        time: Double,
        center: Vector2
    ) {
        position = Vector2(
            center.x + cos(time + angle) * radius,
            center.y + sin(time + angle) * radius
        )
    }

    fun draw(drawer: Drawer) {
        drawer.circle(
            position.x,
            position.y,
            10.0
        )
    }
}
```

Kotlin에서는 생성자에 프로퍼티를 바로 선언할 수 있습니다.

```kotlin
class Particle(
    private val angle: Double,
    private val radius: Double
)
```

p5.js의 `this.angle`, `this.radius`에 해당하는 값이 Kotlin에서는 생성자 프로퍼티가 됩니다.

또한 `private`를 사용하면 클래스 외부에서 직접 값을 변경하지 못하게 할 수 있습니다.

---

# 9. 조건문과 표현식

## p5.js

```javascript
let colorValue;

if (isActive) {
  colorValue = color(255, 100, 150);
} else {
  colorValue = color(255);
}
```

또는 삼항 연산자를 사용할 수 있습니다.

```javascript
const colorValue =
  isActive ? activeColor : inactiveColor;
```

## OpenRNDR

Kotlin의 `if`는 값을 반환할 수 있습니다.

```kotlin
val colorValue =
    if (isActive) {
        activeColor
    } else {
        inactiveColor
    }
```

Kotlin에서는 `when`을 사용해 여러 조건을 더 구조적으로 표현할 수도 있습니다.

```kotlin
val colorValue = when {
    value < 0.3 -> ColorRGBa.BLUE
    value < 0.7 -> ColorRGBa.GREEN
    else -> ColorRGBa.RED
}
```

p5.js에서는 보통 `if`, `else if`, `else`를 이어 붙입니다.

```javascript
let colorValue;

if (value < 0.3) {
  colorValue = blue;
} else if (value < 0.7) {
  colorValue = green;
} else {
  colorValue = red;
}
```

`when`은 패턴에 따라 다른 값을 선택하는 그래픽 코드에서 특히 유용합니다.

---

# 10. 색상 문법 비교

## p5.js

```javascript
background(15, 20, 32);

fill(255, 120, 180);
circle(400, 300, 100);

fill(100, 220, 255, 180);
circle(200, 300, 32);
```

p5.js의 기본적인 RGB 값은 0에서 255 사이입니다. 네 번째 값은 알파 값입니다.

## OpenRNDR

OpenRNDR의 `ColorRGBa`는 일반적으로 0.0에서 1.0 사이의 값을 사용합니다.

```kotlin
val color = ColorRGBa(
    255.0 / 255.0,
    120.0 / 255.0,
    180.0 / 255.0,
    1.0
)
```

16진수 색상을 사용하면 더 읽기 쉽습니다.

```kotlin
val pink = ColorRGBa.fromHex("#FF78B4")
val cyan = ColorRGBa.fromHex("#64DCFF")

 drawer.fill = pink
 drawer.circle(400.0, 300.0, 50.0)
```

p5.js의 `fill()`은 전역 그래픽 상태를 변경하고, OpenRNDR에서는 `drawer.fill` 프로퍼티를 변경합니다.

| 작업 | p5.js | OpenRNDR |
|---|---|---|
| 배경 | `background(0)` | `drawer.background(ColorRGBa.BLACK)` |
| 채우기 | `fill(255, 0, 0)` | `drawer.fill = ColorRGBa.RED` |
| 선 색상 | `stroke(255)` | `drawer.stroke = ColorRGBa.WHITE` |
| 선 없음 | `noStroke()` | `drawer.stroke = null` |
| 채우기 없음 | `noFill()` | `drawer.fill = null` |
| 선 두께 | `strokeWeight(2)` | `drawer.strokeWeight = 2.0` |

---

# 11. 도형 API 비교

## p5.js

```javascript
point(x, y);
line(x1, y1, x2, y2);
rect(x, y, width, height);
ellipse(x, y, width, height);
circle(x, y, diameter);
triangle(x1, y1, x2, y2, x3, y3);
```

## OpenRNDR

```kotlin
drawer.point(x, y)
drawer.lineSegment(x1, y1, x2, y2)
drawer.rectangle(x, y, width, height)
drawer.ellipse(x, y, width, height)
drawer.circle(x, y, radius)
drawer.contour(contour)
```

두 API는 비슷한 역할을 하지만 인자의 의미가 조금 다를 수 있습니다.

특히 원의 크기 표현을 주의해야 합니다.

```javascript
// p5.js: 세 번째 인자는 지름
circle(400, 300, 100);
```

```kotlin
// OpenRNDR: 세 번째 인자는 반지름
// 지름 100을 그리려면 반지름 50을 전달
 drawer.circle(400.0, 300.0, 50.0)
```

OpenRNDR은 `Shape`, `Contour`, `Rectangle`, `Circle` 등 그래픽 형상을 객체로 다룰 수 있다는 점도 차이입니다.

---

# 12. 좌표와 벡터 처리

## p5.js

p5.js에서는 좌표를 여러 방식으로 표현할 수 있습니다.

```javascript
const x = 100;
const y = 200;

circle(x, y, 30);
```

벡터를 사용하려면 `createVector()`를 호출합니다.

```javascript
const position = createVector(100, 200);
const velocity = createVector(1, 0.5);

position.add(velocity);
circle(position.x, position.y, 30);
```

## OpenRNDR

OpenRNDR에서는 벡터 타입을 명시적으로 사용할 수 있습니다.

```kotlin
import org.openrndr.math.Vector2

var position = Vector2(100.0, 200.0)
val velocity = Vector2(1.0, 0.5)

position += velocity

drawer.circle(
    position.x,
    position.y,
    15.0
)
```

벡터를 이용하면 공통 중심과 방향 계산을 명확하게 작성할 수 있습니다.

```kotlin
val center = Vector2(width / 2.0, height / 2.0)
val direction = Vector2(cos(angle), sin(angle))
val position = center + direction * orbitRadius
```

p5.js의 `p5.Vector`도 충분히 강력하지만, Kotlin에서는 벡터 타입과 함수의 타입이 컴파일 단계에서 명확하게 유지됩니다.

---

# 13. 시간과 애니메이션

## p5.js

```javascript
function draw() {
  const time = millis() * 0.001;
  const x = width / 2 + cos(time) * 100;

  circle(x, height / 2, 30);
}
```

p5.js의 `millis()`는 프로그램이 시작된 뒤 경과한 시간을 밀리초로 반환합니다.

## OpenRNDR

```kotlin
extend {
    val time = seconds
    val x = width / 2.0 + cos(time) * 100.0

    drawer.circle(
        x,
        height / 2.0,
        15.0
    )
}
```

OpenRNDR에서는 프로그램 시간에 해당하는 `seconds`를 사용해 시간을 초 단위로 다룰 수 있습니다.

시간 값을 직접 누적하는 방식도 가능합니다.

```kotlin
var time = 0.0

extend {
    time += 1.0 / 60.0
}
```

하지만 프레임레이트가 변하면 실제 시간과 어긋날 수 있으므로, 시간 기반 애니메이션에서는 실제 경과 시간 값을 사용하는 편이 안정적입니다.

---

# 14. 마우스와 키보드 이벤트

## p5.js

마우스 위치는 전역 변수로 접근합니다.

```javascript
function draw() {
  circle(mouseX, mouseY, 50);
}
```

키보드 이벤트는 함수 이름을 통해 처리할 수 있습니다.

```javascript
function keyPressed() {
  if (key === ' ') {
    console.log('Space pressed');
  }
}
```

## OpenRNDR

마우스 위치는 `mouse.position`으로 접근합니다.

```kotlin
extend {
    val position = mouse.position
    drawer.circle(position.x, position.y, 25.0)
}
```

이벤트를 Extension으로 연결할 수 있습니다.

```kotlin
extend {
    keyboard.keyDown.listen {
        println("Key pressed: ${it.name}")
    }
}
```

다만 실제 키 이벤트 구성은 OpenRNDR 버전과 사용하는 Extension에 따라 조금 달라질 수 있습니다. 중요한 차이는 p5.js가 전역 콜백 함수 중심인 반면, OpenRNDR은 이벤트 리스너와 프로그램 구조를 통해 입력을 연결한다는 점입니다.

---

# 15. `push()`/`pop()`과 `isolated`

## p5.js

```javascript
push();

translate(width / 2, height / 2);
rotate(angle);
fill(255, 100, 150);
rectMode(CENTER);
rect(0, 0, 100, 100);

pop();
```

`push()`는 현재 그래픽 상태를 저장하고, `pop()`은 이전 상태로 복원합니다.

## OpenRNDR

```kotlin
drawer.isolated {
    translate(width / 2.0, height / 2.0)
    rotate(angle)

    fill = ColorRGBa.PINK
    rectangle(
        -50.0,
        -50.0,
        100.0,
        100.0
    )
}
```

OpenRNDR의 `isolated {}` 블록은 다음 상태를 격리합니다.

- 위치 변환
- 회전
- 크기 조절
- 채우기 색상
- 선 색상
- 선 두께
- 블렌딩 관련 상태

중첩 구조도 작성할 수 있습니다.

```kotlin
drawer.isolated {
    translate(400.0, 300.0)

    drawer.isolated {
        rotate(angle)
        rectangle(-50.0, -50.0, 100.0, 100.0)
    }
}
```

p5.js의 `push()`와 `pop()`에 비해 Kotlin 블록 구조가 상태의 범위를 더 명확하게 보여준다는 장점이 있습니다.

---

# 16. 람다와 함수형 문법

p5.js에도 함수는 일급 객체이므로 콜백을 자주 사용합니다.

```javascript
particles.forEach((particle) => {
  particle.update();
  particle.draw();
});
```

Kotlin에서도 람다를 사용할 수 있습니다.

```kotlin
particles.forEach { particle ->
    particle.update()
    particle.draw(drawer)
}
```

매개변수가 하나일 때는 `it`을 사용할 수 있습니다.

```kotlin
particles.forEach {
    it.update()
    it.draw(drawer)
}
```

조건에 맞는 객체를 선택하는 코드도 간결합니다.

```kotlin
particles
    .filter { it.radius > 10.0 }
    .forEach { it.draw(drawer) }
```

Kotlin의 람다와 컬렉션 API는 파티클, 노드, 메시, 데이터 시각화 요소를 처리할 때 유용합니다.

---

# 17. Null 안전성과 그래픽 상태

JavaScript에서는 값이 없을 때 `null` 또는 `undefined`가 사용될 수 있습니다.

```javascript
let selectedParticle = null;

if (selectedParticle !== null) {
  selectedParticle.draw();
}
```

Kotlin에서는 nullable 타입을 명시합니다.

```kotlin
var selectedParticle: Particle? = null

selectedParticle?.draw(drawer)
```

`?.`는 값이 null이 아닐 때만 함수를 호출합니다.

```kotlin
val radius = selectedParticle?.radius ?: 0.0
```

위 코드는 `selectedParticle`이 없으면 0.0을 사용합니다.

OpenRNDR에서 `drawer.fill`이나 `drawer.stroke`를 `null`로 설정하는 것도 Kotlin의 nullable 타입과 관련된 사용 방식입니다.

```kotlin
drawer.stroke = null
drawer.fill = null
```

p5.js는 값이 없는 상태를 비교적 자유롭게 처리하지만, Kotlin은 값이 없을 가능성을 코드에 명시하게 하므로 오류의 원인을 추적하기 쉬운 편입니다.

---

# 18. import와 개발 환경

## p5.js

p5.js에서는 HTML 파일에서 라이브러리를 불러옵니다.

```html
<script src="https://cdn.jsdelivr.net/npm/p5@1.11.0/lib/p5.min.js"></script>
<script src="sketch.js"></script>
```

JavaScript 파일에서는 p5.js의 전역 함수와 상수를 바로 사용할 수 있습니다.

```javascript
createCanvas(800, 600);
circle(100, 100, 50);
```

## OpenRNDR

OpenRNDR은 Kotlin import를 작성합니다.

```kotlin
import org.openrndr.application
import org.openrndr.color.ColorRGBa
import org.openrndr.math.Vector2
```

IntelliJ IDEA에서는 클래스 이름을 입력한 뒤 `Alt + Enter`를 누르면 가능한 import를 자동으로 추가할 수 있습니다.

사용하지 않는 import는 다음 단축키로 정리할 수 있습니다.

- Windows/Linux: `Ctrl + Alt + O`
- macOS: `Control + Option + O`

또한 Gradle 프로젝트에 의존성이 정상적으로 등록되어 있으면 OpenRNDR의 클래스, 함수, 타입에 대한 자동 완성과 문서 탐색도 사용할 수 있습니다.

이 점은 OpenRNDR의 중요한 장점입니다.

- 클래스 이름 자동 완성
- 함수 매개변수 정보 표시
- 타입 오류 즉시 표시
- import 자동 추가
- 사용하지 않는 import 정리
- 클래스와 함수의 참조 위치 탐색
- 이름 변경 리팩터링
- Gradle 의존성 자동 인식

p5.js는 브라우저 개발자 도구와 에디터 확장 기능을 활용하지만, Kotlin과 IntelliJ IDEA 조합처럼 강한 정적 분석 경험을 기본으로 제공하지는 않습니다.

---

# 19. 셰이더 코드와 렌더링 구조

## p5.js 셰이더 사용 예

p5.js에서는 셰이더를 로드한 뒤 캔버스에 적용하는 방식으로 사용할 수 있습니다.

```javascript
let shaderProgram;

function preload() {
  shaderProgram = loadShader(
    'shader.vert',
    'shader.frag'
  );
}

function setup() {
  createCanvas(800, 600, WEBGL);
  shader(shaderProgram);
}

function draw() {
  shaderProgram.setUniform('u_time', millis() * 0.001);
  shaderProgram.setUniform(
    'u_resolution',
    [width, height]
  );

  plane(width, height);
}
```

## OpenRNDR 셰이더 사용 예

OpenRNDR에서는 `shadeStyle`과 렌더 타깃을 결합해 셰이더 기반 효과를 구성할 수 있습니다.

```kotlin
drawer.shadeStyle = shadeStyle {
    parameter("u_time", seconds)

    fragmentTransform = """
        vec2 uv = c_boundsPosition;
        float value = sin(u_time + uv.x * 10.0);
        x_fill = vec4(uv.x, uv.y, value, 1.0);
    """
}
```

일반적으로 p5.js는 웹 GLSL과 WebGL 컨텍스트를 직접 연결하는 느낌이 강하고, OpenRNDR은 Kotlin 코드 안에서 렌더링 상태와 셰이더 파라미터를 함께 관리하는 느낌이 강합니다.

두 프레임워크 모두 GLSL을 사용할 수 있지만, OpenRNDR은 다음 구조를 조합하기 좋습니다.

- `RenderTarget`
- `ShadeStyle`
- `Drawer`
- 다중 렌더링 단계
- 후처리 Extension
- Kotlin으로 관리하는 셰이더 파라미터

---

# 20. 에러가 나타나는 시점

## p5.js

다음 코드는 실행해야 오류를 확인할 수 있습니다.

```javascript
let radius = 100;
radius.toUpperCase();
```

`radius`가 숫자라는 사실을 코드 작성 단계에서 강제하지 않기 때문에 실행 중 오류가 발생합니다.

## OpenRNDR

```kotlin
val radius: Double = 100.0
// radius.toUpperCase() // 컴파일 단계에서 오류
```

Kotlin은 `Double`에 존재하지 않는 메서드를 호출하려는 사실을 컴파일 단계에서 알려줍니다.

이 차이는 다음과 같이 정리할 수 있습니다.

| 상황 | p5.js | OpenRNDR |
|---|---|---|
| 변수 타입 확인 | 실행 시점 중심 | 컴파일 시점 중심 |
| 오타 발견 | 실행하거나 린터 필요 | IDE가 즉시 표시 |
| 함수 인자 오류 | 실행 시점 가능 | 컴파일 단계에서 발견 가능 |
| 빠른 실험 | 매우 편리 | 상대적으로 선언이 필요 |
| 대규모 구조 | 규칙을 직접 관리 | 타입과 IDE가 보조 |

---

# 21. 문법적으로 느껴지는 사용 경험

## p5.js의 장점

p5.js는 다음과 같은 코드를 매우 빠르게 실행할 수 있습니다.

```javascript
function setup() {
  createCanvas(400, 400);
}

function draw() {
  background(0);
  circle(mouseX, mouseY, 50);
}
```

이 코드는 그래픽 프로그래밍을 처음 접하는 사람도 실행 결과를 바로 확인할 수 있습니다.

p5.js는 특히 다음 작업에 적합합니다.

- 짧은 시각 실험
- 수업용 예제
- 브라우저 인터랙션
- 웹 기반 포트폴리오
- 간단한 제너러티브 아트
- DOM과 그래픽을 함께 사용하는 작업

## OpenRNDR의 장점

OpenRNDR은 다음처럼 코드 구조가 조금 더 명시적입니다.

```kotlin
fun main() = application {
    configure {
        width = 400
        height = 400
    }

    program {
        extend {
            drawer.background(ColorRGBa.BLACK)
            drawer.circle(
                mouse.position.x,
                mouse.position.y,
                25.0
            )
        }
    }
}
```

초기 코드는 p5.js보다 길지만, 다음 작업에서는 장점이 커집니다.

- 여러 클래스로 구성된 프로그램
- 복잡한 수학 계산
- 벡터와 행렬 연산
- 셰이더와 렌더 타깃 관리
- 많은 파티클과 그래픽 객체
- 여러 Extension을 조합하는 프로젝트
- IntelliJ 리팩터링과 타입 검사

---

# 22. 같은 로직을 더 구조화해 작성하기

## p5.js의 Particle 배열

```javascript
let particles = [];

function setup() {
  createCanvas(800, 600);

  for (let i = 0; i < 8; i++) {
    particles.push(
      new Particle(
        i * TWO_PI / 8,
        150
      )
    );
  }
}

function draw() {
  background(15, 20, 32);

  const center = createVector(mouseX, mouseY);
  const time = millis() * 0.001;

  for (const particle of particles) {
    particle.update(time, center);
    particle.draw();
  }
}
```

## OpenRNDR의 Particle 리스트

```kotlin
val particles = List(8) { index ->
    Particle(
        angle = index * Math.PI * 2.0 / 8.0,
        radius = 150.0
    )
}

extend {
    drawer.background(ColorRGBa.fromHex("#0F1420"))

    val center = mouse.position
    val time = seconds

    particles.forEach { particle ->
        particle.update(time, center)
        particle.draw(drawer)
    }
}
```

두 코드의 처리 흐름은 같습니다.

1. 파티클을 생성합니다.
2. 매 프레임 중심 위치를 계산합니다.
3. 각 파티클의 위치를 갱신합니다.
4. 파티클을 그립니다.

그러나 OpenRNDR에서는 `Particle`의 생성자 타입, `update()`의 매개변수 타입, `draw()`의 `Drawer` 의존성이 명확합니다.

---

# 23. 성능과 문법의 관계

문법이 간결하다고 항상 더 빠른 것은 아닙니다. 두 프레임워크 모두 실제 성능은 다음 요소에 크게 좌우됩니다.

- 매 프레임 생성되는 객체 수
- 배열 또는 컬렉션의 재할당
- CPU에서 수행하는 계산량
- 드로우 콜 수
- 이미지와 텍스처 전환 횟수
- 셰이더의 복잡도
- GPU 버퍼 사용 방식

p5.js에서는 다음과 같은 코드가 매 프레임 많은 배열과 객체를 생성할 수 있습니다.

```javascript
function draw() {
  const positions = [];

  for (let i = 0; i < 10000; i++) {
    positions.push(createVector(i, i));
  }
}
```

OpenRNDR에서도 매 프레임 `List`나 `Vector2` 객체를 새로 만드는 코드는 불필요한 객체 생성과 GC를 유발할 수 있습니다.

```kotlin
extend {
    val positions = List(10000) {
        Vector2(it.toDouble(), it.toDouble())
    }
}
```

따라서 어느 쪽을 사용하든 반복적으로 생성되는 데이터는 미리 만들고 재사용하는 편이 좋습니다.

```kotlin
val positions = MutableList(10000) {
    Vector2.ZERO
}

extend {
    // 기존 리스트를 갱신해서 사용
}
```

OpenRNDR의 정적 타입과 JVM 구조는 규모 있는 데이터 처리를 체계화하는 데 도움을 주지만, Kotlin 객체 생성과 컬렉션 처리에 대한 이해가 필요합니다.

---

# 24. 선택 기준

## p5.js가 적합한 경우

- 브라우저에서 바로 실행해야 할 때
- 짧은 코드로 시각적 결과를 확인하고 싶을 때
- 교육용 예제를 만들 때
- HTML, CSS, DOM과 결합할 때
- 웹 포트폴리오나 인터랙티브 페이지를 만들 때
- JavaScript 생태계를 활용할 때

## OpenRNDR이 적합한 경우

- Kotlin과 IntelliJ IDEA를 선호할 때
- 타입이 명확한 그래픽 코드를 작성하고 싶을 때
- 복잡한 수학적 모델을 구현할 때
- 여러 클래스와 모듈로 프로젝트를 구성할 때
- 벡터, 행렬, 셰이더, 렌더 타깃을 적극적으로 사용할 때
- Gradle 의존성 관리가 필요한 데스크톱 프로젝트를 만들 때
- 제너러티브 아트 시스템을 장기적으로 발전시킬 때

---

# 25. 최종 정리

문법과 코드 작성 경험을 기준으로 하면 다음과 같이 정리할 수 있습니다.

> **p5.js는 캔버스에 명령을 빠르게 전달하는 스케치 언어에 가깝고, OpenRNDR은 타입이 있는 그래픽 애플리케이션을 구성하는 프레임워크에 가깝습니다.**

p5.js는 다음과 같은 형태로 시작합니다.

```javascript
function setup() {}
function draw() {}
```

OpenRNDR은 다음과 같은 형태로 시작합니다.

```kotlin
fun main() = application {
    program {
        extend {
        }
    }
}
```

p5.js의 강점은 **즉시성**입니다. 코드를 짧게 작성하고 브라우저에서 결과를 빠르게 확인할 수 있습니다.

OpenRNDR의 강점은 **구조화와 명확성**입니다. Kotlin의 정적 타입, 데이터 클래스, 벡터 타입, 람다, 컬렉션 API, IntelliJ 자동 완성 및 리팩터링을 활용해 큰 그래픽 프로젝트를 구성할 수 있습니다.

특히 수학 기반 제너러티브 아트, 노이즈, fBm, SDF, 도메인 워핑, 파티클 시스템, GLSL 셰이더처럼 코드가 복잡해지는 작업에서는 OpenRNDR의 구조화된 문법이 유리할 수 있습니다. 반대로 웹에서 바로 공유되는 인터랙티브 스케치와 짧은 교육 예제는 p5.js가 더 자연스럽습니다.

| 목적 | 더 적합한 선택 |
|---|---|
| 브라우저에서 즉시 실행 | p5.js |
| 짧은 그래픽 실험 | p5.js |
| 교육용 그래픽 예제 | p5.js |
| HTML·CSS·DOM 연동 | p5.js |
| IntelliJ 기반 개발 | OpenRNDR |
| 정적 타입과 리팩터링 | OpenRNDR |
| 대규모 제너러티브 아트 | OpenRNDR |
| 복잡한 벡터·수학 구조 | OpenRNDR |
| Kotlin/JVM 라이브러리 연동 | OpenRNDR |
| 셰이더와 다중 렌더링 구조 | OpenRNDR |
