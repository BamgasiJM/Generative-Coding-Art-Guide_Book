# OPENRNDR 시작하기 (7): `when` — 코틀린의 강력한 조건 분기

조건에 따라 다른 코드를 실행해야 할 때가 있습니다.

**p5.js에서 이렇게 했을 거예요:**

```javascript
if (mode === 0) {
    drawGrid();
} else if (mode === 1) {
    drawSpiral();
} else if (mode === 2) {
    drawRadial();
} else {
    background(0);
}
```

**코틀린의 `when`은 훨씬 깔끔합니다:**

```kotlin
when (mode) {
    0 -> drawGrid()
    1 -> drawSpiral()
    2 -> drawRadial()
    else -> clear(ColorRGBa.BLACK)
}
```

그런데 `when`의 진짜 강력함은 여기서 끝나지 않습니다.

이 글에서는:
- **`if-else`보다 뛰어난 `when`의 이유**
- **범위, 타입, 복잡한 조건까지 다루는 패턴**
- **OPENRNDR에서 실제 사용되는 인터랙티브 패턴들**

을 다루겠습니다.

---

## 기본: `when`의 세 가지 형태

### 1. 단순 값 비교

```kotlin
val mode = 1

when (mode) {
    0 -> println("Grid pattern")
    1 -> println("Spiral pattern")
    2 -> println("Radial pattern")
    else -> println("Unknown pattern")
}
```

**`if-else` vs `when` 비교:**

```kotlin
// if-else (구식)
if (mode == 0) {
    println("Grid")
} else if (mode == 1) {
    println("Spiral")
} else if (mode == 2) {
    println("Radial")
} else {
    println("Unknown")
}

// when (현대식)
when (mode) {
    0 -> println("Grid")
    1 -> println("Spiral")
    2 -> println("Radial")
    else -> println("Unknown")
}
```

**`when`이 더 좋은 이유:**
- `mode`를 한 번만 평가 (효율적)
- 구조가 명확 (각 경우가 독립적)
- 모든 경우를 커버해야 함 (안전성)

### 2. 여러 값을 하나의 브랜치로

```kotlin
val key = 'A'

when (key) {
    'A', 'E', 'I', 'O', 'U' -> println("Vowel")
    else -> println("Consonant")
}
```

### 3. 범위 사용

```kotlin
val score = 85

when (score) {
    in 90..100 -> println("A")
    in 80..89 -> println("B")
    in 70..79 -> println("C")
    in 60..69 -> println("D")
    else -> println("F")
}
```

---

## OPENRNDR에서의 기본 패턴

### 패턴 1: 키보드 입력에 따른 패턴 선택

```kotlin
program {
    var selectedPattern = 0
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 숫자 키 입력 감지
        when {
            keyboard.pressedKeys.contains(KEY_1) -> selectedPattern = 0
            keyboard.pressedKeys.contains(KEY_2) -> selectedPattern = 1
            keyboard.pressedKeys.contains(KEY_3) -> selectedPattern = 2
        }
        
        // 선택된 패턴 그리기
        when (selectedPattern) {
            0 -> grid(10, 10, 80.0)
            1 -> radialLayout(12, 150.0)
            2 -> spiral(200, 1.0)
        }
    }
}
```

### 패턴 2: 마우스 위치에 따른 색상 변경

```kotlin
program {
    extend {
        clear(ColorRGBa.BLACK)
        
        val region = when {
            mouse.position.x < width / 3 -> "LEFT"
            mouse.position.x > width * 2 / 3 -> "RIGHT"
            else -> "CENTER"
        }
        
        fill = when (region) {
            "LEFT" -> ColorRGBa.RED
            "RIGHT" -> ColorRGBa.BLUE
            "CENTER" -> ColorRGBa.GREEN
            else -> ColorRGBa.WHITE
        }
        
        circle(mouse.position.x, mouse.position.y, 50.0)
    }
}
```

---

## p5.js와의 비교

### p5.js: if-else 체인

