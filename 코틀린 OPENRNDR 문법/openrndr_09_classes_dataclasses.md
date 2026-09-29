# OPENRNDR 시작하기 (9): 클래스와 데이터 클래스 — 상태 관리

지금까지 배운 것들은 모두 **값과 함수**를 다루는 방법입니다.

하지만 제너러티브 아트에서는 **여러 데이터를 하나로 묶어서 관리해야 합니다.**

예를 들어:

```kotlin
// 나쁜 방법: 데이터를 흩어놓음
var x = 100.0
var y = 200.0
var vx = 1.5
var vy = -0.5
var radius = 10.0
var age = 0.0
var lifespan = 100.0

// 그러면 이 모든 변수들을 함수에 전달해야 함
fun updateParticle(x: Double, y: Double, vx: Double, vy: Double, ...) {
    // 복잡함!
}
```

**이것을 해결하는 방법이 클래스입니다:**

```kotlin
// 좋은 방법: 데이터를 한곳에
class Particle(
    var x: Double,
    var y: Double,
    var vx: Double,
    var vy: Double,
    var radius: Double = 10.0,
    var age: Double = 0.0,
    val lifespan: Double = 100.0
)

// 사용
val particle = Particle(100.0, 200.0, 1.5, -0.5)
particle.update()
```

이 글에서는:
- **클래스가 무엇이고 왜 필요한가**
- **데이터 클래스의 강력함**
- **OPENRNDR에서의 실제 패턴**

을 다루겠습니다.

---

## 클래스의 기본

### 클래스란?

**클래스**는 관련된 데이터와 함수를 하나의 단위로 묶는 것입니다.

```kotlin
class Circle(
    var x: Double,
    var y: Double,
    var radius: Double
) {
    // 생성자 파라미터가 자동으로 프로퍼티가 됨
    
    // 메서드
    fun draw(drawer: Drawer) {
        drawer.circle(x, y, radius)
    }
    
    fun contains(px: Double, py: Double): Boolean {
        val dx = x - px
        val dy = y - py
        return dx * dx + dy * dy < radius * radius
    }
}

// 사용
val myCircle = Circle(100.0, 200.0, 50.0)
myCircle.draw(drawer)
```

### 생성자와 프로퍼티

```kotlin
// 생성자 파라미터에 val/var을 붙이면 프로퍼티가 됨
class Point(
    val x: Double,  // val = 읽기만 가능 (불변)
    var y: Double   // var = 읽고 쓰기 가능 (가변)
)

val p = Point(10.0, 20.0)
println(p.x)  // ✅ OK
p.x = 15.0    // ❌ 에러! val이므로 재할당 불가

p.y = 25.0    // ✅ OK
```

---

## 데이터 클래스 (Data Class)

**데이터 클래스**는 "데이터만 담는" 클래스입니다. 코틀린이 자동으로 유용한 메서드를 만들어줍니다.

```kotlin
data class Particle(
    var position: Vector2,
    var velocity: Vector2,
    val radius: Double = 10.0,
    val lifespan: Double = 100.0
)
```

### 자동으로 생성되는 메서드

#### 1. `toString()` — 문자열 표현

```kotlin
val p = Particle(Vector2(100.0, 200.0), Vector2(1.0, 0.5))

println(p)  // Particle(position=Vector2(100.0, 200.0), velocity=Vector2(1.0, 0.5), ...)
```

#### 2. `equals()` — 값이 같은지 비교

```kotlin
val p1 = Particle(Vector2(100.0, 200.0), Vector2(1.0, 0.5))
val p2 = Particle(Vector2(100.0, 200.0), Vector2(1.0, 0.5))
val p3 = Particle(Vector2(150.0, 200.0), Vector2(1.0, 0.5))

println(p1 == p2)  // true (값이 같음)
println(p1 == p3)  // false (값이 다름)
```

**일반 클래스는 참조만 비교합니다:**

```kotlin
class RegularParticle(val x: Double, val y: Double)

val r1 = RegularParticle(100.0, 200.0)
val r2 = RegularParticle(100.0, 200.0)

println(r1 == r2)  // false! (다른 객체이므로)
```

