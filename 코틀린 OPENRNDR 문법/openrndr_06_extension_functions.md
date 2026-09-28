# OPENRNDR 시작하기 (6): Extension 함수 — OPENRNDR 스타일의 핵심

지금까지 `Drawer`의 내장 메서드를 사용했어요:

```kotlin
extend {
    drawer.clear(ColorRGBa.BLACK)
    drawer.circle(400.0, 400.0, 100.0)
}
```

**그런데 만약 자주 쓰는 패턴이 반복된다면?**

예를 들어, 격자를 매번 이렇게 그려야 한다면:

```kotlin
extend {
    for (x in 0 until 10) {
        for (y in 0 until 10) {
            val px = x * 80.0
            val py = y * 80.0
            circle(px, py, 20.0)
        }
    }
}
```

**반복되는 패턴을 하나의 메서드로 만들 수 있다면?**

```kotlin
extend {
    grid(10, 10, 80.0, 20.0)  // 훨씬 간단!
}
```

**이것이 Extension 함수입니다.**

이 글에서는:
- **Extension 함수가 무엇인가**
- **왜 OPENRNDR 스타일의 핵심인가**
- **실제로 자주 쓰이는 Extension 함수 패턴들**

을 다루겠습니다.

---

## Extension 함수란?

**Extension 함수**는 **기존 클래스에 새로운 메서드를 "붙이는" 기능**입니다.

### 기본 문법

```kotlin
// 정수 클래스에 새 메서드 추가
fun Int.isEven(): Boolean {
    return this % 2 == 0
}

// 사용
val num = 10
println(num.isEven())  // true
```

**구조:**
```
fun 클래스이름.메서드이름(파라미터): 반환타입 {
    // this는 자동으로 클래스의 인스턴스
}
```

### 더 복잡한 예제

```kotlin
// String 클래스에 "단어 개수 세기" 메서드 추가
fun String.wordCount(): Int {
    return this.split(" ").size
}

val text = "Hello World OPENRNDR"
println(text.wordCount())  // 3
```

**여기서 `this`는 자동으로 `String` 객체**입니다.

---

## OPENRNDR에서의 Extension 함수

### 왜 중요한가?

OPENRNDR의 철학: **자주 쓰는 패턴을 재사용 가능한 메서드로 만들자**

```kotlin
// 1단계: 반복되는 코드 발견
extend {
    // 격자 패턴을 매번 이렇게 쓴다
    for (x in 0 until 10) {
        for (y in 0 until 10) {
            circle(x * 80.0, y * 80.0, 20.0)
        }
    }
}

// 2단계: Extension 함수로 추상화
fun Drawer.grid(cols: Int, rows: Int, spacing: Double, radius: Double = 20.0) {
    for (x in 0 until cols) {
        for (y in 0 until rows) {
            circle(x * spacing, y * spacing, radius)
        }
    }
}

// 3단계: 간단하게 사용
extend {
    grid(10, 10, 80.0)  // 매우 간결!
}
```

---

## p5.js와의 비교

### p5.js: 재사용이 어려움

p5.js에서는 새 함수를 만들어야 합니다:

```javascript
function drawGrid(cols, rows, spacing) {
    for (let x = 0; x < cols; x++) {
        for (let y = 0; y < rows; y++) {
            circle(x * spacing, y * spacing, 20);
        }
    }
}

function draw() {
    background(0);
    drawGrid(10, 10, 80);
}
```

**문제점:**
- `drawGrid`와 `circle`의 관계가 명확하지 않음
- 전역 함수 네임스페이스가 오염됨

### OPENRNDR: 메서드처럼 사용

```kotlin
fun Drawer.grid(cols: Int, rows: Int, spacing: Double) {
    for (x in 0 until cols) {
        for (y in 0 until rows) {
            circle(x * spacing, y * spacing, 20.0)
        }
    }
}

extend {
    grid(10, 10, 80.0)  // Drawer의 메서드처럼!
}
```

**장점:**
- `grid`는 명확하게 `Drawer`의 메서드
- 리시버 덕분에 `drawer.` 없이도 사용 가능
- 의도가 명확함 (이것은 드로잉 메서드)

---

## 실전 예제 1: 격자 그리기

```kotlin
// Extension 함수 정의
fun Drawer.grid(
    cols: Int,
    rows: Int,
    spacing: Double,
    radius: Double = 20.0
) {
    for (x in 0 until cols) {
        for (y in 0 until rows) {
            circle(x * spacing, y * spacing, radius)
        }
    }
}

program {
    extend {
        clear(ColorRGBa.BLACK)
        fill = ColorRGBa.WHITE
        grid(12, 12, 60.0, 15.0)
    }
}
```

**사용의 편의성:**
- `grid(12, 12, 60.0, 15.0)` 한 줄
- 원래라면 이중 `for` 루프를 매번 작성해야 함