```javascript
function draw() {
    if (keyIsPressed) {
        if (key === '1') {
            drawGrid();
        } else if (key === '2') {
            drawSpiral();
        } else if (key === '3') {
            drawRadial();
        }
    }
    
    if (mouseX < width / 3) {
        fill(255, 0, 0);
    } else if (mouseX > width * 2 / 3) {
        fill(0, 0, 255);
    } else {
        fill(0, 255, 0);
    }
    
    circle(mouseX, mouseY, 50);
}
```

### OPENRNDR: 깔끔한 when

```kotlin
program {
    var selectedPattern = 0
    
    extend {
        clear(ColorRGBa.BLACK)
        
        when {
            keyboard.pressedKeys.contains(KEY_1) -> selectedPattern = 0
            keyboard.pressedKeys.contains(KEY_2) -> selectedPattern = 1
            keyboard.pressedKeys.contains(KEY_3) -> selectedPattern = 2
        }
        
        when (selectedPattern) {
            0 -> grid(10, 10, 80.0)
            1 -> radialLayout(12, 150.0)
            2 -> spiral(200, 1.0)
        }
        
        fill = when {
            mouse.position.x < width / 3 -> ColorRGBa.RED
            mouse.position.x > width * 2 / 3 -> ColorRGBa.BLUE
            else -> ColorRGBa.GREEN
        }
        
        circle(mouse.position.x, mouse.position.y, 50.0)
    }
}
```

---

## 고급: `when`의 강력한 기능들

### 1. 범위 조건

```kotlin
val mouseDistance = sqrt(
    (mouse.position.x - 400).pow(2) + 
    (mouse.position.y - 400).pow(2)
)

when {
    mouseDistance < 50 -> println("Very close")
    mouseDistance < 100 -> println("Close")
    mouseDistance < 200 -> println("Medium")
    else -> println("Far")
}
```

### 2. 타입 검사 (Smart Cast)

```kotlin
val data: Any = "Hello"

when (data) {
    is String -> println("Text: ${data.length} chars")
    is Int -> println("Number: $data")
    is List<*> -> println("List: ${data.size} items")
    else -> println("Unknown type")
}
```

### 3. 복합 조건

```kotlin
val x = 150.0
val y = 200.0

when {
    x < 200 && y < 200 -> println("Top-left")
    x > 200 && y < 200 -> println("Top-right")
    x < 200 && y > 200 -> println("Bottom-left")
    x > 200 && y > 200 -> println("Bottom-right")
    else -> println("Unknown")
}
```

### 4. `when`을 값으로 반환

```kotlin
val color = when {
    mouse.position.y < height / 3 -> ColorRGBa.RED
    mouse.position.y < height * 2 / 3 -> ColorRGBa.GREEN
    else -> ColorRGBa.BLUE
}

// color를 사용
fill = color
circle(mouse.position.x, mouse.position.y, 50.0)
```

---

## 실전 예제 1: 모드 선택 인터랙티브 아트

```kotlin
program {
    var mode = 0
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 키보드로 모드 변경
        when {
            keyboard.pressedKeys.contains(KEY_1) -> mode = 0
            keyboard.pressedKeys.contains(KEY_2) -> mode = 1
            keyboard.pressedKeys.contains(KEY_3) -> mode = 2
            keyboard.pressedKeys.contains(KEY_4) -> mode = 3
        }
        
        // 현재 모드의 색상 설정
        fill = when (mode) {
            0 -> ColorRGBa.RED
            1 -> ColorRGBa.GREEN
            2 -> ColorRGBa.BLUE
            3 -> ColorRGBa.YELLOW
            else -> ColorRGBa.WHITE
        }
        
        // 모드에 따른 패턴 그리기
        when (mode) {
            0 -> grid(12, 12, 60.0, 15.0)
            1 -> radialLayout(12, 150.0, 15.0)
            2 -> spiral(200, 1.0)
            3 -> {
                // 여러 패턴 조합
                fill = ColorRGBa.YELLOW
                grid(8, 8, 100.0, 10.0)
                fill = ColorRGBa.CYAN
                radialLayout(8, 120.0, 10.0)
            }
        }
        
        // 상태 표시
        fill = ColorRGBa.WHITE
        text("Mode: $mode (Press 1-4)", 20.0, 30.0)
    }
}
```