#### 3. `copy()` — 일부만 바꾼 복사본

```kotlin
val original = Particle(
    position = Vector2(100.0, 200.0),
    velocity = Vector2(1.0, 0.5),
    radius = 10.0
)

// 위치만 바꾼 복사본
val moved = original.copy(position = Vector2(150.0, 250.0))

// 원본은 변하지 않음
println(original.position)  // Vector2(100.0, 200.0)
println(moved.position)     // Vector2(150.0, 250.0)
```

**함수형 프로그래밍에서 매우 중요합니다:**

```kotlin
// 기존 입자들을 업데이트하지만, 원본 보존
val updatedParticles = particles.map { p ->
    p.copy(
        position = p.position + p.velocity,
        age = p.age + 1.0
    )
}
```

---

## p5.js와의 비교

### p5.js: 객체 리터럴

```javascript
let particle = {
    x: 100,
    y: 200,
    vx: 1.5,
    vy: -0.5,
    radius: 10,
    age: 0,
    
    update: function() {
        this.x += this.vx;
        this.y += this.vy;
        this.age++;
    },
    
    draw: function(ctx) {
        ctx.circle(this.x, this.y, this.radius);
    }
};

// 사용
particle.update();
particle.draw(ctx);
```

### OPENRNDR: 클래스와 데이터 클래스

```kotlin
data class Particle(
    var position: Vector2,
    var velocity: Vector2,
    val radius: Double = 10.0,
    var age: Double = 0.0,
    val lifespan: Double = 100.0
) {
    fun update() {
        position += velocity
        age++
    }
    
    fun draw(drawer: Drawer) {
        drawer.circle(position.x, position.y, radius)
    }
}

// 사용
particle.update()
particle.draw(drawer)
```

**OPENRNDR의 장점:**
- 타입 안전성
- 자동 `toString()`, `equals()`, `copy()`
- 컴파일 타임 검사

---

## 실전 예제 1: 입자 시스템

```kotlin
data class Particle(
    var position: Vector2,
    var velocity: Vector2,
    val radius: Double,
    var age: Double = 0.0,
    val lifespan: Double
) {
    val isAlive: Boolean
        get() = age < lifespan
    
    fun update() {
        position += velocity
        velocity *= 0.99  // 마찰
        age++
    }
    
    fun draw(drawer: Drawer) {
        val alpha = 1.0 - (age / lifespan)
        drawer.fill = ColorRGBa(1.0, 1.0, 1.0, alpha)
        drawer.circle(position.x, position.y, radius)
    }
}

program {
    val particles = mutableListOf<Particle>()
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 마우스 위치에서 새 입자 생성
        repeat(3) {
            particles.add(Particle(
                position = mouse.position,
                velocity = Vector2(random(-2.0, 2.0), random(-2.0, 2.0)),
                radius = random(3.0, 8.0),
                lifespan = random(30.0, 60.0)
            ))
        }
        
        // 입자 업데이트 및 그리기
        particles.forEach { p ->
            p.update()
            p.draw(drawer)
        }
        
        // 죽은 입자 제거
        particles.removeIf { !it.isAlive }
    }
}
```

### 개선: 계산된 프로퍼티 (Computed Property)

```kotlin
data class Particle(
    var position: Vector2,
    var velocity: Vector2,
    val radius: Double,
    var age: Double = 0.0,
    val lifespan: Double
) {
    // 읽기 전용 프로퍼티
    val isAlive: Boolean
        get() = age < lifespan
    
    val alpha: Double
        get() = 1.0 - (age / lifespan)
    
    val speed: Double
        get() = sqrt(velocity.x * velocity.x + velocity.y * velocity.y)
}
```

---

## 실전 예제 2: 선택 가능한 도형

