# OPENRNDR 시작하기 (11): Scope 함수 — `apply`, `run`, `let`, `also`

지금까지 배운 문법들을 모두 활용하면, 코드를 더 깔끔하게 작성할 수 있습니다.

**Scope 함수**는 객체를 초기화하거나 체이닝할 때 매우 유용합니다.

### 문제: 객체 초기화가 복잡함

```kotlin
val settings = AppSettings()
settings.width = 800
settings.height = 600
settings.frameRate = 60
settings.debug = true
// 변수 이름을 매번 반복해야 함
```

### 해결: Scope 함수로 간결하게

```kotlin
val settings = AppSettings().apply {
    width = 800
    height = 600
    frameRate = 60
    debug = true
    // this가 AppSettings이므로, 프로퍼티에 직접 접근
}
```

이 글에서는:
- **네 가지 Scope 함수의 차이**
- **각각을 언제 사용할까**
- **OPENRNDR에서의 실제 패턴**

을 다루겠습니다.

---

## Scope 함수란?

**Scope 함수**는 **객체의 범위(scope) 안에서만 작동하는 함수들**입니다.

네 가지가 있습니다:
- `apply` — 객체를 수정하고 반환
- `also` — 객체를 그대로 반환하며 부수효과 실행
- `let` — 변환된 값 반환
- `run` — 변환된 값 반환 (암묵적 리시버)

---

## 1. `apply` — 객체 설정 후 반환

`apply`는 **객체를 설정하고 그 객체를 반환**합니다.

```kotlin
// 기본 형태
val obj = MyClass().apply {
    propertyA = value1
    propertyB = value2
}
// obj는 설정된 MyClass 인스턴스

// 내부 동작
val obj = MyClass()
val receiver = obj  // this == receiver
receiver.propertyA = value1
receiver.propertyB = value2
// 반환: obj
```

### 예제 1: 간단한 초기화

```kotlin
data class Circle(
    var x: Double = 0.0,
    var y: Double = 0.0,
    var radius: Double = 10.0,
    var color: ColorRGBa = ColorRGBa.WHITE
)

// apply 없이
val circle1 = Circle()
circle1.x = 100.0
circle1.y = 200.0
circle1.radius = 50.0
circle1.color = ColorRGBa.RED

// apply로 (깔끔함)
val circle2 = Circle().apply {
    x = 100.0
    y = 200.0
    radius = 50.0
    color = ColorRGBa.RED
}
```

### 예제 2: 리스트 초기화

```kotlin
val numbers = mutableListOf<Int>().apply {
    add(1)
    add(2)
    add(3)
    add(4)
    add(5)
}

// 또는 한 줄로
val numbers = mutableListOf(1, 2, 3, 4, 5)  // 더 간단함
```

### OPENRNDR에서의 apply

```kotlin
program {
    val drawer = this.drawer.apply {
        fill = ColorRGBa.WHITE
        stroke = ColorRGBa.BLACK
        strokeWeight = 2.0
    }
    
    extend {
        clear(ColorRGBa.BLACK)
        
        drawer.circle(400.0, 400.0, 100.0)
    }
}
```

---

## 2. `also` — 부수효과 실행 후 원본 반환

`also`는 **객체를 그대로 반환하면서 부수효과를 실행**합니다.

```kotlin
val result = createList()
    .also { list ->
        println("List created: $list")  // 부수효과
        // 하지만 list는 변경되지 않음
    }
    // result는 원본 list와 같음
```

### 예제 1: 디버그 출력과 함께

```kotlin
val numbers = (1..10).toList()
    .also { println("Original: $it") }
    .filter { it % 2 == 0 }
    .also { println("Filtered: $it") }
    .map { it * 2 }
    .also { println("Mapped: $it") }

// 출력:
// Original: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
// Filtered: [2, 4, 6, 8, 10]
// Mapped: [4, 8, 12, 16, 20]
```

### 예제 2: 로깅과 함께 데이터 처리

```kotlin
data class Particle(
    var position: Vector2,
    var velocity: Vector2
)

val particles = (0 until 100)
    .map { i ->
        Particle(
            position = Vector2(random(width.toDouble()), random(height.toDouble())),
            velocity = Vector2(random(-2.0, 2.0), random(-2.0, 2.0))
        )
    }
    .also { particles ->
        println("Created ${particles.size} particles")
    }
    .filter { it.position.x > 100 }
    .also { filtered ->
        println("Filtered to ${filtered.size} particles")
    }
```

### OPENRNDR에서의 also

