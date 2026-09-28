# OPENRNDR 시작하기 (3): `val`과 `var` — 불변성의 설계

코틀린을 배우면서 가장 먼저 마주치는 개념은 `val`과 `var`입니다.

```kotlin
val position = Vector2(100.0, 200.0)  // val
var time = 0.0                         // var
```

간단해 보이지만, **이 구분이 OPENRNDR 제너러티브 아트의 핵심**입니다.

이 글에서는:
- `val`과 `var`의 기술적 차이
- 왜 제너러티브 아트에서 이 구분이 중요한지
- "재현성(reproducibility)"과 "변동성(variability)"을 코드로 어떻게 관리하는지

를 다루겠습니다.

---

## 기본 개념: 재할당 가능 vs 불가능

### `val` — 값 (Value), 재할당 불가

```kotlin
val x = 10
x = 20  // ❌ 에러! "Val cannot be reassigned"
```

`val`로 선언한 변수는 **한 번 값이 정해지면, 그 이후로 다른 값을 할당할 수 없습니다.**

### `var` — 변수 (Variable), 재할당 가능

```kotlin
var x = 10
x = 20  // ✅ OK
x = 30  // ✅ OK
```

`var`로 선언한 변수는 **몇 번이든 값을 바꿀 수 있습니다.**

---

## p5.js와의 비교

### p5.js: 모든 변수가 `var`처럼 작동

```javascript
let x = 10;
x = 20;  // OK
x = 30;  // OK

const y = 10;
y = 20;  // 에러
```

p5.js는 `let`과 `const`를 구분하지만, **대부분의 상황에서 `let`을 씁니다.**

### OPENRNDR: `val` 우선 선택

```kotlin
val x = 10        // 대부분 여기서 시작
var time = 0.0    // 변해야 하는 것만 var
```

코틀린 철학은 다릅니다: **"변하지 않는 것이 기본, 변할 필요가 있을 때만 `var` 사용"**

이것을 "불변성 우선(immutability first)" 설계라고 부릅니다.

---

## 제너러티브 아트에서의 의미: "재현성"

제너러티브 아트의 중요한 개념 중 하나는 **"같은 입력(씨드)이면 같은 결과(아트워크)가 나와야 한다"**는 것입니다.

### 나쁜 예: 매 실행마다 다른 결과

```kotlin
program {
    extend {
        clear(ColorRGBa.BLACK)
        
        // random()만 호출 — 매 번 다른 값
        val x = random(width)
        val y = random(height)
        circle(x, y, 50.0)
    }
}
```

이 코드는 실행할 때마다 다른 위치에 원을 그립니다. **재현할 수 없어요.**

### 좋은 예: 씨드로 재현성 보장

```kotlin
program {
    randomSeed(42L)  // 고정 씨드
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 같은 씨드면 매번 같은 값
        val x = random(width)
        val y = random(height)
        circle(x, y, 50.0)
    }
}
```

씨드를 고정하면, 프로그램을 재실행해도 같은 위치에 원이 나타납니다.

**여기서 `val`의 역할:**
- `val x = random(width)` — 한 번 정해진 값은 바뀌지 않음
- 다음 프레임도 같은 `x` 값을 사용

---

## 컬렉션과 `val`: 내용물은 변할 수 있다

**중요한 구분:**

```kotlin
val list = mutableListOf<Int>(1, 2, 3)  // val
list[0] = 10  // ✅ OK! 리스트의 내용물은 변경 가능
list = listOf(4, 5, 6)  // ❌ 에러! list 자체는 재할당 불가
```

`val`은 **"변수 자체의 재할당"을 막는 것**이지, **"객체의 내용물 변경"을 막는 것이 아닙니다.**

### OPENRNDR에서의 활용

```kotlin
program {
    val particles = mutableListOf<Particle>()  // val
    
    extend {
        // particles 리스트에 새 입자 추가
        particles.add(Particle(mouse.position))
        
        // 각 입자의 상태 업데이트
        for (p in particles) {
            p.position += p.velocity  // 입자 위치 변경
            circle(p.position.x, p.position.y, 5.0)
        }
    }
}
```

여기서:
- `val particles` — 리스트 자체는 바뀌지 않음
- `particles.add(...)` — 리스트의 내용물은 변경됨
- `p.position += p.velocity` — 각 입자의 상태는 변경됨

이것이 OPENRNDR의 전형적인 패턴입니다.

---

## 실전 예제 1: 재현성 있는 격자 패턴

```kotlin
fun main() = application {
    configure {
        width = 800
        height = 800
    }
    
    program {
        randomSeed(12345L)  // 고정 씨드
        
        // 매 프레임 변하지 않을 데이터는 val
        val cols = 10
        val rows = 10
        val cellWidth = width / cols
        val cellHeight = height / rows
        
        // 각 셀마다 고정된 "개성"을 미리 생성
        val cellSizes = List(cols * rows) { random(20.0, 50.0) }
        
        var t = 0.0  // 시간은 변해야 하므로 var
        
        extend {
            clear(ColorRGBa.BLACK)
            fill = ColorRGBa.WHITE
            
            for (x in 0 until cols) {
                for (y in 0 until rows) {
                    val index = x + y * cols
                    val baseSize = cellSizes[index]  // val로 고정된 크기
                    
                    val px = x * cellWidth + cellWidth / 2
                    val py = y * cellHeight + cellHeight / 2
                    
                    // 크기는 기본값 + 시간에 따른 진동
                    val size = baseSize + 10.0 * sin(t)
                    
                    circle(px, py, size)
                }
            }
            
            t += 0.05
        }
    }
}
```

