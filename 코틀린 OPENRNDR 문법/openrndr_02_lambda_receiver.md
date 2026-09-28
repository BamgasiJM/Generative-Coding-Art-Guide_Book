# OPENRNDR 시작하기 (2): 람다 표현식과 리시버 — Drawer의 비밀

지난 글에서 본 `extend { }` 블록을 다시 보세요.

```kotlin
extend {
    drawer.clear(ColorRGBa.BLACK)
    drawer.circle(400.0, 400.0, 100.0)
}
```

혹시 이런 의문이 들었나요?

> "왜 `drawer.circle()`이 아니라 그냥 `circle()`이라고 쓸 수 없을까?"

또는, p5.js에서는 이렇게 쓰는데:

```javascript
background(0);
circle(400, 400, 100);
```

OPENRNDR에서는 왜 항상 `drawer.`를 붙여야 하나요?

**답: 코틀린의 "리시버(receiver)" 문법 때문입니다.**

이 글에서는 **람다 표현식**과 **리시버**가 무엇인지, 그리고 **어떻게 제너러티브 아트 코드를 깔끔하게 만드는지**를 상세히 설명하겠습니다.

---

## 먼저 람다 표현식(Lambda Expression)이란?

람다 표현식은 **"이름 없는 함수"** 또는 **"익명 함수"**입니다. JavaScript의 화살표 함수 `=>` 같은 거죠.

### JavaScript의 화살표 함수

```javascript
// 일반 함수
function double(x) {
  return x * 2;
}

// 화살표 함수 (람다와 동일)
const double = (x) => x * 2;

// 사용
console.log(double(5));  // 10
```

### 코틀린의 람다 표현식

```kotlin
// 일반 함수
fun double(x: Int): Int {
    return x * 2
}

// 람다 표현식
val double = { x: Int -> x * 2 }

// 사용
println(double(5))  // 10
```

**코틀린 람다의 문법:**
```
{ 입력 파라미터 -> 함수 본체 }
```

예제들을 보겠습니다.

```kotlin
// 인자가 없는 람다
val greet = { println("Hello, OPENRNDR!") }
greet()  // "Hello, OPENRNDR!" 출력

// 인자가 하나인 람다
val square = { x: Int -> x * x }
println(square(5))  // 25

// 인자가 여러 개인 람다
val add = { x: Int, y: Int -> x + y }
println(add(3, 5))  // 8
```

---

## 람다를 함수에 전달하기

코틀린에서 **함수는 일급 객체(first-class citizen)**입니다. 즉, 함수를 다른 함수에 인자로 넘길 수 있습니다.

```kotlin
// 람다를 받는 함수
fun repeat(times: Int, action: (Int) -> Unit) {
    for (i in 0 until times) {
        action(i)
    }
}

// 람다를 전달
repeat(3, { i -> println(i) })
```

실행 결과:
```
0
1
2
```

**여기서 주목할 점:**
- `action: (Int) -> Unit` — 이 함수는 `Int`를 받아서 아무것도 반환하지 않는 함수(`Unit`)를 기대합니다
- 람다 `{ i -> println(i) }`가 그 역할을 합니다

---

## 마지막 인자가 람다면 소괄호 밖으로!

코틀린에는 편의상 **"마지막 인자가 람다면, 소괄호 밖으로 빼도 된다"**는 규칙이 있습니다.

```kotlin
// 공식적인 형태
repeat(3, { i -> println(i) })

// 람다를 소괄호 밖으로 (권장)
repeat(3) { i -> println(i) }
```

둘 다 같은 코드예요. 하지만 두 번째 형태가 훨씬 읽기 편합니다.

**이제 이 패턴이 어디서 나타나는지 보세요:**

```kotlin
extend {
    drawer.circle(400.0, 400.0, 100.0)
}
```

`extend`는 **람다를 인자로 받는 함수**입니다. 그래서 소괄호 밖으로 `{ }` 블록을 뺀 거예요.

공식 형태로 쓰면:
```kotlin
extend({ 
    drawer.circle(400.0, 400.0, 100.0)
})
```

하지만 람다 규칙을 적용하면:
```kotlin
extend { 
    drawer.circle(400.0, 400.0, 100.0)
}
```

훨씬 깔끔하죠?

---

## 리시버(Receiver)란 무엇인가?

이제 핵심입니다.

람다는 **"리시버"**를 가질 수 있습니다. 리시버는 **"그 람다 안에서 암묵적으로 `this`인 객체"**를 의미합니다.

