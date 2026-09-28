# OPENRNDR 시작하기 (5): 컬렉션과 함수형 파이프라인

지금까지 배운 `for` 반복문은 명령형(imperative) 스타일입니다. 코드가 "어떻게"를 설명합니다.

```kotlin
// "1단계: 원 100개를 만든다"
// "2단계: 살아있는 것만 필터링한다"
// "3단계: 각각을 그린다"

val circles = mutableListOf<Circle>()
for (i in 0 until 100) {
    circles.add(Circle(random(width), random(height)))
}

val alive = mutableListOf<Circle>()
for (circle in circles) {
    if (circle.isAlive) {
        alive.add(circle)
    }
}

for (circle in alive) {
    drawer.circle(circle.x, circle.y, circle.size)
}
```

**하지만 선언형(declarative) 스타일이 있습니다.** 코드가 "무엇을" 설명합니다.

```kotlin
// "원 100개를 만들고, 살아있는 것만 그린다"
(0 until 100)
    .map { Circle(random(width), random(height)) }
    .filter { it.isAlive }
    .forEach { drawer.circle(it.x, it.y, it.size) }
```

**이것이 함수형 파이프라인입니다.**

이 글에서는:
- **컬렉션의 기초** (`List`, `Set`, `Map`)
- **함수형 메서드** (`map`, `filter`, `forEach`, `reduce` 등)
- **OPENRNDR에서 실제로 쓰이는 패턴들**

을 다루겠습니다.

---

## 컬렉션이란?

**컬렉션**은 여러 값을 모아놓은 그룹입니다.

### 기본 컬렉션 타입

```kotlin
// List: 순서가 있고, 중복 가능
val list = listOf(1, 2, 3, 3, 4)

// Set: 순서가 없고, 중복 불가
val set = setOf(1, 2, 3, 4)

// Map: 키-값 쌍
val map = mapOf("red" to ColorRGBa.RED, "blue" to ColorRGBa.BLUE)
```

**OPENRNDR에서는 주로 `List`를 씁니다.**

### 불변(Immutable) vs 가변(Mutable)

```kotlin
val immutable = listOf(1, 2, 3)
immutable[0] = 10  // ❌ 에러! 리스트 내용 변경 불가

val mutable = mutableListOf(1, 2, 3)
mutable[0] = 10    // ✅ OK
mutable.add(4)     // ✅ OK
```

**OPENRNDR에서의 선택:**

```kotlin
// 초기화 후 변하지 않으면 val + 불변 리스트
val positions = listOf(Vector2(0.0, 0.0), Vector2(100.0, 100.0))

// 계속 추가/제거하면 val + 가변 리스트
val particles = mutableListOf<Particle>()
```

---

## p5.js와의 비교

### p5.js: 배열 다루기

```javascript
let numbers = [1, 2, 3, 4, 5];

// 각 수를 2배로
let doubled = [];
for (let i = 0; i < numbers.length; i++) {
    doubled.push(numbers[i] * 2);
}
console.log(doubled);  // [2, 4, 6, 8, 10]

// 3보다 큰 수만 필터링
let filtered = [];
for (let i = 0; i < doubled.length; i++) {
    if (doubled[i] > 3) {
        filtered.push(doubled[i]);
    }
}
console.log(filtered);  // [4, 6, 8, 10]
```

### 코틀린: 함수형 파이프라인

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)

val result = numbers
    .map { it * 2 }           // 각 수를 2배로
    .filter { it > 3 }        // 3보다 큰 수만
    .forEach { println(it) }  // [4, 6, 8, 10] 출력
```

**훨씬 간결하고, 의도가 명확합니다.**

---

## 핵심 함수형 메서드

### 1. `map` — 변환

각 요소를 변환해서 새로운 리스트를 만듭니다.

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val doubled = numbers.map { it * 2 }
println(doubled)  // [2, 4, 6, 8, 10]

// 더 복잡한 변환
val positions = listOf(1, 2, 3)
val vectors = positions.map { Vector2(it * 100.0, it * 100.0) }
// [Vector2(100, 100), Vector2(200, 200), Vector2(300, 300)]
```

**OPENRNDR에서:**