### 확장: 콘솔 피드백

```kotlin
when (mode) {
    0 -> {
        grid(12, 12, 60.0, 15.0)
        println("Grid pattern selected")
    }
    1 -> {
        radialLayout(12, 150.0, 15.0)
        println("Radial pattern selected")
    }
    2 -> {
        spiral(200, 1.0)
        println("Spiral pattern selected")
    }
}
```

---

## 실전 예제 2: 마우스 위치 기반 렌더링

```kotlin
program {
    extend {
        clear(ColorRGBa.BLACK)
        
        val quadrant = when {
            mouse.position.x < width / 2 && mouse.position.y < height / 2 -> 0
            mouse.position.x > width / 2 && mouse.position.y < height / 2 -> 1
            mouse.position.x < width / 2 && mouse.position.y > height / 2 -> 2
            else -> 3
        }
        
        when (quadrant) {
            0 -> {
                fill = ColorRGBa.RED
                grid(6, 6, 80.0)
            }
            1 -> {
                fill = ColorRGBa.GREEN
                radialLayout(8, 100.0)
            }
            2 -> {
                fill = ColorRGBa.BLUE
                spiral(100, 2.0)
            }
            3 -> {
                fill = ColorRGBa.YELLOW
                concentricCircles(5, 30.0, 30.0)
            }
        }
        
        // 경계선
        stroke = ColorRGBa.WHITE
        strokeWeight = 2.0
        line(width / 2, 0.0, width / 2, height.toDouble())
        line(0.0, height / 2, width.toDouble(), height / 2)
    }
}
```

---

## 실전 예제 3: 마우스 버튼과 키 조합

```kotlin
program {
    extend {
        clear(ColorRGBa.BLACK)
        
        // 버튼 상태에 따른 처리
        val action = when {
            mouse.buttonDown && keyboard.pressedKeys.contains(KEY_shift) -> "SCALE"
            mouse.buttonDown -> "DRAG"
            keyboard.pressedKeys.contains(KEY_shift) -> "ROTATE"
            else -> "IDLE"
        }
        
        fill = when (action) {
            "SCALE" -> ColorRGBa.RED
            "DRAG" -> ColorRGBa.GREEN
            "ROTATE" -> ColorRGBa.BLUE
            else -> ColorRGBa.GRAY
        }
        
        // 현재 액션에 따른 시각적 피드백
        when (action) {
            "SCALE" -> {
                circle(mouse.position.x, mouse.position.y, 100.0)
            }
            "DRAG" -> {
                circle(mouse.position.x, mouse.position.y, 50.0)
            }
            "ROTATE" -> {
                rectangle(
                    mouse.position.x - 30,
                    mouse.position.y - 30,
                    60.0,
                    60.0
                )
            }
            else -> {
                // 아무것도 하지 않음
            }
        }
        
        // 상태 표시
        fill = ColorRGBa.WHITE
        text("Action: $action", 20.0, 30.0)
    }
}
```

---

## 실전 예제 4: 파라메트릭 선택

```kotlin
data class Pattern(
    val name: String,
    val draw: Drawer.() -> Unit
)

program {
    var selectedIndex = 0
    
    val patterns = listOf(
        Pattern("Grid") { grid(10, 10, 80.0) },
        Pattern("Radial") { radialLayout(12, 150.0) },
        Pattern("Spiral") { spiral(200, 1.0) },
        Pattern("Circles") { concentricCircles(6, 20.0, 30.0) }
    )
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 상/하 키로 패턴 변경
        when {
            keyboard.pressedKeys.contains(KEY_up) -> {
                selectedIndex = (selectedIndex - 1 + patterns.size) % patterns.size
            }
            keyboard.pressedKeys.contains(KEY_down) -> {
                selectedIndex = (selectedIndex + 1) % patterns.size
            }
        }
        
        fill = ColorRGBa.WHITE
        
        // 선택된 패턴 실행
        patterns[selectedIndex].draw()
        
        // 패턴 이름 표시
        fill = ColorRGBa.YELLOW
        text("Pattern: ${patterns[selectedIndex].name}", 20.0, 30.0)
    }
}
```