```kotlin
program {
    val particles = mutableListOf<Particle>()
    
    extend {
        // 파티클 생성 및 로깅
        particles.add(
            Particle(mouse.position, Vector2(1.0, 0.0))
                .also { p ->
                    println("Particle created at ${p.position}")
                }
        )
    }
}
```

---

## 3. `let` — 변환 후 반환

`let`은 **객체를 변환해서 새로운 값을 반환**합니다.

```kotlin
val original = "Hello"
val transformed = original.let { str ->
    str.toUpperCase() + "!"
}
println(transformed)  // "HELLO!"

// original은 변경되지 않음
println(original)     // "Hello"
```

### 예제 1: Null 안전성과 함께

```kotlin
val name: String? = getUserName()

// let으로 null 체크
name?.let { n ->
    println("Hello, $n")
    // n은 String (null이 아님이 보장됨)
}
```

### 예제 2: 데이터 변환

```kotlin
val point = Vector2(100.0, 200.0)

val distance = point.let { p ->
    sqrt(p.x * p.x + p.y * p.y)
}

println(distance)  // 223.6...

// 또는 람다 축약
val distance2 = point.let { sqrt(it.x * it.x + it.y * it.y) }
```

### 예제 3: 체이닝

```kotlin
val result = mutableListOf(1, 2, 3)
    .let { list ->
        list.map { it * 2 }  // List<Int>로 변환
    }
    .let { doubled ->
        doubled.sum()  // Int로 변환
    }

println(result)  // 12
```

### OPENRNDR에서의 let

```kotlin
program {
    var selectedPoint: Vector2? = null
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // selectedPoint로 계산 수행
        selectedPoint?.let { point ->
            val distance = (point - mouse.position).length
            fill = if (distance < 100) ColorRGBa.RED else ColorRGBa.WHITE
            circle(point.x, point.y, 20.0)
        }
    }
}
```

---

## 4. `run` — 암묵적 리시버로 변환

`run`은 **리시버를 암묵적으로 사용하면서 변환된 값을 반환**합니다.

```kotlin
val result = "Hello".run {
    // this == "Hello"
    toUpperCase() + "!"
}
println(result)  // "HELLO!"

// let과 비슷하지만, 파라미터 대신 this 사용
// let: let { str -> str.toUpperCase() }
// run: run { toUpperCase() }
```

### 예제 1: 간단한 계산

```kotlin
val circle = Circle(100.0, 200.0, 50.0)

val area = circle.run {
    PI * radius * radius
}
println(area)  // 7853.98...
```

### 예제 2: 조건부 처리

```kotlin
val input: String? = getUserInput()

val result = input?.run {
    if (this.isEmpty()) "Empty" else "Not empty: $this"
} ?: "Input is null"

println(result)
```

### OPENRNDR에서의 run

```kotlin
program {
    data class Settings(var scale: Double = 1.0)
    
    val settings = Settings().run {
        scale = 2.0
        this  // Settings 객체 반환
    }
}
```

---

## 네 가지 Scope 함수 비교

| 함수 | 파라미터 | 리시버 | 반환값 | 용도 |
|------|---------|--------|--------|------|
| `apply` | 없음 | `this` | 객체 | 초기화 |
| `also` | `it` | 없음 | 객체 | 부수효과 |
| `let` | `it` | 없음 | 변환값 | 변환 |
| `run` | 없음 | `this` | 변환값 | 계산 |

**선택 기준:**

```kotlin
// apply: 객체 설정 후 사용
val obj = MyClass().apply {
    property = value
}

// also: 부수효과 (로깅, 검증)
val list = createList().also {
    println("Created: $it")
}

// let: null 체크, 변환
val value = data?.let { it.process() }

// run: 계산 또는 조건부 처리
val area = circle.run {
    PI * radius * radius
}
```

---

## 실전 예제 1: 게임 객체 초기화

```kotlin
data class GameCharacter(
    var name: String = "",
    var health: Int = 100,
    var mana: Int = 50,
    var position: Vector2 = Vector2(0.0, 0.0),
    var level: Int = 1
) {
    fun takeDamage(amount: Int) {
        health -= amount
    }
}

// apply로 초기화
val hero = GameCharacter().apply {
    name = "Hero"
    health = 200
    mana = 150
    position = Vector2(400.0, 400.0)
    level = 5
}

// also로 검증
val validHero = hero.also {
    require(it.health > 0) { "Health must be positive" }
    println("Hero initialized: ${it.name} at level ${it.level}")
}
```

---