### 리시버가 없는 람다

```kotlin
val double = { x: Int -> x * 2 }
// 여기서 x는 파라미터일 뿐, 리시버가 없어요
```

### 리시버가 있는 람다

```kotlin
val greetPerson: (String) -> Unit = { name ->
    println("Hello, $name")
}

// 하지만 이건 문법적으로 좀 이상합니다.
// 코틀린은 "리시버 타입"이라는 별도 문법을 제공합니다:

val greetPerson: String.() -> Unit = {
    println("Hello, $this")
}

// 사용할 때:
greetPerson("Alice")  // 아니, 이건 안 됩니다...
"Alice".greetPerson()  // 이렇게 써야 합니다!
```

**리시버의 핵심:**
- 람다가 "누군가"의 메서드처럼 작동
- 그 "누군가" 안에서 `this`를 생략할 수 있음

### 실제 예제: StringBuilder

코틀린 표준 라이브러리의 `apply` 함수가 좋은 예입니다.

```kotlin
val text = StringBuilder()
text.append("Hello")
text.append(" ")
text.append("World")
println(text)  // "Hello World"
```

이걸 람다 리시버로 쓰면:

```kotlin
val text = StringBuilder().apply {
    append("Hello")
    append(" ")
    append("World")
}
println(text)  // "Hello World"
```

**무슨 차이가 있나요?**
- `append()` 앞에 `text.`를 안 붙였습니다!
- `apply { }` 블록 안에서 `this`가 `StringBuilder` 객체입니다
- 따라서 `this.append()`를 `append()`로 축약 가능

---

## OPENRNDR의 `extend` 블록: 리시버의 실제 활용

이제 OPENRNDR로 돌아옵시다.

```kotlin
extend {
    drawer.circle(400.0, 400.0, 100.0)
}
```

`extend` 함수의 정의는 (개념적으로) 이렇습니다:

```kotlin
fun extend(action: Drawer.() -> Unit) {
    // ... (프레임마다 반복)
    drawer.action()
}
```

**`Drawer.() -> Unit`이 의미하는 것:**
- 이 람다는 `Drawer`를 리시버로 가져야 함
- 즉, 람다 블록 안에서 `this == drawer`

그래서 이렇게 쓸 수 있게 됩니다:

```kotlin
extend {
    // this가 drawer이므로
    clear(ColorRGBa.BLACK)      // this.clear() 축약
    circle(400.0, 400.0, 100.0) // this.circle() 축약
}
```

**명시적으로 쓰면:**

```kotlin
extend { drawer ->
    drawer.clear(ColorRGBa.BLACK)
    drawer.circle(400.0, 400.0, 100.0)
}
```

두 형태 모두 같은 코드예요. 하지만 첫 번째가 OPENRNDR 관례입니다.

---

## 제너러티브 아트 관점: 왜 리시버가 중요한가?

리시버 문법이 없다면, OPENRNDR 코드는 이렇게 될 거예요:

```kotlin
// 리시버 없이 (상상하기만 하세요)
extend {
    drawer.clear(ColorRGBa.BLACK)
    drawer.fill = ColorRGBa.PINK
    drawer.stroke = ColorRGBa.CYAN
    drawer.strokeWeight = 2.0
    drawer.circle(100.0, 100.0, 50.0)
    drawer.rectangle(200.0, 200.0, 100.0, 100.0)
    drawer.line(300.0, 300.0, 400.0, 400.0)
}
```

리시버로 (실제 OPENRNDR):

```kotlin
extend {
    clear(ColorRGBa.BLACK)
    fill = ColorRGBa.PINK
    stroke = ColorRGBa.CYAN
    strokeWeight = 2.0
    circle(100.0, 100.0, 50.0)
    rectangle(200.0, 200.0, 100.0, 100.0)
    line(300.0, 300.0, 400.0, 400.0)
}
```

**차이점:**
- 코드가 훨씬 간결하고 읽기 쉬움
- "그리기 명령"에 집중하기 좋음
- 보일러플레이트(`drawer.` 반복)이 없음

이게 제너러티브 아트 코드에서 얼마나 중요한지 생각해 보세요. 한 프레임에 수백 개의 도형을 그릴 때, `drawer.`를 매번 타이핑하는 건 비효율적입니다.

---

## 람다 안에서 여러 줄 코드 쓰기