---

## 실전 예제 5: 애니메이션 상태 관리

```kotlin
enum class AnimationState {
    IDLE,
    EXPANDING,
    CONTRACTING,
    ROTATING
}

program {
    var state = AnimationState.IDLE
    var time = 0.0
    
    extend {
        clear(ColorRGBa.BLACK)
        
        // 사용자 입력에 따라 상태 변경
        when {
            keyboard.pressedKeys.contains(KEY_1) -> state = AnimationState.EXPANDING
            keyboard.pressedKeys.contains(KEY_2) -> state = AnimationState.CONTRACTING
            keyboard.pressedKeys.contains(KEY_3) -> state = AnimationState.ROTATING
            keyboard.pressedKeys.contains(KEY_0) -> state = AnimationState.IDLE
        }
        
        // 상태에 따른 애니메이션
        fill = ColorRGBa.WHITE
        
        val size = when (state) {
            AnimationState.IDLE -> 50.0
            AnimationState.EXPANDING -> 50.0 + 30.0 * sin(time)
            AnimationState.CONTRACTING -> 50.0 - 30.0 * sin(time)
            AnimationState.ROTATING -> {
                // 회전은 다르게 처리
                50.0
            }
        }
        
        val rotation = when (state) {
            AnimationState.ROTATING -> time
            else -> 0.0
        }
        
        // 도형 그리기
        when (state) {
            AnimationState.ROTATING -> {
                // 회전하는 사각형
                push()
                translate(400.0, 400.0)
                rotate(rotation)
                rectangle(-25.0, -25.0, 50.0, 50.0)
                pop()
            }
            else -> {
                // 크기가 변하는 원
                circle(400.0, 400.0, size)
            }
        }
        
        // 상태 표시
        fill = ColorRGBa.YELLOW
        text("State: $state", 20.0, 30.0)
        
        time += 0.03
    }
}
```

---

## 실전 예제 6: 데이터 타입에 따른 처리

```kotlin
sealed class DrawCommand {
    data class DrawCircle(val x: Double, val y: Double, val radius: Double) : DrawCommand()
    data class DrawSquare(val x: Double, val y: Double, val size: Double) : DrawCommand()
    data class DrawLine(val x1: Double, val y1: Double, val x2: Double, val y2: Double) : DrawCommand()
}

program {
    val commands = listOf(
        DrawCommand.DrawCircle(100.0, 100.0, 30.0),
        DrawCommand.DrawSquare(300.0, 100.0, 50.0),
        DrawCommand.DrawLine(100.0, 300.0, 500.0, 300.0)
    )
    
    extend {
        clear(ColorRGBa.BLACK)
        fill = ColorRGBa.WHITE
        stroke = ColorRGBa.CYAN
        strokeWeight = 2.0
        
        for (cmd in commands) {
            when (cmd) {
                is DrawCommand.DrawCircle -> {
                    circle(cmd.x, cmd.y, cmd.radius)
                }
                is DrawCommand.DrawSquare -> {
                    rectangle(cmd.x - cmd.size / 2, cmd.y - cmd.size / 2, cmd.size, cmd.size)
                }
                is DrawCommand.DrawLine -> {
                    lineSegment(cmd.x1, cmd.y1, cmd.x2, cmd.y2)
                }
            }
        }
    }
}
```

---

## `when`과 `else`의 안전성

**중요: `else`를 항상 포함하세요!**

```kotlin
// 위험: 모든 경우를 다루지 않음
val result = when (x) {
    1 -> "ONE"
    2 -> "TWO"
    // 3 이상은 어떻게?
}

// 안전: 모든 경우 다룸
val result = when (x) {
    1 -> "ONE"
    2 -> "TWO"
    else -> "OTHER"
}
```

**컴파일러가 경고를 줍니다:**
```
'when' expression on type 'Int' 
has uncovered case: else is required
```

---

## `when`의 성능