## 실전 예제 2: 데이터 파이프라인

```kotlin
data class DataPoint(
    val x: Double,
    val y: Double,
    val value: Double
)

val results = (0 until 1000)
    .map { i ->
        DataPoint(
            x = i.toDouble(),
            y = random(100.0),
            value = sin(i.toDouble() / 100)
        )
    }
    .also { data ->
        println("Generated ${data.size} data points")
    }
    .filter { it.value > 0 }
    .also { data ->
        println("Filtered to ${data.size} positive values")
    }
    .sortedByDescending { it.value }
    .also { data ->
        println("Top value: ${data.firstOrNull()?.value}")
    }
    .take(10)
    .also { data ->
        println("Took top 10: ${data.size} items")
    }
```

---

## 실전 예제 3: Null 안전성과 체이닝

```kotlin
program {
    var cachedResult: List<Vector2>? = null
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 캐시된 결과 또는 새로 계산
        val points = cachedResult?.let { cached ->
            println("Using cached result")
            cached
        } ?: run {
            println("Computing new result")
            (0 until 20).map { i ->
                Vector2(i * 40.0, 300.0 + 100.0 * sin(i.toDouble()))
            }
        }.also { points ->
            cachedResult = points  // 캐시 저장
        }
        
        // 포인트 그리기
        fill = ColorRGBa.WHITE
        points.forEach { circle(it.x, it.y, 5.0) }
    }
}
```

---

## 실전 예제 4: Builder 패턴

```kotlin
data class DrawingContext(
    var strokeColor: ColorRGBa = ColorRGBa.BLACK,
    var fillColor: ColorRGBa = ColorRGBa.WHITE,
    var strokeWeight: Double = 1.0,
    var fontSize: Int = 12
)

fun createContext(init: DrawingContext.() -> Unit): DrawingContext {
    return DrawingContext().apply(init)
}

program {
    val darkContext = createContext {
        strokeColor = ColorRGBa.WHITE
        fillColor = ColorRGBa.BLACK
        strokeWeight = 2.0
    }
    
    val lightContext = createContext {
        strokeColor = ColorRGBa.BLACK
        fillColor = ColorRGBa.WHITE
        strokeWeight = 1.0
    }
    
    extend {
        clear(ColorRGBa.BLACK)
        
        fill = darkContext.fillColor
        stroke = darkContext.strokeColor
        strokeWeight = darkContext.strokeWeight
        
        circle(200.0, 400.0, 50.0)
    }
}
```

---

## 실전 예제 5: 복잡한 설정 체이닝

```kotlin
program {
    val particles = mutableListOf<Particle>()
    
    val config = object {
        var particleCount = 100
        var minVelocity = -2.0
        var maxVelocity = 2.0
        var particleSize = 5.0
    }.apply {
        particleCount = 150  // 설정 조정
        particleSize = 8.0
    }
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 파티클 추가
        repeat(3) {
            particles.add(
                Particle(
                    position = mouse.position,
                    velocity = Vector2(
                        random(config.minVelocity, config.maxVelocity),
                        random(config.minVelocity, config.maxVelocity)
                    ),
                    radius = config.particleSize
                ).also { p ->
                    println("Particle added with radius: ${p.radius}")
                }
            )
        }
        
        // 파티클 업데이트 및 렌더링
        particles
            .also { list ->
                println("Updating ${list.size} particles")
            }
            .forEach { p ->
                p.update()
                p.draw(drawer)
            }
        
        particles.removeIf { !it.isAlive }
    }
}
```

---

## Scope 함수의 체이닝

여러 Scope 함수를 연결할 수 있습니다.

```kotlin
val result = mutableListOf(1, 2, 3)
    .apply {
        add(4)
        add(5)
    }
    .also { list ->
        println("List: $list")
    }
    .map { it * 2 }
    .let { doubled ->
        doubled.sum()
    }

println(result)  // 30
```

---

## 실전 예제 6: 게임 상태 관리

```kotlin
object GameManager {
    var score = 0
    var level = 1
    var enemies = mutableListOf<Enemy>()
    
    fun updateScore(points: Int) {
        (this.score + points).let { newScore ->
            score = newScore
            if (newScore > 1000) {
                level++
            }
        }.also {
            println("Score updated to $score")
        }
    }
    
    fun createEnemies(count: Int) {
        enemies = (0 until count)
            .map { i ->
                Enemy(
                    position = Vector2(random(800.0), random(600.0)),
                    health = 10 * level
                )
            }
            .toMutableList()
            .also { list ->
                println("Created ${list.size} enemies for level $level")
            }
    }
}

program {
    extend {
        clear(ColorRGBa.BLACK)
        
        // 게임 관리자 상태 표시
        fill = ColorRGBa.WHITE
        text("Score: ${GameManager.score}", 20.0, 30.0)
        text("Level: ${GameManager.level}", 20.0, 60.0)
        text("Enemies: ${GameManager.enemies.size}", 20.0, 90.0)
    }
}
```