**여기서의 `val`과 `var` 구분:**

| 변수 | 타입 | 이유 |
|------|------|------|
| `cols`, `rows` | `val` | 화면 설정이 바뀌지 않는 한 고정 |
| `cellSizes` | `val` | 프로그램 시작 시 한 번 결정, 이후 변하지 않음 |
| `t` | `var` | 매 프레임 증가 |

**재현성:**
- `randomSeed(12345L)`로 고정하면, 프로그램을 10번 실행해도 똑같은 패턴이 나옵니다
- `cellSizes`가 `val`로 고정되어 있으므로

---

## 실전 예제 2: 입자 시스템 (Particle System)

입자 시스템은 제너러티브 아트에서 매우 인기 있는 패턴입니다.

```kotlin
data class Particle(
    var position: Vector2,
    var velocity: Vector2,
    val lifespan: Double,  // 생성 시점에 결정, 변하지 않음
    var age: Double = 0.0  // 시간에 따라 증가
)

program {
    val particles = mutableListOf<Particle>()  // val
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 마우스 위치에서 새 입자 생성
        repeat(5) {
            val p = Particle(
                position = mouse.position,
                velocity = Vector2(random(-2.0, 2.0), random(-2.0, 2.0)),
                lifespan = random(30.0, 60.0)
            )
            particles.add(p)
        }
        
        // 입자 업데이트 및 그리기
        for (p in particles) {
            p.position += p.velocity      // 위치 업데이트
            p.age += 1.0                  // 나이 증가
            p.velocity *= 0.98            // 마찰(friction)
            
            val alpha = 1.0 - (p.age / p.lifespan)  // 투명도
            fill = ColorRGBa(1.0, 1.0, 1.0, alpha)
            
            circle(p.position.x, p.position.y, 3.0)
        }
        
        // 죽은 입자 제거
        particles.removeIf { it.age > it.lifespan }
    }
}
```

**`val`과 `var`의 역할:**

- `val particles` — 리스트 자체는 고정, 하지만 내용물(입자들)은 추가/제거
- `val lifespan` — 입자가 생성될 때 수명이 정해지고, 이후 변하지 않음
- `var age` — 매 프레임 증가
- `var position`, `var velocity` — 매 프레임 업데이트

이 패턴은 p5.js, nannou, openFrameworks 모두에서 동일하게 나타나는 표준 패턴입니다.

---

## 타입 추론과 `val`

코틀린은 **타입 추론**이 매우 강력합니다.

```kotlin
val x = 10           // Int로 자동 추론
val y = 10.0         // Double로 자동 추론
val z = "hello"      // String으로 자동 추론
val colors = listOf(ColorRGBa.RED, ColorRGBa.GREEN)  // List<ColorRGBa>로 추론
```

명시적으로 타입을 쓸 수도 있습니다:

```kotlin
val x: Int = 10
val y: Double = 10.0
val pos: Vector2 = Vector2(100.0, 200.0)
```

OPENRNDR 코드에서는 **명시적 타입을 거의 안 씁니다.** 대신 타입 추론에 의존합니다.

---

## 프로퍼티와 `val`: Drawer의 상태

이전 글에서 본 이것들을 기억하나요?

```kotlin
extend {
    fill = ColorRGBa.PINK
    stroke = ColorRGBa.CYAN
    strokeWeight = 2.0
}
```

`fill`, `stroke`, `strokeWeight`는 `Drawer` 객체의 **프로퍼티(property)**입니다. 이들은 `var`입니다.

```kotlin
// Drawer의 정의 (개념적)
class Drawer {
    var fill: ColorRGBa = ColorRGBa.WHITE
    var stroke: ColorRGBa = ColorRGBa.BLACK
    var strokeWeight: Double = 1.0
    // ...
}
```

그래서 `extend` 블록 안에서 얼마든지 변경할 수 있습니다:

```kotlin
extend {
    fill = ColorRGBa.RED
    circle(100.0, 100.0, 50.0)
    
    fill = ColorRGBa.BLUE  // 상태 변경
    circle(200.0, 200.0, 50.0)
}
```

---

## 실전 예제 3: 상태 변경의 명확성