```kotlin
data class SelectableShape(
    val position: Vector2,
    val radius: Double,
    var isSelected: Boolean = false
) {
    fun contains(point: Vector2): Boolean {
        val dx = position.x - point.x
        val dy = position.y - point.y
        return dx * dx + dy * dy < radius * radius
    }
    
    fun draw(drawer: Drawer) {
        drawer.fill = if (isSelected) {
            ColorRGBa.RED
        } else {
            ColorRGBa.WHITE
        }
        drawer.circle(position.x, position.y, radius)
    }
    
    fun toggle() {
        isSelected = !isSelected
    }
}

program {
    val shapes = listOf(
        SelectableShape(Vector2(200.0, 200.0), 30.0),
        SelectableShape(Vector2(600.0, 200.0), 30.0),
        SelectableShape(Vector2(400.0, 600.0), 30.0)
    )
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 마우스 클릭으로 선택 토글
        if (mouse.buttonDown) {
            for (shape in shapes) {
                if (shape.contains(mouse.position)) {
                    shape.toggle()
                }
            }
        }
        
        // 모든 도형 그리기
        shapes.forEach { it.draw(drawer) }
    }
}
```

---

## 실전 예제 3: 움직이는 에이전트

```kotlin
data class Agent(
    var position: Vector2,
    var target: Vector2? = null,
    val speed: Double = 2.0,
    val radius: Double = 10.0
) {
    fun moveTo(newTarget: Vector2) {
        target = newTarget
    }
    
    fun update() {
        target?.let { t ->
            val direction = (t - position).normalized
            position += direction * speed
            
            // 목표 근처에 도착하면 멈춤
            if ((position - t).length < 10.0) {
                target = null
            }
        }
    }
    
    fun draw(drawer: Drawer) {
        drawer.fill = if (target != null) ColorRGBa.CYAN else ColorRGBa.WHITE
        drawer.circle(position.x, position.y, radius)
        
        target?.let { t ->
            drawer.stroke = ColorRGBa.GRAY
            drawer.lineSegment(position, t)
        }
    }
}

program {
    val agent = Agent(Vector2(400.0, 400.0))
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 마우스 클릭으로 이동 명령
        if (mouse.buttonDown) {
            agent.moveTo(mouse.position)
        }
        
        agent.update()
        agent.draw(drawer)
    }
}
```

---

## 실전 예제 4: 중첩된 데이터 구조

```kotlin
data class Vector(
    var x: Double,
    var y: Double
) {
    operator fun plus(other: Vector): Vector {
        return Vector(x + other.x, y + other.y)
    }
}

data class Body(
    val position: Vector,
    var velocity: Vector,
    val mass: Double,
    val radius: Double
) {
    fun applyForce(force: Vector) {
        val acceleration = Vector(force.x / mass, force.y / mass)
        velocity = velocity + acceleration
    }
    
    fun update() {
        position.x += velocity.x
        position.y += velocity.y
    }
    
    fun draw(drawer: Drawer) {
        drawer.circle(position.x, position.y, radius)
    }
}

data class PhysicsSystem(
    val bodies: MutableList<Body> = mutableListOf(),
    var gravity: Vector = Vector(0.0, 0.1)
) {
    fun update() {
        bodies.forEach { body ->
            val gravitationalForce = Vector(0.0, body.mass * gravity.y)
            body.applyForce(gravitationalForce)
            body.update()
        }
    }
    
    fun draw(drawer: Drawer) {
        bodies.forEach { it.draw(drawer) }
    }
}

program {
    val physics = PhysicsSystem()
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 마우스 클릭으로 물체 추가
        if (mouse.buttonDown) {
            physics.bodies.add(Body(
                position = Vector(mouse.position.x, mouse.position.y),
                velocity = Vector(random(-1.0, 1.0), random(-1.0, 1.0)),
                mass = random(1.0, 5.0),
                radius = random(5.0, 15.0)
            ))
        }
        
        physics.update()
        physics.draw(drawer)
    }
}
```

---

## 기본값 (Default Parameters)

데이터 클래스에서 기본값을 설정하면 생성 시 생략할 수 있습니다.