```kotlin
// 효율적: 각 조건을 한 번만 평가
val size = when (mode) {
    0 -> 50.0
    1 -> 100.0
    else -> 75.0
}

// 비효율적: if-else는 순차적으로 평가
val size = if (mode == 0) {
    50.0
} else if (mode == 1) {
    100.0
} else {
    75.0
}
```

**정수/열거값 비교에서는 `when`이 더 빠릅니다.**

---

## 정리: `when` vs `if-else`

| 기준 | `when` | `if-else` |
|------|--------|----------|
| **가독성** | 명확한 구조 | 여러 조건 시 복잡 |
| **안전성** | else 필수 | 실수하기 쉬움 |
| **성능** | 더 빠름 (정수 비교) | 순차 평가 |
| **표현력** | 범위, 타입 등 지원 | 기본 조건만 |
| **코드 길이** | 짧음 | 길어질 수 있음 |

**결론: OPENRNDR에서는 `when`을 사용하세요.**

---

## 다음 단계: Null 안전성

다음 글에서는 **Null 처리**를 다룹니다.

코틀린의 가장 강력한 기능 중 하나: **null 안전성**

```kotlin
val result: String? = "Hello"  // null일 수 있음

// 안전한 접근
val length = result?.length  // null이면 null 반환
val length2 = result?.length ?: 0  // null이면 기본값 0

// when으로도 처리
when (result) {
    null -> println("No data")
    else -> println(result.length)
}
```

제너러티브 아트에서 마우스 입력, 센서 데이터 등은 항상 null일 가능성이 있습니다. 안전한 처리가 필수입니다.

---

## 연습 문제

### 1. 기본 `when`

```kotlin
val day = 3

val dayName = when (day) {
    1 -> "Monday"
    2 -> "Tuesday"
    3 -> "Wednesday"
    4 -> "Thursday"
    5 -> "Friday"
    6 -> "Saturday"
    7 -> "Sunday"
    else -> "Invalid day"
}

println(dayName)  // "Wednesday"
```

### 2. 범위 조건

```kotlin
val score = 85

val grade = when (score) {
    in 90..100 -> "A"
    in 80..89 -> "B"
    in 70..79 -> "C"
    in 60..69 -> "D"
    else -> "F"
}

println(grade)  // "B"
```

### 3. OPENRNDR: 키보드 제어

```kotlin
program {
    var color = ColorRGBa.WHITE
    
    extend {
        clear(ColorRGBa.BLACK)
        
        when {
            keyboard.pressedKeys.contains(KEY_r) -> color = ColorRGBa.RED
            keyboard.pressedKeys.contains(KEY_g) -> color = ColorRGBa.GREEN
            keyboard.pressedKeys.contains(KEY_b) -> color = ColorRGBa.BLUE
        }
        
        fill = color
        circle(400.0, 400.0, 100.0)
    }
}
```

실행해 본 후:
- 색상을 더 추가해 보세요
- 도형도 변하게 해보세요

### 4. 마우스 영역 감지

```kotlin
program {
    extend {
        clear(ColorRGBa.BLACK)
        
        val region = when {
            mouse.position.x < width / 3 -> "LEFT"
            mouse.position.x > width * 2 / 3 -> "RIGHT"
            else -> "CENTER"
        }
        
        fill = when (region) {
            "LEFT" -> ColorRGBa.RED
            "CENTER" -> ColorRGBa.GREEN
            "RIGHT" -> ColorRGBa.BLUE
            else -> ColorRGBa.WHITE
        }
        
        circle(mouse.position.x, mouse.position.y, 50.0)
    }
}
```

### 5. 복합 패턴 (심화)

지난 예제들의 Extension 함수들을 조합해서:
- 키보드로 4가지 패턴 선택
- 마우스 위치에 따라 색상 변경
- 마우스 클릭으로 애니메이션 토글

---

**이전 글**: [OPENRNDR 시작하기 (6): Extension 함수 — OPENRNDR 스타일의 핵심](./openrndr_06_extension_functions.md)  
**다음 글**: OPENRNDR 시작하기 (8): Null 안전성 — `?`, `?.`, `?:`, `!!`