```kotlin
program {
    var palette = 0  // 팔레트 인덱스
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 키보드 입력에 따라 팔레트 변경
        when {
            keyboard.pressedKeys.contains(KEY_1) -> palette = 0
            keyboard.pressedKeys.contains(KEY_2) -> palette = 1
            keyboard.pressedKeys.contains(KEY_3) -> palette = 2
        }
        
        // 현재 팔레트에 맞는 색상으로 그리기
        val colors = when (palette) {
            0 -> listOf(ColorRGBa.RED, ColorRGBa.YELLOW, ColorRGBa.ORANGE)
            1 -> listOf(ColorRGBa.CYAN, ColorRGBa.MAGENTA, ColorRGBa.GREEN)
            else -> listOf(ColorRGBa.GRAY, ColorRGBa.WHITE, ColorRGBa.BLACK)
        }
        
        for (i in 0..9) {
            for (j in 0..9) {
                fill = colors[(i + j) % colors.size]
                rectangle(i * 80.0, j * 80.0, 80.0, 80.0)
            }
        }
    }
}
```

**여기서:**
- `var palette` — 사용자 입력에 따라 변함
- `val colors` — 현재 프레임의 `palette` 값에 따라 결정되고, 그 프레임 내에서는 변하지 않음

---

## `val` 우선: 코드 안정성

`val`을 우선으로 사용하는 습관은:

1. **버그를 줄입니다**
   - 의도치 않은 재할당이 컴파일 타임에 감지됨

2. **코드를 읽기 쉽게 합니다**
   - 리더가 "이 값은 바뀌지 않는구나"라고 즉시 알 수 있음

3. **동시성 문제를 피합니다**
   - 나중에 멀티스레드 코드를 짤 때 도움

---

## OPENRNDR 코딩 스타일: val/var 체크리스트

변수를 선언할 때마다 이렇게 물어보세요:

```
1. 이 변수가 프로그램 실행 중에 바뀔 필요가 있나?
   - 아니오 → val
   - 예 → 2번으로

2. 정확히 어디서 바뀌나?
   - extend 블록 안에서만 → var (프레임마다 리셋되거나 누적)
   - 특정 이벤트에서만 → var (상태 관리)
   - 항상 증가/감소 → var (시간, 카운터)

3. var 앞에 주석을 달아라
   - // 시간 변수
   - // 현재 선택된 팔레트
   - // 마우스 누른 상태
```

---

## 제너러티브 아트의 두 가지 철학

### 1. 재현 가능한 아트 (Reproducible)

```kotlin
randomSeed(12345L)
val seed = 12345L  // 재현에 필요한 정보
```

씨드를 저장하면, 나중에 같은 작품을 다시 생성할 수 있습니다.

### 2. 동적 아트 (Dynamic)

```kotlin
var time = 0.0
var mousePressed = false
```

시간, 마우스, 키보드 입력 같은 외부 요소에 반응합니다.

**OPENRNDR은 두 가지를 모두 지원합니다.** 그리고 `val`/`var` 구분이 이 두 세계를 명확하게 분리합니다.

---

## 다음 단계: 범위와 반복

다음 글에서는 **`for` 반복문과 범위(range)**를 다룹니다.

제너러티브 아트에서 반복은 매우 중요합니다:
- 격자 패턴 (이중 `for`)
- 방사형 배치 (각도 범위)
- 시뮬레이션 (조건부 반복)

---

## 연습 문제

### 1. `val`과 `var` 구분하기

아래 변수들을 보고, 각각이 `val`이어야 할지 `var`이어야 할지 판단하세요:

```kotlin
program {
    // ???
    val cols = 10
    
    // ???
    val rows = 10
    
    // ???
    var time = 0.0
    
    // ???
    val positions = mutableListOf<Vector2>()
    
    // ???
    var currentColor = ColorRGBa.WHITE
    
    extend {
        // ...
    }
}
```

**정답:**
- `cols` = `val` (화면 설정이 변하지 않음)
- `rows` = `val` (화면 설정이 변하지 않음)
- `time` = `var` (매 프레임 증가)
- `positions` = `val` (리스트 자체는 고정, 내용물은 변경)
- `currentColor` = `var` (사용자 입력에 따라 변함)

### 2. 재현성 테스트

```kotlin
fun main() = application {
    configure { width = 600; height = 600 }
    
    program {
        randomSeed(999L)
        val randomPositions = List(20) {
            Vector2(random(600.0), random(600.0))
        }
        
        extend {
            clear(ColorRGBa.BLACK)
            fill = ColorRGBa.WHITE
            for (pos in randomPositions) {
                circle(pos.x, pos.y, 10.0)
            }
        }
    }
}
```

이 코드를 실행한 후:
1. 프로그램을 여러 번 재시작해 보세요
2. 원들의 위치가 항상 같은지 확인하세요
3. `randomSeed` 값을 다른 숫자로 바꿔보세요

### 3. 입자 시스템 개선

앞의 "입자 시스템" 예제에서:
- 입자의 초기 속도를 더 크게 해보세요 (더 빠르게 흩어짐)
- 입자의 수명을 더 길게 해보세요
- 입자의 투명도 계산식을 바꿔서 다른 효과를 만들어보세요

---

**이전 글**: [OPENRNDR 시작하기 (2): 람다 표현식과 리시버](./openrndr_02_lambda_receiver.md)  
**다음 글**: OPENRNDR 시작하기 (4): 범위와 반복 — 루프 제어