```kotlin
data class Particle(
    var position: Vector2,
    var velocity: Vector2,
    val radius: Double = 10.0,      // 기본값
    var age: Double = 0.0,           // 기본값
    val lifespan: Double = 100.0     // 기본값
)

// 사용
val p1 = Particle(Vector2(0.0, 0.0), Vector2(1.0, 0.0))
// radius, age, lifespan은 기본값 사용

val p2 = Particle(
    Vector2(0.0, 0.0),
    Vector2(1.0, 0.0),
    radius = 20.0
)
// age, lifespan은 기본값, radius만 명시
```

---

## 이름 있는 인자 (Named Arguments)

가독성을 위해 인자에 이름을 붙일 수 있습니다.

```kotlin
// 위치로만 전달 (읽기 어려움)
val p = Particle(Vector2(100.0, 200.0), Vector2(1.0, 0.5), 15.0, 0.0, 100.0)

// 이름과 함께 (읽기 쉬움)
val p = Particle(
    position = Vector2(100.0, 200.0),
    velocity = Vector2(1.0, 0.5),
    radius = 15.0,
    age = 0.0,
    lifespan = 100.0
)

// 기본값을 사용하며 일부만 명시
val p = Particle(
    position = Vector2(100.0, 200.0),
    velocity = Vector2(1.0, 0.5),
    radius = 15.0
)
```

---

## 메서드 vs 확장 함수

### 메서드 (클래스 내부)

```kotlin
data class Circle(
    val x: Double,
    val y: Double,
    val radius: Double
) {
    fun area(): Double {
        return PI * radius * radius
    }
    
    fun draw(drawer: Drawer) {
        drawer.circle(x, y, radius)
    }
}
```

### 확장 함수 (클래스 외부)

```kotlin
fun Circle.circumference(): Double {
    return 2 * PI * radius
}

// 사용
val c = Circle(100.0, 100.0, 50.0)
println(c.area())           // 메서드
println(c.circumference())  // 확장 함수
```

**선택 기준:**
- **메서드**: 클래스의 핵심 동작
- **확장 함수**: 선택적인 기능, 라이브러리 함수

---

## 계산된 프로퍼티 (Computed Properties)

데이터로 계산되는 프로퍼티는 `val`로 정의할 수 있습니다.

```kotlin
data class Particle(
    var position: Vector2,
    var velocity: Vector2,
    var age: Double = 0.0,
    val lifespan: Double = 100.0
) {
    // 계산된 프로퍼티: 매번 계산됨
    val isAlive: Boolean
        get() = age < lifespan
    
    val alpha: Double
        get() = 1.0 - (age / lifespan)
    
    val speed: Double
        get() = sqrt(velocity.x * velocity.x + velocity.y * velocity.y)
}

// 사용
val p = Particle(Vector2(0.0, 0.0), Vector2(1.0, 0.0), 50.0)
println(p.isAlive)   // true
println(p.alpha)     // 0.5
```

---

## 데이터 클래스 vs 일반 클래스

| 기능 | 데이터 클래스 | 일반 클래스 |
|------|-------------|----------|
| `toString()` | 자동 생성 | 수동 정의 |
| `equals()` | 자동 생성 (값 비교) | 수동 정의 (참조 비교) |
| `copy()` | 자동 생성 | 없음 |
| 메서드 | 가능 | 가능 |
| 용도 | 데이터 담기 | 복잡한 동작 |

---

## 실전 예제 5: 격자 기반 게임

```kotlin
data class Cell(
    val x: Int,
    val y: Int,
    var state: Boolean = false  // false = 죽음, true = 살아있음
) {
    fun draw(drawer: Drawer, size: Double) {
        if (state) {
            drawer.fill = ColorRGBa.WHITE
        } else {
            drawer.fill = ColorRGBa.GRAY
        }
        drawer.rectangle(x * size, y * size, size, size)
    }
}

data class Grid(
    val cols: Int,
    val rows: Int,
    val cells: MutableList<Cell> = MutableList(cols * rows) { i ->
        Cell(i % cols, i / cols)
    }
) {
    fun get(x: Int, y: Int): Cell? {
        if (x < 0 || x >= cols || y < 0 || y >= rows) return null
        return cells[x + y * cols]
    }
    
    fun toggle(x: Int, y: Int) {
        get(x, y)?.state = !(get(x, y)?.state ?: false)
    }
    
    fun draw(drawer: Drawer, cellSize: Double) {
        cells.forEach { it.draw(drawer, cellSize) }
    }
}

program {
    val grid = Grid(10, 10)
    val cellSize = 80.0
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 클릭으로 셀 상태 토글
        if (mouse.buttonDown) {
            val col = (mouse.position.x / cellSize).toInt()
            val row = (mouse.position.y / cellSize).toInt()
            grid.toggle(col, row)
        }
        
        grid.draw(drawer, cellSize)
    }
}
```