```kotlin
extend {
    clear(ColorRGBa.BLACK)
    
    // 인덱스를 원의 위치로 변환
    val circles = (0 until 10).map { i ->
        Vector2(i * 80.0, 400.0)
    }
    
    circles.forEach { pos ->
        circle(pos.x, pos.y, 20.0)
    }
}
```

### 2. `filter` — 선택

조건을 만족하는 요소만 남깁니다.

```kotlin
val numbers = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
val even = numbers.filter { it % 2 == 0 }
println(even)  // [2, 4, 6, 8, 10]

// 더 복잡한 조건
val particles = listOf(...)
val alive = particles.filter { it.age < it.lifespan }
```

**OPENRNDR 입자 시스템:**

```kotlin
program {
    val particles = mutableListOf<Particle>()
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 마우스 클릭 시 입자 생성
        if (mouse.buttonDown) {
            repeat(10) {
                particles.add(Particle(mouse.position))
            }
        }
        
        // 살아있는 입자만 그리기
        particles
            .filter { it.isAlive }
            .forEach { p ->
                p.update()
                circle(p.pos.x, p.pos.y, p.size)
            }
        
        // 죽은 입자 제거
        particles.removeIf { !it.isAlive }
    }
}
```

### 3. `forEach` — 반복

각 요소에 어떤 작업을 합니다 (부수효과가 있는 경우).

```kotlin
val items = listOf("apple", "banana", "cherry")

// map 대신 forEach (값을 만들지 않고, 부수효과만)
items.forEach { println(it) }
// apple
// banana
// cherry

// OPENRNDR: 모든 원을 그리기
val positions = listOf(Vector2(100.0, 100.0), Vector2(200.0, 200.0))
positions.forEach { circle(it.x, it.y, 50.0) }
```

### 4. `map` + `filter` + `forEach` — 파이프라인

**이것이 함수형 프로그래밍의 핵심입니다.**

```kotlin
val data = (0 until 100)
    .map { i -> Particle(random(width), random(height)) }     // 생성
    .filter { it.x > 200.0 }                                  // 필터링
    .forEach { circle(it.x, it.y, 5.0) }                      // 그리기
```

**단계별 분석:**
1. `(0 until 100)` — 0부터 99까지의 범위
2. `.map { ... }` — 각 숫자를 Particle로 변환
3. `.filter { ... }` — x 좌표가 200 이상인 것만
4. `.forEach { ... }` — 각각을 그리기

---

## 고급 함수형 메서드

### 5. `reduce` — 누적

모든 요소를 하나로 합칩니다.

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)

// 모두 더하기
val sum = numbers.reduce { acc, value ->
    acc + value
}
println(sum)  // 15

// 문자 연결
val words = listOf("Hello", " ", "World")
val sentence = words.reduce { acc, word -> acc + word }
println(sentence)  // "Hello World"
```

**OPENRNDR에서의 실제 사용:**

```kotlin
extend {
    val positions = listOf(
        Vector2(100.0, 100.0),
        Vector2(200.0, 200.0),
        Vector2(300.0, 150.0)
    )
    
    // 모든 위치의 평균 구하기
    val average = positions.reduce { acc, pos ->
        acc + pos / positions.size.toDouble()
    }
    
    // 평균을 중심으로 표시
    circle(average.x, average.y, 30.0)
}
```

### 6. `fold` — 누적 (초기값 포함)

`reduce`와 비슷하지만, **초기값**을 지정할 수 있습니다.

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)

// reduce: 첫 번째 요소부터 시작
val sum1 = numbers.reduce { acc, value -> acc + value }
println(sum1)  // 15

// fold: 초기값 0부터 시작
val sum2 = numbers.fold(0) { acc, value -> acc + value }
println(sum2)  // 15

// 초기값이 유용한 경우
val sum3 = numbers.fold(100) { acc, value -> acc + value }
println(sum3)  // 115 (100 + 1 + 2 + 3 + 4 + 5)
```

### 7. `groupBy` — 분류

특정 기준에 따라 그룹으로 나눕니다.

```kotlin
val particles = listOf(
    Particle(x = 100.0, alive = true),
    Particle(x = 200.0, alive = false),
    Particle(x = 300.0, alive = true)
)

val grouped = particles.groupBy { it.alive }
// {
//   true: [Particle(100.0), Particle(300.0)],
//   false: [Particle(200.0)]
// }

// 살아있는 입자만 그리기
grouped[true]?.forEach { circle(it.x, it.y, 5.0) }
```

