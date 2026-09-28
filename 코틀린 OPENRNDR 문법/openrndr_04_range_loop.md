# OPENRNDR 시작하기 (4): 범위와 반복 — 루프 제어

제너러티브 아트의 기본은 **반복(repetition)**입니다.

격자 패턴, 방사형 배치, 선의 배열... 모두 어떤 형태의 반복입니다.

p5.js를 배웠다면 이미 `for` 반복문을 알고 있어요:

```javascript
for (let i = 0; i < 10; i++) {
    circle(i * 50, 200, 20);
}
```

**코틀린의 `for` 루프는 훨씬 더 강력하고 간결합니다.**

이 글에서는:
- 코틀린의 범위(range) 문법
- 다양한 `for` 반복 패턴
- OPENRNDR에서 실제로 자주 쓰는 반복 패턴들

을 다루겠습니다.

---

## 기본: 범위(Range)란?

코틀린의 **범위**는 "시작부터 끝까지의 연속된 값"을 나타냅니다.

```kotlin
val range = 1..10  // 1부터 10까지 (양 끝 포함)
```

이 `range`는 `1, 2, 3, 4, 5, 6, 7, 8, 9, 10`을 나타냅니다.

범위 문법을 몇 가지 보겠습니다:

```kotlin
// 양 끝 포함 (inclusive)
val a = 1..10     // 1, 2, 3, ... 10

// 끝 포함 안 함 (exclusive)
val b = 1 until 10  // 1, 2, 3, ... 9 (10은 제외)

// 역순 (descending)
val c = 10 downTo 1  // 10, 9, 8, ... 1

// 간격 지정
val d = 1..10 step 2  // 1, 3, 5, 7, 9
val e = 10 downTo 1 step 3  // 10, 7, 4, 1

// 부동소수점도 가능 (하지만 조심해야 함)
val f = 0.0..1.0 step 0.1  // 0.0, 0.1, 0.2, ...
```

---

## p5.js와의 비교

### p5.js: C 스타일 for 루프

```javascript
for (let i = 0; i < 10; i++) {
    circle(i * 50, 200, 20);
}
```

루프 변수, 조건, 증분을 명시적으로 써야 합니다.

### 코틀린: 범위 기반 for 루프

```kotlin
for (i in 0 until 10) {
    circle(i * 50.0, 200.0, 20.0)
}
```

훨씬 간결하고 명확합니다. **범위 자체에 어디까지 갈지가 명시되어 있으니까요.**

---

## OPENRNDR에서의 기본 반복 패턴

### 패턴 1: 수평선 그리기

여러 개의 원을 가로로 배열:

```kotlin
extend {
    clear(ColorRGBa.BLACK)
    
    for (i in 0 until 10) {
        val x = 50.0 + i * 70.0
        circle(x, 400.0, 20.0)
    }
}
```

**범위 선택:**
- `0 until 10` — 0부터 9까지 (인덱스 용도이므로 `until` 사용)

### 패턴 2: 격자 패턴 (이중 for)

세로와 가로를 동시에 제어:

```kotlin
extend {
    clear(ColorRGBa.BLACK)
    
    for (x in 0 until 8) {
        for (y in 0 until 8) {
            val px = 50.0 + x * 100.0
            val py = 50.0 + y * 100.0
            circle(px, py, 30.0)
        }
    }
}
```

**구조:**
- 바깥쪽 `for` (x): 가로 반복
- 안쪽 `for` (y): 세로 반복
- 결과: 8×8 = 64개의 원

### 패턴 3: 방사형 배치

중심 주위에 원들을 원형으로 배치:

```kotlin
extend {
    clear(ColorRGBa.BLACK)
    
    val count = 12  // 12개의 원
    for (i in 0 until count) {
        val angle = TWO_PI / count * i
        val x = 400.0 + 150.0 * cos(angle)
        val y = 400.0 + 150.0 * sin(angle)
        circle(x, y, 20.0)
    }
}
```

**패턴 분석:**
- `for (i in 0 until count)` — 0부터 11까지
- `angle = TWO_PI / count * i` — 각 원이 균등한 각도로 분포
- 삼각함수로 원 위의 좌표 계산

---

## 범위의 실용적 사용법

### 1. `until` vs `..` 선택

언제 무엇을 쓸까요?

```kotlin
// 인덱스로 쓸 때: until 사용 (배열/리스트)
for (i in 0 until list.size) {
    println(list[i])
}

// "~부터 ~까지"의 포함적 범위: .. 사용
for (x in 1..100) {
    total += x  // 1 + 2 + 3 + ... + 100
}

// OPENRNDR에서의 실제 사용
val width = 800
val cols = 10
for (x in 0 until cols) {
    val px = x * (width / cols)  // 0 until는 배열 인덱싱 스타일
}
```