람다는 **여러 줄의 코드를 포함**할 수 있습니다. 마지막 표현식이 반환값이 됩니다.

```kotlin
// 단일 표현식
val double = { x: Int -> x * 2 }

// 여러 줄 (블록 형태)
val compute = { x: Int ->
    val squared = x * x
    val doubled = x * 2
    squared + doubled
}
```

OPENRNDR에서:

```kotlin
extend {
    val t = System.currentTimeMillis() / 1000.0
    
    for (i in 0..10) {
        val x = 100.0 * i
        val y = 400.0 + 100.0 * sin(t + i)
        circle(x, y, 20.0)
    }
}
```

---

## 람다에서 `it` 사용하기

**인자가 정확히 하나일 때**, 그 인자를 `it`이라고 부를 수 있습니다.

```kotlin
// 명시적 이름
val double = { x: Int -> x * 2 }

// it 사용 (인자가 하나일 때만)
val double = { it: Int -> it * 2 }

// 타입 추론되면 더 간단
val double = { it -> it * 2 }
val double = { it * 2 }  // 본체가 간단하면 생략도 가능
```

**OPENRNDR과는 직접 관련이 없지만**, 함수형 프로그래밍에서 자주 봅니다.

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val doubled = numbers.map { it * 2 }  // [2, 4, 6, 8, 10]
```

다음 글(컬렉션)에서 이 패턴을 자세히 다루겠습니다.

---

## 실전 예제 1: 리시버를 명시적으로 사용하기

이전 글의 마우스 인터랙션 예제를 리시버의 명시적 형태로 쓰면:

```kotlin
program {
    extend { drawer ->
        drawer.clear(ColorRGBa.BLACK)
        drawer.fill = ColorRGBa.PINK
        drawer.circle(mouse.position.x, mouse.position.y, 50.0)
    }
}
```

하지만 OPENRNDR 관례는 이렇습니다:

```kotlin
program {
    extend {
        clear(ColorRGBa.BLACK)
        fill = ColorRGBa.PINK
        circle(mouse.position.x, mouse.position.y, 50.0)
    }
}
```

둘 다 작동하지만, **두 번째 형태가 표준입니다.**

---

## 실전 예제 2: 상태 변화와 리시버

시간에 따라 변하는 제너러티브 아트를 만들어 봅시다.

```kotlin
fun main() = application {
    configure {
        width = 800
        height = 800
    }
    
    program {
        var t = 0.0
        
        extend {
            clear(ColorRGBa.BLACK)
            
            // 중앙에서 방사형으로 원들 배치
            for (i in 0..8) {
                val angle = (TWO_PI / 9) * i + t
                val distance = 100.0 + 50.0 * sin(t)
                
                val x = 400.0 + distance * cos(angle)
                val y = 400.0 + distance * sin(angle)
                
                circle(x, y, 20.0)
            }
            
            t += 0.05
        }
    }
}
```

**리시버 덕분에 가능한 코드의 간결성:**
- `clear()`, `circle()` 호출이 간단
- 반복문 안에서도 `fill`, `stroke` 같은 프로퍼티에 쉽게 접근 가능

리시버가 없었다면 매번 `drawer.circle()`, `drawer.fill = ...` 이렇게 써야 합니다.

---

## 실전 예제 3: 격자 패턴 (리시버의 진가)

더 복잡한 패턴을 보겠습니다.

```kotlin
program {
    var t = 0.0
    
    extend {
        clear(ColorRGBa.BLACK)
        fill = ColorRGBa.WHITE
        stroke = ColorRGBa.YELLOW
        strokeWeight = 2.0
        
        val cols = 10
        val rows = 10
        val cellWidth = width / cols
        val cellHeight = height / rows
        
        for (x in 0 until cols) {
            for (y in 0 until rows) {
                val px = x * cellWidth + cellWidth / 2
                val py = y * cellHeight + cellHeight / 2
                
                // 각 셀마다 다른 크기의 원
                val size = 20.0 + 15.0 * sin(t + x + y)
                
                circle(px, py, size)
            }
        }
        
        t += 0.05
    }
}
```

**여기서 리시버의 가치:**
- `fill`, `stroke`, `strokeWeight` 설정이 명확
- 100개의 원을 그리는데 `drawer.` 반복이 없음
- 코드 가독성이 매우 높음

만약 p5.js였다면:
```javascript
for (let x = 0; x < cols; x++) {
    for (let y = 0; y < rows; y++) {
        // ... 계산
        fill(255);
        stroke(255, 255, 0);
        strokeWeight(2);
        circle(px, py, size);
    }
}
```

OPENRNDR은 setup 시점에 한 번만 상태를 설정하고, 이후 그리기에만 집중할 수 있어요.

---

## 고급: 커스텀 리시버 함수 만들기

**여기서는 다음 글에서 다룰 "Extension 함수"와 연결됩니다.**

아직 완전히 이해할 필요는 없지만, 미리 보면:

```kotlin
// Drawer에 새로운 함수 추가
fun Drawer.grid(cols: Int, rows: Int, cellSize: Double) {
    for (x in 0 until cols) {
        for (y in 0 until rows) {
            val px = x * cellSize + cellSize / 2
            val py = y * cellSize + cellSize / 2
            point(px, py)
        }
    }
}