### 8. `zip` — 쌍 만들기

두 리스트의 요소를 쌍으로 묶습니다.

```kotlin
val xs = listOf(100.0, 200.0, 300.0)
val ys = listOf(150.0, 250.0, 350.0)

val points = xs.zip(ys) { x, y -> Vector2(x, y) }
// [Vector2(100, 150), Vector2(200, 250), Vector2(300, 350)]

// 또는 간단하게
val points2 = xs.zip(ys).map { (x, y) -> Vector2(x, y) }
```

---

## 실전 예제 1: 다중 필터링

```kotlin
program {
    val particles = mutableListOf<Particle>()
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 마우스 클릭 시 입자 생성
        repeat(5) {
            particles.add(Particle(mouse.position))
        }
        
        // 조건: 살아있고, x < 500이고, 크기 > 2인 입자들을 그리기
        particles
            .filter { it.isAlive }
            .filter { it.x < 500.0 }
            .filter { it.size > 2.0 }
            .forEach { p ->
                p.update()
                circle(p.x, p.y, p.size)
            }
        
        // 또는 한 번에
        particles
            .filter { it.isAlive && it.x < 500.0 && it.size > 2.0 }
            .forEach { p ->
                p.update()
                circle(p.x, p.y, p.size)
            }
    }
}
```

### 2. 데이터 변환 파이프라인

```kotlin
data class Circle(val pos: Vector2, val radius: Double)

program {
    extend {
        clear(ColorRGBa.BLACK)
        
        val rawData = (0 until 50).map {
            Vector2(random(width), random(height))
        }
        
        // 데이터 정제 및 변환
        val circles = rawData
            .filter { it.x > 100.0 && it.y > 100.0 }  // 경계 제외
            .map { pos ->
                Circle(pos, radius = 20.0)
            }
            .sortedBy { it.pos.y }  // 위에서 아래로
        
        // 그리기
        circles.forEachIndexed { index, circle ->
            val alpha = 1.0 - (index.toDouble() / circles.size)
            fill = ColorRGBa(1.0, 1.0, 1.0, alpha)
            drawer.circle(circle.pos.x, circle.pos.y, circle.radius)
        }
    }
}
```

**파이프라인 단계:**
1. `filter` — 좌표가 화면 경계 내인 것만
2. `map` — Vector2를 Circle 객체로 변환
3. `sortedBy` — y 좌표 순서로 정렬 (뒤에서 앞으로 렌더링)
4. `forEachIndexed` — 인덱스와 함께 반복 (투명도 계산)

### 3. 격자 패턴 (함수형)

```kotlin
extend {
    clear(ColorRGBa.BLACK)
    
    val cols = 10
    val rows = 10
    val cellWidth = width / cols
    val cellHeight = height / rows
    
    // 격자 좌표 생성
    val points = (0 until cols).flatMap { x ->
        (0 until rows).map { y ->
            Vector2(
                x * cellWidth + cellWidth / 2,
                y * cellHeight + cellHeight / 2
            )
        }
    }
    
    // 모든 점 그리기
    points.forEach { circle(it.x, it.y, 20.0) }
}
```

**`flatMap` 설명:**
- `map`은 각 요소를 변환: `List<A> → List<B>`
- `flatMap`은 각 요소를 리스트로 변환 후 펼침: `List<A> → List<List<B>> → List<B>`

격자에서 `flatMap`의 효과:
- 외부 `flatMap` — 각 x에 대해 모든 y 조합 생성
- 결과: cols × rows 개의 포인트

---

## `it` vs 명시적 파라미터

### `it` 사용 (짧은 람다)

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)

// it 사용
val doubled = numbers.map { it * 2 }

// 명시적 이름
val doubled2 = numbers.map { n -> n * 2 }
```

### 명시적 파라미터 (복잡한 로직)

```kotlin
// 간단: it 사용
val squares = numbers.map { it * it }

// 복잡: 명시적 이름으로 명확하게
val processed = numbers.map { n ->
    val doubled = n * 2
    val squared = doubled * doubled
    squared
}