---

## Scope 함수의 장점

| 장점 | 설명 |
|------|------|
| **간결성** | 반복되는 변수 이름 제거 |
| **가독성** | 의도가 명확함 |
| **체이닝** | 메서드 체이닝으로 유연함 |
| **유효범위** | 별도의 변수 오염 없음 |
| **표현력** | 함수형 스타일로 우아함 |

---

## 실전 팁: 언제 어떤 함수를 쓸까?

```kotlin
// 초기화: apply
val obj = MyClass().apply {
    property = value
}

// 검증/로깅: also
val list = createList().also {
    require(it.isNotEmpty())
    println("List created")
}

// 변환: let
val transformed = data?.let { it.process() }

// 계산: run
val area = circle.run {
    PI * radius * radius
}

// 복잡한 초기화: apply + 체이닝
val config = Settings().apply {
    debug = true
    maxConnections = 100
}.also { s ->
    println("Config: $s")
}
```

---

## 다음 단계

지금까지 배운 코틀린의 핵심 문법들:
1. 프로그램 구조
2. 람다와 리시버
3. val/var
4. 범위와 반복
5. 컬렉션
6. Extension 함수
7. when 조건 분기
8. Null 안전성
9. 클래스와 데이터 클래스
10. Object와 싱글턴
11. Scope 함수 ✅

**이제 OPENRNDR에서 제너러티브 아트를 만들기 위한 문법 기초가 완성되었습니다!**

다음 단계:
- 더 복잡한 프로젝트 빌드
- OPENRNDR의 고급 기능 탐색
- 생각하는 아트워크를 실제로 코딩하기

---

## 연습 문제

### 1. 기본 apply

```kotlin
data class Shape(
    var x: Double = 0.0,
    var y: Double = 0.0,
    var size: Double = 10.0
)

val shape = Shape().apply {
    x = 100.0
    y = 200.0
    size = 50.0
}

println(shape)
```

### 2. also로 디버깅

```kotlin
val numbers = (1..10).toList()
    .also { println("Original: $it") }
    .filter { it % 2 == 0 }
    .also { println("Filtered: $it") }

```

### 3. let으로 null 처리

```kotlin
var selected: Vector2? = null

selected?.let { pos ->
    println("Selected position: $pos")
    circle(pos.x, pos.y, 20.0)
}
```

### 4. run으로 계산

```kotlin
data class Circle(val radius: Double)

val circle = Circle(50.0)

val area = circle.run {
    PI * radius * radius
}

println(area)
```

### 5. OPENRNDR 종합 예제

```kotlin
program {
    data class Config(
        var particleCount: Int = 100,
        var particleSize: Double = 5.0
    )
    
    val config = Config().apply {
        particleCount = 200
        particleSize = 8.0
    }
    
    val particles = mutableListOf<Particle>()
    
    extend {
        clear(ColorRGBa.BLACK)
        
        repeat(5) {
            particles.add(
                Particle(mouse.position, config.particleSize)
                    .also { p ->
                        println("Particle created")
                    }
            )
        }
        
        particles
            .also { println("Drawing ${it.size} particles") }
            .forEach { it.draw(drawer) }
        
        particles.removeIf { !it.isAlive }
    }
}
```

---

## 마지막 조언

코틀린의 Scope 함수들은 처음엔 복잡해 보일 수 있습니다. 하지만 자주 사용하면 자연스러워집니다.

**핵심:**
- 초기화 → `apply`
- 부수효과 → `also`
- 변환 → `let`
- 계산 → `run`

이 네 가지를 기억하면 됩니다.

이제 당신은 OPENRNDR에서 제너러티브 아트를 만들기 위한 코틀린 기초를 모두 배웠습니다. 

**다음은 당신의 창의성이 코드가 되는 차례입니다. 행운을 빕니다! 🎨**

---

**이전 글**: [OPENRNDR 시작하기 (10): Object와 싱글턴 — 전역 상태 관리](./openrndr_10_object_singleton.md)  
**다음 글**: OPENRNDR 시작하기 (12): 종합 코드 구조 (추후 작성)
