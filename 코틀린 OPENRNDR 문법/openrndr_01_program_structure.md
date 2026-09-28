# OPENRNDR 시작하기 (1): 프로그램 구조 — `fun main`과 `extend`

p5.js에서 제너러티브 아트를 시작했다면, OPENRNDR의 기본 구조는 낯설지 않을 거예요. 하지만 코틀린의 문법과 OPENRNDR의 독특한 DSL 방식이 처음엔 헷갈릴 수 있어요. 이 글에서는 "모든 코드가 어디서 시작하고, 어떻게 흐르는가"를 명확히 짚어주려고 합니다.

---

## p5.js와의 비교: 개념적 유사성

먼저 p5.js로 생각해볼게요.

```javascript
function setup() {
  createCanvas(800, 800);
}

function draw() {
  background(0);
  circle(400, 400, 100);
}
```

p5.js는:
- `setup()`: 한 번 실행 (캔버스 초기화)
- `draw()`: 매 프레임 반복 실행

**OPENRNDR도 똑같은 구조를 가져요.** 단지 문법이 다를 뿐입니다.

---

## OPENRNDR의 기본 구조

```kotlin
fun main() = application {
    configure {
        width = 800
        height = 800
    }
    program {
        extend {
            drawer.clear(ColorRGBa.BLACK)
            drawer.circle(400.0, 400.0, 100.0)
        }
    }
}
```

이 코드를 세 부분으로 나눠서 봅시다.

### 1단계: `fun main()` — 진입점

```kotlin
fun main() = application { ... }
```

모든 코틀린 프로그램은 `main()`에서 시작합니다. p5.js처럼 자동으로 `setup()`과 `draw()`가 호출되는 게 아니라, **명시적으로 `application`이라는 DSL 함수를 호출해서 앱을 시작**합니다.

코틀린 초보자들이 헷갈리는 부분:
- `fun main() =` — 한 줄로 함수를 정의하고 반환값을 지정
- 정확히는 `fun main() { return application { ... } }`과 동일

### 2단계: `configure { }` — 캔버스 설정 (setup처럼)

```kotlin
configure {
    width = 800
    height = 800
}
```

p5.js의 `setup()`과 비슷해요. **프로그램이 시작될 때 한 번만 실행**됩니다.

여기서 할 수 있는 것:
- 캔버스 크기 설정
- 창 위치, 제목 설정
- 프레임레이트 설정
- 기타 초기 설정

예제:
```kotlin
configure {
    width = 1024
    height = 768
    title = "Generative Art Study"
    windowResizable = false
}
```

### 3단계: `extend { }` — 매 프레임 실행 루프 (draw처럼)

```kotlin
extend {
    drawer.clear(ColorRGBa.BLACK)
    drawer.circle(400.0, 400.0, 100.0)
}
```

여기가 **가장 중요한 블록**입니다. `extend { }` 안의 코드는 **매 프레임 반복 실행**됩니다.

p5.js의 `draw()` 블록과 정확히 같은 역할을 해요.

---

## 코드 배치의 논리

이제 이런 질문이 나올 거예요: **"왜 이렇게 중첩되어 있어?"**

```kotlin
fun main() = application {          // 앱 시작
    configure { ... }               // 초기화 (한 번)
    program {                       // 프로그램 본체
        extend { ... }              // 루프 (매 프레임)
    }
}
```

이건 OPENRNDR의 **DSL 설계** 때문입니다. 각 블록이 명확한 **생명주기(lifecycle)**를 가져요:

1. `application` — 전체 앱 리소스 관리
2. `configure` — 화면/시스템 설정
3. `program` — 프로그램 본체 (1회만 호출)
4. `extend` — 프레임마다 실행 (렌더링 루프)

---

## 첫 번째 작품: 간단한 원 그리기

이제 실제 코드를 보겠습니다.