// 여러 파라미터
val pairs = xs.zip(ys).map { (x, y) ->
    Vector2(x, y)
}
```

**규칙:**
- 한 줄 표현식 → `it`
- 여러 줄 로직 → 명시적 파라미터 이름

---

## `map` vs `forEach`의 선택

**중요한 구분:**

```kotlin
// 잘못된 사용: map으로 부수효과
val result = particles.map { p ->
    p.update()
    circle(p.x, p.y, p.size)  // 부수효과!
}
// map의 반환값이 필요 없어서 낭비

// 올바른 사용: forEach로 부수효과
particles.forEach { p ->
    p.update()
    circle(p.x, p.y, p.size)
}
```

**선택 기준:**
- **값을 만들 때** → `map`
- **부수효과만 있을 때** → `forEach`
- **조건부로 선택** → `filter`
- **값을 만들고 필터링** → `map` + `filter` 조합

---

## 실전 예제: 복잡한 파이프라인

```kotlin
data class Star(
    val pos: Vector2,
    val brightness: Double,
    val age: Double = 0.0
)

program {
    randomSeed(42L)
    
    val stars = (0 until 500)
        .map { _ ->
            Star(
                pos = Vector2(random(width), random(height)),
                brightness = random(0.3, 1.0)
            )
        }
        .toMutableList()
    
    var time = 0.0
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 별 업데이트 및 필터링
        stars
            .filter { it.age < 1000.0 }  // 수명 체크
            .sortedBy { it.brightness }   // 어두운 순서 (앞에서 뒤로)
            .forEach { star ->
                val alpha = sin(time * 0.01 + star.age * 0.001) * 0.5 + 0.5
                fill = ColorRGBa(1.0, 1.0, 1.0, alpha)
                circle(star.pos.x, star.pos.y, 2.0)
            }
        
        // 별의 나이 증가
        stars.forEach { it.age += 1.0 }
        
        time += 1.0
    }
}
```

**파이프라인:**
1. `filter { it.age < 1000.0 }` — 살아있는 별만
2. `sortedBy { it.brightness }` — 밝기 순서로 (앞뒤 깊이감)
3. `forEach { ... }` — 각 별을 그리기 (투명도는 시간에 따라 변함)

---

## 컬렉션의 함정과 주의

### 1. 불변 vs 가변의 혼동

```kotlin
val list = listOf(1, 2, 3)
list.add(4)  // ❌ 에러! 불변 리스트

val mutableList = mutableListOf(1, 2, 3)
mutableList.add(4)  // ✅ OK
```

### 2. `toList()` 사용으로 중간 리스트 생성

```kotlin
// 비효율: 중간 리스트 세 개 생성
val result = numbers.filter { it > 5 }.toList()
    .map { it * 2 }.toList()
    .filter { it < 100 }.toList()

// 효율: 중간 리스트 없음
val result = numbers
    .filter { it > 5 }
    .map { it * 2 }
    .filter { it < 100 }
    .toList()  // 마지막에만 필요하면 변환
```

### 3. `forEach` vs `for` 루프의 성능

**코틀린에서는 차이가 거의 없습니다.** 컴파일러가 최적화합니다.

**가독성으로 선택하세요:**
- 명확한 의도 → `forEach`
- 복잡한 제어 (break, continue) → `for` 루프

---

## 정리: 함수형 메서드 선택 가이드

| 메서드 | 입력 | 출력 | 용도 |
|--------|------|------|------|
| `map` | List<A> | List<B> | 변환 |
| `filter` | List<A> | List<A> | 선택 |
| `forEach` | List<A> | Unit | 반복 (부수효과) |
| `reduce` | List<A> | A | 누적 |
| `fold` | List<A> + 초기값 | B | 누적 (초기값) |
| `groupBy` | List<A> | Map<K, List<A>> | 분류 |
| `zip` | List<A> + List<B> | List<Pair<A,B>> | 쌍 |
| `flatMap` | List<A> | List<B> | 변환 후 펼침 |

---

## p5.js vs OPENRNDR: 코드 스타일 비교

### p5.js: 명령형

```javascript
let particles = [];

for (let i = 0; i < 100; i++) {
    particles.push({
        x: random(width),
        y: random(height),
        life: 100
    });
}