// 사용
extend {
    clear(ColorRGBa.BLACK)
    grid(10, 10, 80.0)  // 훨씬 간결!
}
```

---

## 정리: 람다와 리시버의 관계

| 개념 | 설명 | 예제 |
|------|------|------|
| **람다** | 이름 없는 함수 | `{ x -> x * 2 }` |
| **리시버** | 람다 안에서 암묵적 `this` | `String.() -> Unit` |
| **마지막 인자 람다** | 소괄호 밖으로 뺄 수 있음 | `repeat(5) { ... }` |
| **리시버 생략** | `this.` 대신 그냥 호출 | `clear()` 대신 안 씀 |

**OPENRNDR에서의 패턴:**

```kotlin
extend {              // <- 람다를 인자로 받음
    clear(...)        // <- Drawer를 리시버로 가짐
    circle(...)       // <- this. 생략
}
```

---

## p5.js와의 비교: 왜 OPENRNDR 방식이 강력한가?

### p5.js 스타일 (전역 함수)
```javascript
function draw() {
    background(0);
    fill(255);
    circle(400, 400, 100);
}
```
- 모든 함수가 전역 네임스페이스에 있음
- 상태 관리가 암묵적 (어디서든 `fill()` 호출 가능)

### OPENRNDR 스타일 (객체 + 리시버)
```kotlin
extend {
    clear(ColorRGBa.BLACK)
    fill = ColorRGBa.WHITE
    circle(400.0, 400.0, 100.0)
}
```
- 모든 작업이 `Drawer` 객체에 묶여있음
- 상태가 명시적 (누구의 상태인지 알 수 있음)
- 리시버 덕분에 간결한 문법 가능

**대규모 제너러티브 아트 프로젝트에서는 OPENRNDR 방식이 훨씬 더 체계적입니다.**

---

## 다음 단계

다음 글에서는 **`val`과 `var`** — 불변성의 설계를 다룹니다. 

제너러티브 아트에서 "같은 씨드로 같은 결과를 만든다"는 개념은 얼마나 중요할까요? 그것이 바로 `val`과 `var`의 구분과 깊은 관련이 있습니다.

---

## 연습 문제

1. **리시버 명시 vs 생략**
   - 아래 두 코드가 같다는 걸 확인하세요:
   ```kotlin
   // 형태 1: 리시버 명시
   extend { drawer ->
       drawer.circle(400.0, 400.0, 50.0)
   }
   
   // 형태 2: 리시버 생략 (관례)
   extend {
       circle(400.0, 400.0, 50.0)
   }
   ```

2. **마우스와 시간**
   ```kotlin
   program {
       var t = 0.0
       extend {
           clear(ColorRGBa.BLACK)
           
           // 마우스 위치를 중심으로 원이 시간에 따라 커졌다 작아졌다
           val radius = 30.0 + 30.0 * sin(t)
           circle(mouse.position.x, mouse.position.y, radius)
           
           t += 0.02
       }
   }
   ```
   이 코드를 실행하고, 마우스를 움직이면서 원의 크기 변화를 관찰하세요.

3. **격자 패턴 커스터마이징**
   - 위의 "격자 패턴" 예제에서 `cols`, `rows` 값을 바꿔보세요
   - `fill` 색상을 `t` 값에 따라 변하도록 수정해 보세요

---

**이전 글**: [OPENRNDR 시작하기 (1): 프로그램 구조](./openrndr_01_program_structure.md)  
**다음 글**: OPENRNDR 시작하기 (3): `val`과 `var` — 불변성의 설계