```kotlin
fun main() = application {
    configure {
        width = 800
        height = 800
        title = "My First OPENRNDR"
    }
    
    program {
        extend {
            // 매 프레임 실행
            drawer.clear(ColorRGBa.BLACK)
            drawer.circle(400.0, 400.0, 100.0)
        }
    }
}
```

**실행하면:**
- 800×800 검은색 창이 뜸
- 화면 중앙에 흰색 원이 보임 (기본 색상은 흰색)

p5.js와 차이점:
- `createCanvas()` 대신 `width`, `height`를 프로퍼티로 설정
- `background()` 대신 `drawer.clear()`
- `circle()` 대신 `drawer.circle()`

---

## 상태 관리: 시간 변수 추가하기

제너러티브 아트에서는 **시간에 따라 변하는 상태**가 필수예요. p5.js처럼 프레임 카운터나 시간 변수를 써봅시다.

```kotlin
fun main() = application {
    configure {
        width = 800
        height = 800
    }
    
    program {
        var time = 0.0  // 시간 변수
        
        extend {
            drawer.clear(ColorRGBa.BLACK)
            
            // 시간에 따라 원의 크기가 변함
            val radius = 50.0 + 50.0 * sin(time)
            drawer.circle(400.0, 400.0, radius)
            
            time += 0.01
        }
    }
}
```

**여기서 중요한 점:**
- `var time = 0.0`은 `program` 블록 안, `extend` 블록 **바깥**에 있습니다.
- 이렇게 하면 `extend`가 매 프레임 실행될 때마다 같은 `time` 변수에 접근할 수 있어요.
- p5.js에서 `frameCount`를 쓸 때와 같은 원리입니다.

**결과:**
- 원의 크기가 50부터 100까지 반복적으로 커졌다 작아졌다 함

---

## 제너러티브 아트 관점: 재현성(Reproducibility) 설계

제너러티브 아트에서 중요한 개념: **같은 씨드(seed)로 같은 결과를 만들기**.

```kotlin
fun main() = application {
    configure {
        width = 800
        height = 800
    }
    
    program {
        randomSeed(42L)  // 고정 씨드
        var time = 0.0
        
        extend {
            drawer.clear(ColorRGBa.BLACK)
            
            // 같은 씨드면 항상 같은 무늬가 나옴
            val x = 200.0 + random(400.0)
            val y = 200.0 + random(400.0)
            drawer.circle(x, y, 30.0)
            
            time += 0.01
        }
    }
}
```

여기서:
- `randomSeed(42L)` — 난수 생성기를 초기화 (p5.js의 `randomSeed()`와 동일)
- 같은 씨드를 쓰면 "실행할 때마다 같은 위치에 원이 나타남"
- 이건 작품을 저장하고 재현하는 데 필수적입니다

---

## OPENRNDR의 강점: `drawer` 객체

p5.js와의 또 다른 중요한 차이: **모든 그리기가 `drawer` 객체를 거칩니다.**

```kotlin
extend {
    drawer.clear(ColorRGBa.BLACK)
    drawer.fill = ColorRGBa.PINK          // 채우기 색상 설정
    drawer.stroke = ColorRGBa.CYAN        // 선 색상 설정
    drawer.strokeWeight = 2.0
    
    drawer.circle(100.0, 100.0, 50.0)
    drawer.rectangle(300.0, 300.0, 100.0, 100.0)
}
```

`drawer`를 통해서만 화면에 그려진다는 건 **"상태 관리가 명확"**하다는 뜻입니다.

p5.js는:
```javascript
fill(255, 0, 255);
stroke(0, 255, 255);
strokeWeight(2);
circle(100, 100, 50);
```

OPENRNDR은:
```kotlin
drawer.fill = ColorRGBa.PINK
drawer.stroke = ColorRGBa.CYAN
drawer.strokeWeight = 2.0
drawer.circle(100.0, 100.0, 50.0)
```

**좀 더 verbose하지만, 훨씬 명확합니다.** 어느 것이 그리기 명령이고, 어느 것이 상태 설정인지 한눈에 보여요.