let alive = [];
for (let p of particles) {
    if (p.life > 0) {
        alive.push(p);
    }
}

for (let p of alive) {
    circle(p.x, p.y, 5);
    p.life--;
}
```

### OPENRNDR: 선언형

```kotlin
program {
    val particles = (0 until 100)
        .map {
            Particle(
                x = random(width),
                y = random(height),
                life = 100
            )
        }
        .toMutableList()
    
    extend {
        particles
            .filter { it.life > 0 }
            .forEach { p ->
                circle(p.x, p.y, 5.0)
                p.life--
            }
    }
}
```

**OPENRNDR의 장점:**
- 데이터 흐름이 명확함 (파이프라인)
- 중간 변수 최소화
- 의도가 한눈에 보임

---

## 다음 단계: Extension 함수

다음 글에서는 **Extension 함수**를 다룹니다.

Extension 함수를 사용하면, `Drawer`나 `Vector2`에 새로운 메서드를 "붙일" 수 있습니다:

```kotlin
// Extension 함수 정의
fun Drawer.grid(cols: Int, rows: Int, cellSize: Double) {
    for (x in 0 until cols) {
        for (y in 0 until rows) {
            point(x * cellSize, y * cellSize)
        }
    }
}

// 사용
extend {
    grid(10, 10, 50.0)  // 마치 Drawer의 내장 메서드처럼
}
```

이것이 OPENRNDR 코드를 정말로 우아하고 재사용 가능하게 만듭니다.

---

## 연습 문제

### 1. 기본 함수형 메서드

```kotlin
val numbers = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

// map: 각 수를 3배로
val tripled = numbers.map { it * 3 }
println(tripled)  // [3, 6, 9, 12, ...]

// filter: 홀수만
val odds = numbers.filter { it % 2 == 1 }
println(odds)  // [1, 3, 5, 7, 9]

// map + filter: 홀수를 3배로
val result = numbers
    .filter { it % 2 == 1 }
    .map { it * 3 }
println(result)  // [3, 9, 15, 21, 27]
```

이 코드를 실행하고, 각 단계의 결과를 확인하세요.

### 2. 원 그리기 (함수형)

```kotlin
program {
    extend {
        clear(ColorRGBa.BLACK)
        
        (0 until 20)
            .map { i -> Vector2(i * 40.0, 400.0) }
            .forEach { pos ->
                circle(pos.x, pos.y, 20.0)
            }
    }
}
```

실행해 본 후:
- 원을 세로로 배치해보세요
- 원의 개수를 50개로 늘려보세요
- `filter`를 추가해 일부 원만 그려보세요

### 3. 입자 시스템 (함수형)

```kotlin
program {
    val particles = mutableListOf<Particle>()
    
    extend {
        clear(ColorRGBa.BLACK)
        
        repeat(5) {
            particles.add(Particle(mouse.position))
        }
        
        // 함수형 파이프라인으로 그리기
        particles
            .filter { it.isAlive }
            .forEach { p ->
                p.update()
                circle(p.x, p.y, p.size)
            }
        
        particles.removeIf { !it.isAlive }
    }
}
```

실행해 본 후:
- 투명도를 시간에 따라 변하게 해보세요
- 살아있는 입자 수를 콘솔에 출력해보세요

### 4. 복잡한 파이프라인 (심화)

```kotlin
extend {
    clear(ColorRGBa.BLACK)
    
    val data = (0 until 100)
        .map { Vector2(random(width), random(height)) }
        .filter { it.x > 200.0 && it.y > 200.0 }
        .sortedBy { it.x + it.y }  // 좌상향 대각선 순서
        .take(20)  // 처음 20개만
    
    data.forEachIndexed { index, pos ->
        val alpha = 1.0 - (index.toDouble() / data.size)
        fill = ColorRGBa(1.0, 1.0, 1.0, alpha)
        circle(pos.x, pos.y, 10.0)
    }
}
```

실행해 본 후, 각 단계를 변경해 보세요.

---

**이전 글**: [OPENRNDR 시작하기 (4): 범위와 반복 — 루프 제어](./openrndr_04_range_loop.md)  
**다음 글**: OPENRNDR 시작하기 (6): Extension 함수 — OPENRNDR 스타일의 핵심