**규칙:**
- 배열/리스트 접근: `until`
- 수학적 범위: `..`

### 2. `step` 으로 간격 조절

```kotlin
// 2칸씩 점프
for (i in 0 until 20 step 2) {
    println(i)  // 0, 2, 4, 6, 8, 10, 12, 14, 16, 18
}

// OPENRNDR에서 대각선 점 찍기
for (i in 0 until 800 step 50) {
    circle(i.toDouble(), i.toDouble(), 5.0)
}
```

### 3. `downTo` 역순

```kotlin
// 역순으로 반복
for (i in 10 downTo 1) {
    println(i)  // 10, 9, 8, ..., 1
}

// OPENRNDR에서 순서를 뒤집어 그리기
for (i in 100 downTo 1) {
    val radius = i.toDouble() / 2.0
    circle(400.0, 400.0, radius)  // 큰 원부터 작은 원
}
```

---

## 부동소수점 범위의 함정

부동소수점으로 범위를 만들 때 조심하세요:

```kotlin
// 위험: 부동소수점 오차 누적
for (i in 0.0..1.0 step 0.1) {
    println(i)  // 0.0, 0.1, 0.2, ..., 0.9, 1.0?
}
// 결과가 1.0을 포함하지 않을 수도 있음 (부동소수점 오차)
```

**더 안전한 방법:**

```kotlin
// 정수 범위 → 부동소수점 변환
for (i in 0..10) {
    val x = i * 0.1
    println(x)  // 0.0, 0.1, 0.2, ..., 1.0
}
```

**OPENRNDR에서의 실제 사용:**

```kotlin
extend {
    clear(ColorRGBa.BLACK)
    
    // 각도 범위는 정수로 다룬 후 변환
    for (i in 0 until 360 step 10) {
        val angle = i.toDouble() * PI / 180.0  // 도(degree)를 라디안으로
        val x = 400.0 + 100.0 * cos(angle)
        val y = 400.0 + 100.0 * sin(angle)
        circle(x, y, 5.0)
    }
}
```

---

## 시간과 반복: 애니메이션 만들기

반복과 시간을 결합하면 애니메이션이 탄생합니다.

### 예제 1: 회전하는 격자

```kotlin
program {
    var rotation = 0.0
    
    extend {
        clear(ColorRGBa.BLACK)
        
        val cols = 6
        val rows = 6
        val cellWidth = width / cols
        val cellHeight = height / rows
        
        for (x in 0 until cols) {
            for (y in 0 until rows) {
                val px = x * cellWidth + cellWidth / 2
                val py = y * cellHeight + cellHeight / 2
                
                // 각 셀마다 다른 회전 속도
                val angle = rotation + x + y
                val distance = 30.0 + 10.0 * sin(angle)
                
                circle(px, py, distance)
            }
        }
        
        rotation += 0.05
    }
}
```

**로직:**
- `for` 반복으로 격자 순회
- 각 셀의 `angle`이 다름 (`x + y` 포함)
- 파도 효과 생성

### 예제 2: 확산 효과

```kotlin
program {
    var time = 0.0
    
    extend {
        clear(ColorRGBa.BLACK)
        
        val ringCount = 5
        for (ring in 0 until ringCount) {
            val radius = 30.0 + ring * 40.0 + 20.0 * sin(time - ring * 0.5)
            val count = 8 + ring * 4
            
            for (i in 0 until count) {
                val angle = TWO_PI / count * i
                val x = 400.0 + radius * cos(angle)
                val y = 400.0 + radius * sin(angle)
                circle(x, y, 5.0)
            }
        }
        
        time += 0.03
    }
}
```

**로직:**
- 바깥쪽 `for (ring)` — 여러 개의 동심원(concentric circles)
- 안쪽 `for (i)` — 각 동심원 위의 점들
- 각 동심원의 반지름이 `time`에 따라 변함
- 결과: 맥동하는 방사형 패턴

---

## 고급: `forEach`와 함수형 스타일

지금까지 본 `for` 루프는 **명령형(imperative)** 스타일입니다.

코틀린은 **함수형(functional)** 스타일도 지원합니다:

```kotlin
// 명령형: for 반복
for (i in 0 until 10) {
    circle(i * 50.0, 200.0, 20.0)
}

// 함수형: forEach
(0 until 10).forEach { i ->
    circle(i * 50.0, 200.0, 20.0)
}
```