---

## 실전 예제 2: 원형 배치

```kotlin
// 중심 주위에 원형으로 배치
fun Drawer.radialLayout(
    count: Int,
    distance: Double,
    radius: Double = 20.0
) {
    for (i in 0 until count) {
        val angle = TWO_PI / count * i
        val x = distance * cos(angle)
        val y = distance * sin(angle)
        circle(x, y, radius)
    }
}

program {
    extend {
        clear(ColorRGBa.BLACK)
        fill = ColorRGBa.PINK
        
        // 중심에서 12개 원을 150 픽셀 거리에 배치
        radialLayout(12, 150.0, 15.0)
    }
}
```

---

## 실전 예제 3: 나선 그리기

```kotlin
// 나선 패턴
fun Drawer.spiral(
    count: Int,
    spacing: Double,
    rotation: Double = 0.0
) {
    for (i in 0 until count) {
        val angle = rotation + i * 0.2
        val radius = i * spacing
        val x = radius * cos(angle)
        val y = radius * sin(angle)
        point(x, y)
    }
}

program {
    var t = 0.0
    
    extend {
        clear(ColorRGBa.BLACK)
        strokeWeight = 2.0
        stroke = ColorRGBa.CYAN
        
        spiral(500, 0.5, t)  // 회전하는 나선
        
        t += 0.02
    }
}
```

---

## 고급: `Drawer` + `Vector2` Extension

### Vector2에 메서드 추가

```kotlin
// Vector2의 각도와 거리를 얻기
fun Vector2.angle(): Double {
    return atan2(y, x)
}

fun Vector2.magnitude(): Double {
    return sqrt(x * x + y * y)
}

// 사용
val v = Vector2(3.0, 4.0)
println(v.magnitude())  // 5.0
println(v.angle())      // ~0.93 라디안
```

### Drawer와 Vector2 Extension 조합

```kotlin
// Vector2 리스트를 경로로 그리기
fun Drawer.path(points: List<Vector2>, closed: Boolean = false) {
    if (points.isEmpty()) return
    
    for (i in 0 until points.size - 1) {
        lineSegment(points[i], points[i + 1])
    }
    
    if (closed) {
        lineSegment(points.last(), points.first())
    }
}

program {
    extend {
        clear(ColorRGBa.BLACK)
        stroke = ColorRGBa.WHITE
        
        val triangle = listOf(
            Vector2(400.0, 200.0),
            Vector2(600.0, 400.0),
            Vector2(200.0, 400.0)
        )
        
        path(triangle, closed = true)  // 삼각형 그리기
    }
}
```

---

## 기본 파라미터 값 (Default Parameters)

Extension 함수에서 기본값을 지정할 수 있습니다:

```kotlin
// 반지름의 기본값 설정
fun Drawer.grid(
    cols: Int,
    rows: Int,
    spacing: Double,
    radius: Double = 20.0  // 기본값
) {
    for (x in 0 until cols) {
        for (y in 0 until rows) {
            circle(x * spacing, y * spacing, radius)
        }
    }
}

// 사용
extend {
    grid(10, 10, 80.0)          // radius = 20.0 (기본값)
    grid(10, 10, 80.0, 30.0)    // radius = 30.0 (명시)
}
```

**좋은 기본값 설계:**
- 자주 쓰는 값을 기본값으로
- 변경 가능성이 있는 값만 파라미터로

---

## 실전 예제 4: 복잡한 패턴

```kotlin
// 동심원(concentric circles)
fun Drawer.concentricCircles(
    count: Int,
    startRadius: Double,
    spacing: Double,
    centerX: Double = 400.0,
    centerY: Double = 400.0
) {
    for (i in 0 until count) {
        val radius = startRadius + i * spacing
        circle(centerX, centerY, radius)
    }
}

// 격자 + 동심원 조합
program {
    var t = 0.0
    
    extend {
        clear(ColorRGBa.BLACK)
        fill = ColorRGBa.WHITE
        
        // 격자
        for (x in 0 until 5) {
            for (y in 0 until 5) {
                val cx = 100.0 + x * 160.0
                val cy = 100.0 + y * 160.0
                
                concentricCircles(
                    count = 3,
                    startRadius = 20.0,
                    spacing = 15.0,
                    centerX = cx,
                    centerY = cy
                )
            }
        }
        
        t += 0.01
    }
}
```

---

## 리시버 체이닝

Extension 함수는 **다른 Extension 함수 안에서도 사용**할 수 있습니다:

```kotlin
// 기본 Extension
fun Drawer.box(
    x: Double,
    y: Double,
    width: Double,
    height: Double,
    cornerRadius: Double = 0.0
) {
    rectangle(x, y, width, height)
    // 모서리 처리 등...
}

// 다른 Extension을 사용하는 새로운 Extension
fun Drawer.tiled(
    cols: Int,
    rows: Int,
    width: Double,
    height: Double
) {
    for (x in 0 until cols) {
        for (y in 0 until rows) {
            val px = x * width
            val py = y * height
            box(px, py, width, height)  // 다른 Extension 사용
        }
    }
}

extend {
    tiled(5, 5, 100.0, 100.0)
}
```

---

## 유틸리티 Extension 라이브러리 만들기

자주 쓰는 Extension들을 한 파일에 모아서 라이브러리처럼 사용할 수 있습니다:

```kotlin
// DrawerExtensions.kt (또는 아무 파일)

// 격자
fun Drawer.grid(cols: Int, rows: Int, spacing: Double, radius: Double = 20.0) {
    for (x in 0 until cols) {
        for (y in 0 until rows) {
            circle(x * spacing, y * spacing, radius)
        }
    }
}

// 원형 배치
fun Drawer.radialLayout(count: Int, distance: Double, radius: Double = 20.0) {
    for (i in 0 until count) {
        val angle = TWO_PI / count * i
        val x = distance * cos(angle)
        val y = distance * sin(angle)
        circle(x, y, radius)
    }
}

// 경로 그리기
fun Drawer.path(points: List<Vector2>, closed: Boolean = false) {
    if (points.isEmpty()) return
    for (i in 0 until points.size - 1) {
        lineSegment(points[i], points[i + 1])
    }
    if (closed) {
        lineSegment(points.last(), points.first())
    }
}

// 나선
fun Drawer.spiral(count: Int, spacing: Double, rotation: Double = 0.0) {
    for (i in 0 until count) {
        val angle = rotation + i * 0.2
        val radius = i * spacing
        val x = radius * cos(angle)
        val y = radius * sin(angle)
        point(x, y)
    }
}

// --------- 메인 파일에서 사용 ---------

program {
    extend {
        clear(ColorRGBa.BLACK)
        fill = ColorRGBa.WHITE
        
        // 모든 Extension 메서드 사용 가능
        grid(10, 10, 80.0)
        radialLayout(8, 150.0)
        spiral(200, 1.0)
    }
}
```

---

## Vector2 Extension의 실용성

### 벡터 연산 확장

```kotlin
// 정규화된 벡터 (방향만, 거리는 1)
fun Vector2.normalized(): Vector2 {
    val len = sqrt(x * x + y * y)
    if (len == 0.0) return Vector2(0.0, 0.0)
    return Vector2(x / len, y / len)
}

// 벡터의 회전
fun Vector2.rotated(angleRadians: Double): Vector2 {
    val cos = cos(angleRadians)
    val sin = sin(angleRadians)
    return Vector2(
        x * cos - y * sin,
        x * sin + y * cos
    )
}

// 사용
val v = Vector2(1.0, 1.0)
val rotated = v.rotated(PI / 4)  // 45도 회전
val norm = v.normalized()        // 정규화
```

### 실제 활용

```kotlin
program {
    extend {
        clear(ColorRGBa.BLACK)
        
        val center = Vector2(400.0, 400.0)
        val direction = (mouse.position - center).normalized()
        
        // direction 방향으로 선 그리기
        lineSegment(center, center + direction * 150.0)
    }
}
```

---

## 실전 예제: 상호작용하는 나선

```kotlin
// 마우스를 따라다니는 나선
fun Drawer.spiralTowards(
    count: Int,
    spacing: Double,
    targetPosition: Vector2,
    rotation: Double = 0.0
) {
    for (i in 0 until count) {
        val angle = rotation + i * 0.2
        val radius = i * spacing
        
        val x = radius * cos(angle)
        val y = radius * sin(angle)
        
        val point = Vector2(x, y) + targetPosition
        circle(point.x, point.y, 2.0)
    }
}

program {
    var t = 0.0
    
    extend {
        clear(ColorRGBa.BLACK)
        fill = ColorRGBa.CYAN
        
        spiralTowards(200, 0.8, mouse.position, t)
        
        t += 0.02
    }
}
```

---

## 조건부 로직이 있는 Extension

```kotlin
// 격자 + 선택적 필터링
fun Drawer.selectiveGrid(
    cols: Int,
    rows: Int,
    spacing: Double,
    radius: Double = 20.0,
    shouldDraw: (Int, Int) -> Boolean = { _, _ -> true }  // 기본: 모두 그리기
) {
    for (x in 0 until cols) {
        for (y in 0 until rows) {
            if (shouldDraw(x, y)) {
                circle(x * spacing, y * spacing, radius)
            }
        }
    }
}

// 사용 1: 기본 (모두 그리기)
extend {
    selectiveGrid(10, 10, 80.0)
}

// 사용 2: 체스판 패턴
extend {
    selectiveGrid(10, 10, 80.0) { x, y ->
        (x + y) % 2 == 0
    }
}

// 사용 3: 대각선 패턴
extend {
    selectiveGrid(10, 10, 80.0) { x, y ->
        x == y
    }
}
```