---

## 여러 도형 그리기

실제 제너러티브 아트 작품은 보통 한 두 개 도형이 아니라 많은 요소들의 조합입니다.

```kotlin
fun main() = application {
    configure {
        width = 800
        height = 800
    }
    
    program {
        var t = 0.0
        
        extend {
            drawer.clear(ColorRGBa.BLACK)
            
            // 격자 패턴 그리기
            for (x in 0..7) {
                for (y in 0..7) {
                    val px = 50.0 + x * 100.0
                    val py = 50.0 + y * 100.0
                    
                    // 시간에 따라 크기가 다르게 변함
                    val size = 30.0 + 20.0 * sin(t + x + y)
                    
                    drawer.fill = ColorRGBa.WHITE
                    drawer.circle(px, py, size)
                }
            }
            
            t += 0.05
        }
    }
}
```

**결과:**
- 8×8 격자로 원들이 배치됨
- 각 원의 크기가 위상 차이(`x + y`)를 가지고 파도처럼 움직임

이건 제너러티브 아트의 전형적인 패턴입니다.

---

## 한발 더: 마우스 인터랙션

제너러티브 아트에서 자주 쓰이는 패턴: **마우스 움직임에 반응하기**.

```kotlin
fun main() = application {
    configure {
        width = 800
        height = 800
    }
    
    program {
        extend {
            drawer.clear(ColorRGBa.BLACK)
            
            // 마우스 위치를 중심으로 원 그리기
            val mx = mouse.position.x
            val my = mouse.position.y
            
            drawer.circle(mx, my, 50.0)
        }
    }
}
```

OPENRNDR의 `mouse` 객체가 자동으로 마우스 위치를 추적합니다. p5.js의 `mouseX`, `mouseY`와 유사하지만, **벡터(Vector2) 형태**라는 게 다릅니다.

---

## 정리: 프로그램 구조의 생명주기

OPENRNDR 프로그램이 작동하는 순서:

```
1. 프로그램 시작 (main())
    ↓
2. configure { } 실행 (한 번)
    → 창 크기, 제목 등 설정
    ↓
3. program { } 블록 초기화 (한 번)
    → 전역 변수, 상태 선언
    ↓
4. extend { } 반복 실행 (매 프레임, 기본 60fps)
    → 화면 지우기
    → 도형 그리기
    → 상태 업데이트
    ↓
5. 창이 닫힐 때까지 반복
```

이 구조를 정확히 이해하면, OPENRNDR의 나머지 문법들도 훨씬 쉬워집니다.

---

## 다음 단계

다음 글에서는 **람다 표현식과 리시버**를 다룹니다. `extend { }` 블록 안에서 `drawer.` 없이 `circle()`을 쓸 수 있는 이유, 그리고 그게 제너러티브 아트 코드를 얼마나 깔끔하게 만드는지 보겠습니다.

---

## 연습 문제

1. **기본 구조 이해하기**
   ```kotlin
   fun main() = application {
       configure { width = 600; height = 600 }
       program {
           extend {
               drawer.clear(ColorRGBa.BLACK)
               drawer.circle(300.0, 300.0, 100.0)
           }
       }
   }
   ```
   위 코드를 실행하고, 캔버스 크기와 원의 크기를 바꿔보세요.

2. **시간 변수 추가하기**
   ```kotlin
   program {
       var t = 0.0
       extend {
           // ... 시간 t를 이용해 원의 위치나 크기를 변화시켜보세요
           t += 0.01
       }
   }
   ```

3. **여러 도형 조합하기**
   - 격자 패턴 대신 원형 배치로 원들을 배열하고, 시간에 따라 회전시켜 보세요.

---

**이전 글**: 없음 (첫 번째)  
**다음 글**: OPENRNDR 시작하기 (2): 람다 표현식과 리시버 — Drawer의 비밀