---

## 데이터 클래스의 장점 요약

| 장점 | 설명 |
|------|------|
| **간결성** | 기본 메서드 자동 생성 |
| **안전성** | 타입 체크, null 안전성 |
| **비교** | 값 기반 `equals()` |
| **복사** | `copy()`로 불변성 지원 |
| **가독성** | 명확한 데이터 구조 |

---

## 다음 단계: Object와 싱글턴

다음 글에서는 **Object와 싱글턴**을 다룹니다.

때로는 **오직 하나의 인스턴스만 필요한 경우**가 있습니다:

```kotlin
// 색상 팔레트는 프로젝트 전체에서 하나만 필요
object ColorPalette {
    val primary = ColorRGBa.RED
    val secondary = ColorRGBa.BLUE
    val accent = ColorRGBa.YELLOW
}

// 사용
extend {
    fill = ColorPalette.primary
}
```

싱글턴 패턴은 전역 상태를 관리하는 깔끔한 방법입니다.

---

## 연습 문제

### 1. 기본 데이터 클래스

```kotlin
data class Rectangle(
    val x: Double,
    val y: Double,
    val width: Double,
    val height: Double
)

val rect = Rectangle(100.0, 200.0, 150.0, 100.0)
println(rect)  // Rectangle(x=100.0, y=200.0, ...)
```

### 2. 메서드 추가

```kotlin
data class Circle(
    val x: Double,
    val y: Double,
    val radius: Double
) {
    fun area(): Double = PI * radius * radius
    
    fun draw(drawer: Drawer) {
        drawer.circle(x, y, radius)
    }
}
```

### 3. 계산된 프로퍼티

```kotlin
data class Particle(
    var position: Vector2,
    var velocity: Vector2,
    var age: Double = 0.0,
    val lifespan: Double = 100.0
) {
    val isAlive: Boolean
        get() = age < lifespan
    
    val alpha: Double
        get() = 1.0 - (age / lifespan)
}
```

### 4. OPENRNDR: 간단한 입자 시스템

```kotlin
data class Particle(
    var position: Vector2,
    var velocity: Vector2,
    val radius: Double = 5.0,
    var age: Double = 0.0,
    val lifespan: Double = 60.0
) {
    val isAlive: Boolean
        get() = age < lifespan
    
    fun update() {
        position += velocity
        velocity *= 0.99
        age++
    }
    
    fun draw(drawer: Drawer) {
        val alpha = 1.0 - (age / lifespan)
        drawer.fill = ColorRGBa(1.0, 1.0, 1.0, alpha)
        drawer.circle(position.x, position.y, radius)
    }
}

program {
    val particles = mutableListOf<Particle>()
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 마우스 위치에서 입자 생성
        repeat(5) {
            particles.add(Particle(
                position = mouse.position,
                velocity = Vector2(random(-2.0, 2.0), random(-3.0, 0.0))
            ))
        }
        
        particles.forEach { p ->
            p.update()
            p.draw(drawer)
        }
        
        particles.removeIf { !it.isAlive }
    }
}
```

### 5. 심화: 상호작용하는 객체

지난 "선택 가능한 도형" 예제를 확장해서:
- 도형을 드래그할 수 있게 하기
- 색상 변경 기능 추가
- 여러 도형 동시 관리

---

**이전 글**: [OPENRNDR 시작하기 (8): Null 안전성 — `?`, `?.`, `?:`, `!!`](./openrndr_08_null_safety.md)  
**다음 글**: OPENRNDR 시작하기 (10): Object와 싱글턴 — 전역 상태 관리