**고급 기능:**
- 마지막 파라미터는 람다 (조건 함수)
- 기본값으로 "모두 그리기" 제공
- 호출할 때 조건을 커스터마이징

---

## 정리: Extension 함수의 이점

| 이점 | 설명 |
|------|------|
| **재사용성** | 반복되는 패턴을 한 번만 정의 |
| **명확성** | 코드의 의도가 명확함 |
| **가독성** | 복잡한 로직을 간단한 메서드 호출로 |
| **유지보수** | 패턴 수정 시 한 곳만 변경 |
| **메서드 체이닝** | Extension을 조합해서 복잡한 작업 구성 |
| **라이브러리화** | 유틸리티 함수들을 체계적으로 관리 |

---

## p5.js와의 차이

### p5.js: 전역 함수

```javascript
function drawGrid(cols, rows, spacing) {
    for (let x = 0; x < cols; x++) {
        for (let y = 0; y < rows; y++) {
            circle(x * spacing, y * spacing, 20);
        }
    }
}

function draw() {
    drawGrid(10, 10, 80);
}
```

문제: 전역 네임스페이스 오염, 의도 불명확

### OPENRNDR: Extension 함수

```kotlin
fun Drawer.grid(cols: Int, rows: Int, spacing: Double) {
    for (x in 0 until cols) {
        for (y in 0 until rows) {
            circle(x * spacing, y * spacing, 20.0)
        }
    }
}

extend {
    grid(10, 10, 80.0)
}
```

장점: 명확한 관계, 체계적 조직, 메서드처럼 사용

---

## 다음 단계: `when` 조건 분기

다음 글에서는 **`when` 문법**을 다룹니다.

`if-else`보다 훨씬 강력한 조건 분기:

```kotlin
val mode = getUserInput()

when (mode) {
    0 -> drawGrid(10, 10, 80.0)
    1 -> radialLayout(12, 150.0)
    2 -> spiral(200, 1.0)
    else -> clear(ColorRGBa.BLACK)
}
```

이것이 사용자 입력에 따라 다른 패턴을 그리는 인터랙티브 아트를 가능하게 합니다.

---

## 연습 문제

### 1. 기본 Extension 만들기

```kotlin
// 정수에 "2배가 되는가" 메서드 추가
fun Int.isDouble(other: Int): Boolean {
    return this == other * 2
}

// 테스트
println(10.isDouble(5))   // true
println(10.isDouble(3))   // false
```

### 2. Drawer Extension 만들기

```kotlin
// 정사각형 그리기 (중심 기준)
fun Drawer.square(
    centerX: Double,
    centerY: Double,
    size: Double
) {
    val half = size / 2
    rectangle(centerX - half, centerY - half, size, size)
}

extend {
    square(400.0, 400.0, 100.0)
}
```

### 3. 격자 개선

```kotlin
// 격자를 Drawer의 메서드로
fun Drawer.grid(cols: Int, rows: Int, spacing: Double) {
    for (x in 0 until cols) {
        for (y in 0 until rows) {
            circle(x * spacing, y * spacing, 20.0)
        }
    }
}

program {
    extend {
        clear(ColorRGBa.BLACK)
        fill = ColorRGBa.WHITE
        grid(12, 12, 60.0)
    }
}
```

실행해 본 후:
- 반지름을 파라미터로 추가해보세요
- 기본값을 설정해보세요

### 4. 복합 Extension

```kotlin
// Vector2 리스트를 시각화
fun Drawer.visualizePoints(points: List<Vector2>, radius: Double = 5.0) {
    for (point in points) {
        circle(point.x, point.y, radius)
    }
}

program {
    extend {
        clear(ColorRGBa.BLACK)
        fill = ColorRGBa.PINK
        
        val points = (0 until 20)
            .map { Vector2(it * 40.0, 200.0 + 100.0 * sin(it.toDouble())) }
        
        visualizePoints(points, 10.0)
    }
}
```

### 5. 심화: 조건부 Grid

앞의 "조건부 로직이 있는 Extension" 예제를 실행해 보고:
- 다양한 패턴을 만들어 보세요 (대각선, 체스판, 테두리만 등)
- 여러 조건을 조합해 보세요

---

**이전 글**: [OPENRNDR 시작하기 (5): 컬렉션과 함수형 파이프라인](./openrndr_05_collections_functional.md)  
**다음 글**: OPENRNDR 시작하기 (7): `when` — 코틀린의 강력한 조건 분기
