OPENRNDR은 코틀린의 언어 특성(람다, 확장 함수, DSL, 타입 추론)을 적극적으로 활용하므로, "그냥 코틀린 문법"보다 **OPENRNDR에서 실제로 마주치는 패턴** 중심으로 정리하는 게 효율적입니다.

***

## 1. 프로그램 구조 — `fun main`과 `extend`

```kotlin
fun main() = application {
    configure {
        width = 800
        height = 800
    }
    program {
        extend {
            // ← 매 프레임 실행되는 블록
            drawer.clear(ColorRGBa.BLACK)
            drawer.circle(400.0, 400.0, 100.0)
        }
    }
}
```

* `application { }` — 전체 앱을 시작하는 DSL 블록입니다
* `extend { }` — **매 프레임 실행**되는 핵심 루프이며, 리시버가 `Drawer`라서 `drawer.` 생략 가능합니다

***

## 2. 람다 표현식과 리시버 (Lambda with Receiver)

OPENRNDR 코드 곳곳에서 필수이며, `extend` 블록의 핵심 메커니즘입니다.

```kotlin
// 기본 형태: 인자가 하나면 it 사용
val randomPoint = (0 until 100).map { Vector2(random() * width, random() * height) }

// 이름 붙이기
val sorted = points.sortedBy { p -> p.y }

// 여러 개 사용할 때는 명시적으로
val result = list.map { v -> v * 2 }.filter { v -> v > 10 }
```

**리시버가 있는 람다** (OPENRNDR 핵심):
```kotlin
extend { // this == Drawer (암묵적)
    clear(ColorRGBa.BLACK)
    circle(400.0, 400.0, 100.0)
}

// 명시적 리시버 이름이 필요할 땐
extend { drawer ->
    drawer.circle(x, y, r)
}
```

**마지막 인자가 람다면 소괄호 밖으로:**
```kotlin
extend({ ... })       // 정식 형태
extend { ... }        // 관례적 형태 (권장)
```

***

## 3. `val` / `var` — 불변성의 설계

```kotlin
val position = Vector2(0.0, 0.0)  // 재할당 불가
var time = 0.0                     // 값이 변해야 할 때만

val mutableList = mutableListOf<Double>() // val이어도 내용물은 변경 가능
```

OPENRNDR 아트워크에서 `val`(씨드, 파라미터)과 `var`(시간, 상태)의 구분이 **재현성과 변동성**을 관리합니다.

***

## 4. 범위와 반복 — `for`, `repeat`, 람다 형태

```kotlin
for (i in 0 until 100) { }        // 0..99
for (i in 0..100 step 2) { }      // 0, 2, 4, ..., 100 (양 끝 포함)
for (i in 100 downTo 0) { }       // 내림차순

repeat(50) { i ->                 // 람다 버전, i는 인덱스
    drawer.rectangle(i * 10.0, 0.0, 5.0, 200.0)
}
```

***

## 5. 컬렉션과 함수형 파이프라인 (핵심 패턴)

제너러티브 작업에서 점의 리스트 만들고 필터링하고 그리는 패턴이 핵심입니다.

```kotlin
val particles = List(200) { i ->
    Particle(Vector2(random() * width, random() * height))
}

particles
    .filter { p -> p.isAlive }
    .forEach { p -> drawer.circle(p.pos.x, p.pos.y, p.size) }
```

주요 함수: `map`, `filter`, `forEach`, `sortedBy`, `groupBy`, `flatMap`, `fold`, `take(n)`, `drop(n)`, `zip`.

```kotlin
// 좋은 예: 위치를 하나로 결합
val xs = (0..9).map { it * 100.0 }
val ys = List(10) { random() * height }
val points = xs.zip(ys).map { (x, y) -> Vector2(x, y) }
```

> **주의**: `forEach` 안에서 `drawer.circle(...)`처럼 부수효과(그리기)가 있는 경우에만 쓰고, 값을 만들어낼 땐 `map`을 쓰세요. 이 구분이 성능과 가독성 모두에 영향을 줍니다.

***

## 6. Extension 함수 — OPENRNDR 스타일의 핵심

기존 클래스에 메서드를 붙입니다. OPENRNDR의 설계 철학 자체입니다.

```kotlin
// 직접 확장 함수 만들기
fun Drawer.grid(cols: Int, rows: Int, spacing: Double) {
    for (x in 0..cols) for (y in 0..rows) {
        point(x * spacing, y * spacing)
    }
}

// 사용
extend {
    drawer.grid(20, 20, 40.0)
}
```

OPENRNDR의 `Vector2 + Vector2`, `ColorRGBa.mix(...)` 같은 편의 문법이 전부 확장 함수로 만들어져 있습니다. **자주 쓰는 계산 로직은 Drawer/Vector2 확장 함수로 뽑는 게 OPENRNDR 표준 스타일**입니다.

```kotlin
// 벡터 연장
fun Vector2.rotated(angleRadians: Double): Vector2 {
    val cos = cos(angleRadians)
    val sin = sin(angleRadians)
    return Vector2(x * cos - y * sin, x * sin + y * cos)
}

// 색상 팔레트
fun ColorRGBa.Companion.golden() = ColorRGBa.fromHex(0xFFD700)
```

***

## 7. `when` — 코틀린의 switch (훨씬 강력함)