**두 형태는 같은 결과를 만듭니다.** 

**코틀린에서는 `for`를 더 권장합니다:**
- 읽기 쉬움
- 루프 제어가 명시적 (break, continue 가능)

함수형은 다음 글(컬렉션)에서 자세히 다루겠습니다.

---

## 루프 제어: `break`와 `continue`

때로는 루프를 도중에 멈추거나 건너뛸 필요가 있습니다.

### `continue` — 현재 반복만 건너뛰기

```kotlin
for (i in 0 until 10) {
    if (i == 5) continue  // i가 5일 때 건너뜀
    
    circle(i * 50.0, 200.0, 20.0)
}
// 결과: 0, 1, 2, 3, 4, 6, 7, 8, 9 위치에만 원이 나타남
```

### `break` — 루프 탈출

```kotlin
for (i in 0 until 10) {
    if (i == 5) break  // i가 5일 때 루프 종료
    
    circle(i * 50.0, 200.0, 20.0)
}
// 결과: 0, 1, 2, 3, 4 위치에만 원이 나타남
```

**OPENRNDR에서의 실제 사용:**

```kotlin
extend {
    clear(ColorRGBa.BLACK)
    
    for (x in 0 until 20) {
        for (y in 0 until 20) {
            // 특정 조건을 만족하지 않으면 건너뜀
            if ((x + y) % 2 == 0) continue
            
            circle(x * 40.0, y * 40.0, 15.0)
        }
    }
}
```

이 코드는 **체스판 패턴**을 만듭니다.

---

## 실전 예제: 별 필드 (Starfield)

유명한 제너러티브 아트 패턴입니다.

```kotlin
program {
    var time = 0.0
    
    extend {
        clear(ColorRGBa.BLACK)
        fill = ColorRGBa.WHITE
        
        val starCount = 200
        for (i in 0 until starCount) {
            // 각 별마다 고유한 위치와 속도 (씨드로 결정됨)
            randomSeed((i * 12345L) % Long.MAX_VALUE)
            val startX = random(width)
            val startY = random(height)
            val speed = random(2.0, 5.0)
            
            // 시간에 따른 별의 위치
            val x = startX + speed * time
            val y = startY
            
            // 화면 경계를 넘으면 반대편에서 나타남 (토러스 래핑)
            val wrappedX = (x % width + width) % width
            
            circle(wrappedX, y, 2.0)
        }
        
        time += 1.0
    }
}
```

**패턴 분석:**
- `for (i in 0 until starCount)` — 200개의 별 반복
- 각 별이 고유한 위치와 속도를 가짐 (씨드로 결정)
- 시간에 따라 오른쪽으로 이동
- 모듈러(%) 연산으로 토러스 래핑

---

## 실전 예제: 나선(Spiral) 패턴

```kotlin
program {
    var time = 0.0
    
    extend {
        clear(ColorRGBa.BLACK)
        
        val arms = 3  // 나선 팔의 개수
        val pointsPerArm = 50
        
        for (arm in 0 until arms) {
            for (step in 0 until pointsPerArm) {
                // 각도: 팔마다 다름
                val baseAngle = TWO_PI / arms * arm
                val angle = baseAngle + step * 0.3 + time * 0.02
                
                // 반지름: 스텝에 따라 증가
                val radius = step * 3.0
                
                val x = 400.0 + radius * cos(angle)
                val y = 400.0 + radius * sin(angle)
                
                circle(x, y, 3.0)
            }
        }
        
        time += 1.0
    }
}
```

**결과:**
- 3개의 팔이 중심에서 방사형으로 뻗어나감
- 시간에 따라 회전
- 고전적인 나선 효과

---

## 범위 타입과 성능

거대한 범위를 만들 때 주의하세요:

```kotlin
// OK: 범위는 메모리를 많이 쓰지 않음
for (i in 0 until 1_000_000) {
    // 작업
}

// 주의: 리스트는 메모리를 많이 씀
val list = (0 until 1_000_000).toList()  // 1백만 개 요소의 리스트 생성
for (i in list) {
    // 작업
}
```

**OPENRNDR에서는 범위를 직접 쓰세요.**

```kotlin
// 좋음
for (i in 0 until 10000) {
    circle(...)
}

// 피함
val indices = (0 until 10000).toList()
for (i in indices) {
    circle(...)
}
```

---

## 체크리스트: 반복문 선택

반복을 작성할 때 이렇게 물어보세요:

```
1. 얼마나 많이 반복해야 하나?
   - 고정 숫자 → for + 범위
   - 리스트의 요소들 → for + in
   - 조건까지만 → while

2. 범위는 어떻게 표현할까?
   - 배열 인덱싱 → 0 until size
   - 포함적 범위 → 1..100
   - 역순 → downTo
   - 간격 → step

3. 중첩 반복이 필요한가?
   - 격자: 이중 for
   - 방사형: 바깥쪽은 각도, 안쪽은 거리
   - 계층 구조: 바깥쪽은 그룹, 안쪽은 요소
```

---

## p5.js와의 성능 비교

### p5.js: 100개 원 그리기

```javascript
for (let i = 0; i < 10; i++) {
    for (let j = 0; j < 10; j++) {
        circle(i * 50, j * 50, 20);
    }
}
```

### OPENRNDR: 100개 원 그리기

```kotlin
for (i in 0 until 10) {
    for (j in 0 until 10) {
        circle(i * 50.0, j * 50.0, 20.0)
    }
}
```

**성능:**
- OPENRNDR이 훨씬 빠릅니다 (p5.js는 JavaScript, OPENRNDR은 JVM 기반)
- p5.js: ~60fps (최대)
- OPENRNDR: 수천 개 도형도 60fps (GPU 가속)

---

## 정리: 범위와 반복의 패턴

| 패턴 | 범위 | 용도 |
|------|------|------|
| 선형 배치 | `0 until count` | 가로/세로 나열 |
| 격자 | `0 until cols`, `0 until rows` | 행렬 패턴 |
| 방사형 | `0 until count` | 원형 배치 |
| 역순 | `max downTo 0` | 큰 것부터 그리기 |
| 간격 | `step` | 일부만 건너뛰기 |
| 토러스 | `% width` | 경계 래핑 |

---

## 다음 단계: 컬렉션과 함수형 파이프라인

다음 글에서는 **컬렉션 (`List`, `Set`, `Map`)과 함수형 메서드** (`map`, `filter`, `forEach`)를 다룹니다.

이들은 반복보다 더 선언적이고, 코드를 훨씬 깔끔하게 만듭니다:

```kotlin
// for 반복 vs 함수형
val circles = (0 until 100)
    .map { i -> Vector2(i * 10.0, random(height)) }
    .filter { it.x < 500.0 }
    .forEach { circle(it.x, it.y, 5.0) }
```

---

## 연습 문제

### 1. 범위 실습

다음 범위들을 각각 작성하고 무엇을 생성하는지 확인하세요:

```kotlin
(0 until 5)         // 0, 1, 2, 3, 4
(1..5)              // 1, 2, 3, 4, 5
(5 downTo 1)        // 5, 4, 3, 2, 1
(0 until 10 step 2) // 0, 2, 4, 6, 8
```

### 2. 격자 패턴

```kotlin
extend {
    clear(ColorRGBa.BLACK)
    
    for (x in 0 until 10) {
        for (y in 0 until 10) {
            circle(x * 80.0, y * 80.0, 20.0)
        }
    }
}
```

실행해 본 후, 다음을 시도하세요:
- 원의 개수를 20×20으로 늘려보세요
- 체스판 패턴으로 만들어보세요 (`continue` 사용)
- 대각선 패턴으로 만들어보세요

### 3. 방사형 배치

```kotlin
extend {
    clear(ColorRGBa.BLACK)
    
    val count = 8
    for (i in 0 until count) {
        val angle = TWO_PI / count * i
        val x = 400.0 + 150.0 * cos(angle)
        val y = 400.0 + 150.0 * sin(angle)
        circle(x, y, 30.0)
    }
}
```

실행해 본 후:
- 원의 개수를 12, 16, 24로 늘려보세요
- 거리를 시간에 따라 변하게 해보세요

### 4. 나선 (심화)

```kotlin
program {
    var t = 0.0
    
    extend {
        clear(ColorRGBa.BLACK)
        
        for (i in 0 until 200) {
            val angle = t + i * 0.1
            val radius = i * 1.5
            val x = 400.0 + radius * cos(angle)
            val y = 400.0 + radius * sin(angle)
            circle(x, y, 2.0)
        }
        
        t += 0.02
    }
}
```

실행해 본 후:
- 나선의 팔 개수를 여러 개로 만들어보세요
- 포인트의 크기를 거리에 따라 변하게 해보세요

---

**이전 글**: [OPENRNDR 시작하기 (3): `val`과 `var` — 불변성의 설계](./openrndr_03_val_var.md)  
**다음 글**: OPENRNDR 시작하기 (5): 컬렉션과 함수형 파이프라인