```kotlin
val color = when (mode) {
    0 -> ColorRGBa.PINK
    1, 2 -> ColorRGBa.CYAN
    in 3..5 -> ColorRGBa.YELLOW
    else -> ColorRGBa.WHITE
}

when { // 조건식 없이 브랜치별 boolean
    seconds < 10.0 -> phase1()
    else -> phase2()
}
```

`else ->`는 필수이거나 모든 경우를 커버해야 합니다.

***

## 8. Null 안전성 — `?`, `?.`, `?:`, `!!`

OPENRNDR 객체(예: 마우스 인터랙션 결과, 특정 패턴에서 null 가능한 값)를 다룰 때 자주 만납니다.

```kotlin
var lastMouse: Vector2? = null  // null 가능 타입

lastMouse?.let { prev ->          // null이 아니면 실행
    drawer.lineSegment(prev, mouse.position)
}

val radius = lastMouse?.x ?: 10.0 // null이면 기본값 (엘비스 연산자)

val x = lastMouse!!.x             // !!은 절대 null이 아님을 단언 — 크래시 위험, 피하기
```

***

## 9. 클래스와 데이터 클래스 — 상태 관리

```kotlin
// 데이터 클래스: 자동으로 toString, equals, copy 제공
data class Particle(
    var position: Vector2,
    var velocity: Vector2,
    val radius: Double = 3.0
)

// copy로 불변 갱신 (원본 유지) — 함수형 스타일
val moved = particle.copy(position = particle.position + particle.velocity)
```

```kotlin
// 일반 클래스: 메서드 포함 시에는
class Attractor(
    val pos: Vector2,
    var strength: Double = 1.0
) {
    fun force(p: Vector2): Vector2 {
        val d = pos - p
        return d.normalized * (strength / (d.length * d.length + 1e-6))
    }
}

val a = Attractor(Vector2(400.0, 400.0))
```

**포인트:**
* 생성자 파라미터에 `val/var`을 붙이면 자동 프로퍼티화
* `data class`는 수많은 점/입자 상태 관리에 필수
* `init { }` 블록으로 생성 시 초기화 가능

***

## 9. `object`와 싱글턴 — 리소스 관리

```kotlin
object ColorPalette {
    val bg = ColorRGBa.fromHex(0x0B0B0F)
    val accents = listOf(ColorRGBa.PINK, ColorRGBa.CYAN)
}

// 사용: ColorPalette.bg
extend {
    drawer.background(ColorPalette.bg)
    drawer.fill = ColorPalette.accents[0]
}
```

파라미터 세트, 색상 팔레트, RNG 씨드를 싱글턴으로 관리하면 하나의 파일로 여러 작품 변형을 만들기 좋습니다.

***

## 10. 타입 추론과 숫자 타입 주의

```kotlin
val w = width / 2.0     // Double
val n = 5               // Int
val x = width / 2       // Int 나눗셈! 401/2 = 200 (소수 버림)
```

* 코틀린은 `Int`와 `Double`을 자동 변환하지 않습니다. 그래서 OPENRNDR 예제에서 항상 `50.0`, `0.5`처럼 `.0`을 붙입니다.
* `"${width}x${height}"` — 문자열 템플릿은 로그 출력에 편리합니다.

***

## 12. scope 함수 — `apply`, `run`, `let`, `also`, `isolated`

파이프라인이 길어질 때 유용합니다.

```kotlin
val layer = renderTarget(width, height) {
    colorBuffer()
    depthBuffer()
}

val result = someValue?.let { compute(it) } ?: defaultValue

drawer.isolated {          // Drawer 상태(스타일)를 임시 변경 후 복원
    fill = ColorRGBa.PINK
    circle(x, y, r)
}
```

`drawer.isolated { }`는 OPENRNDR에서 상태 오염을 막는 핵심 패턴입니다.

***

## OPENRNDR에서의 전형적 코드 구조 종합

```kotlin
fun main() = application {
    configure { width = 800; height = 800 }

    program {
        // ---- 상태 선언 (val/var, data class) ----
        val cols = 20
        var t = 0.0

        // ---- 매 프레임 ----
        extend {
            drawer.clear(ColorRGBa.BLACK)

            val points = (0 until cols).map { i ->
                Vector2(i * 40.0, 200 + 100 * sin(t + i))
            }

            points.zipWithNext { a, b ->
                drawer.lineSegment(a, b)   // 람다 리시버로 drawer 생략 가능
            }

            t += 0.01
        }
    }
}
```

***

### 우선순위 요약

| 순위 | 문법                              | OPENRNDR에서의 역할     |
| :- | :------------------------------- | :------------------ |
| 1  | `extend { }` 람다 리시버 (섹션 1-2) | 드로잉 루프의 기본 형태   |
| 2  | 컬렉션 파이프라인 (섹션 5)            | 점/입자 리스트 생성·처리  |
| 3  | Extension 함수 (섹션 6)             | OPENRNDR 스타일의 핵심  |
| 4  | `val`/`var`, data class (섹션 3, 9) | 상태 관리 및 재현성     |
| 5  | 범위·반복, 조건분기 (섹션 4, 7)      | 루프 제어와 분기      |
| 6  | Null 안전, scope 함수 (섹션 8, 12)  | 안전성과 코드 정리    |

***
