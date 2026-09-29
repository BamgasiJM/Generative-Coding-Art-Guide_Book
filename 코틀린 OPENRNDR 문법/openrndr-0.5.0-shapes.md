# OPENRNDR 0.5.0 도형 그리기 정리

기본 도형 함수 · 색 지정 · 도형을 마스크로 쓰는 방법 · 도형 안에 GLSL 셰이더 넣기

---

## 0. 문서 범위와 기준

### 0.1 기준 버전

- 기준 버전은 OPENRNDR 0.5.0(2026-09-16 릴리즈)과 ORX 0.5.0입니다.
- 0.5.0 릴리즈 노트에는 `Drawer`의 도형 그리기 함수(`circle`, `rectangle`, `contour`, `shape` 등)를 변경하거나 제거했다는 항목이 없습니다. 따라서 공식 가이드(guide.openrndr.org)의 도형 API가 그대로 유효한 것으로 판단했습니다.
- 도형 작업에 간접적으로 영향을 주는 0.5.0 변경 사항은 다음과 같습니다.

| 변경 사항 | 도형 작업과의 관계 |
| --- | --- |
| SDL3 기반 백엔드 추가, GLFW는 deprecated | 창 생성·입력 계층의 변경입니다. 그리기 API에는 영향이 없습니다. |
| macOS의 Angle 기반 GLES 백엔드에서 shape/contour fill 렌더링 수정 | macOS에서 채워진 shape가 이상하게 보이던 문제가 수정되었습니다. |
| `Segment2D/3D`에 이차(quadratic) 근사 추가 | `openrndr-math` 변경입니다. 릴리즈 노트에 함수 이름이 없어 본문에서는 사용하지 않았습니다. |
| Kotlin 2.4.20, Gradle 9.7.1, LWJGL 3.4.3 | 빌드 환경 요건입니다. |
| `openrndr-module-catalog`, `openrndr-dependency-catalog` 추가 | 의존성 선언 방식이 단순해졌습니다. |

### 0.2 검증 수준에 대한 고지

- 이 문서의 API 사용법과 예제는 공식 가이드 예제와 0.5.0 API 문서를 근거로 작성했습니다.
- 공식 예제를 그대로 옮긴 코드와, 공식 API를 조합해 새로 작성한 코드가 섞여 있습니다. 새로 작성한 코드는 0.5.0 환경에서 직접 컴파일·실행해 확인한 것이 아닙니다.
- 실행 전에 확인이 필요한 항목은 [12장 검증 메모](#12-검증-메모)에 정리했습니다.

### 0.3 공통 import

```kotlin
import org.openrndr.application
import org.openrndr.color.ColorRGBa
import org.openrndr.draw.*
import org.openrndr.math.Vector2
import org.openrndr.shape.*
import kotlin.math.cos
import kotlin.math.sin
```

- ORX 모듈(`orx-compositor`, `orx-fx`, `orx-shade-styles`, `orx-shapes`)의 클래스는 패키지가 모듈별로 나뉘어 있습니다. 본문 코드에는 ORX import를 적지 않았으므로 IDE의 자동 import를 사용하시기 바랍니다.

---

## 1. 준비

### 1.1 프로젝트 설정

- `openrndr-template`을 사용하는 경우 `gradle/libs.versions.toml`에서 버전을 지정합니다.

```toml
openrndr = "0.5.0"
orx = "0.5.0"
```

- 템플릿의 기본 orx 번들에는 `orx-compositor`, `orx-shade-styles` 등이 포함되어 있다고 공식 가이드에 명시되어 있습니다.
- 버전 변경 후에는 Gradle 설정을 다시 불러와야 합니다.

### 1.2 기본 골격

```kotlin
fun main() = application {
    configure {
        width = 800
        height = 600
    }
    program {
        extend {
            drawer.clear(ColorRGBa.PINK)
            // 그리기 코드
        }
    }
}
```

### 1.3 좌표계와 자료형 규칙

- 원점은 창의 **왼쪽 위**이고, x는 오른쪽, y는 **아래쪽**으로 증가합니다.
- 좌표와 크기는 대부분 `Double`입니다. `width`, `height`는 `Int`이므로 `width / 2.0`, `height * 1.0`처럼 `Double`로 변환해서 사용합니다.
- `extend { }` 블록은 프레임마다 실행됩니다. `seconds`는 프로그램 시작 이후의 시간(초)입니다.
- 매 프레임 반복할 필요가 없는 객체(`ShadeStyle`, `ShapeContour`, `Shape`, `RenderTarget`)는 `extend` 바깥의 `program { }` 안에서 한 번만 만드는 것이 원칙입니다.

---

## 2. 기본 도형 그리기

### 2.1 Drawer 도형 함수 개요

| 함수 | 입력 | 적용되는 스타일 |
| --- | --- | --- |
| `circle(x, y, radius)` / `circle(Vector2, radius)` / `circle(Circle)` | 중심 기준 | fill, stroke, strokeWeight |
| `circles(List<Circle>)` / `circles(List<Vector2>, radius)` | 배치 그리기 | 동일 |
| `rectangle(x, y, width, height)` / `rectangle(Rectangle)` | **왼쪽 위** 기준 | fill, stroke, strokeWeight |
| `rectangles { }` | 배치 빌더 | 동일 |
| `lineSegment(x0, y0, x1, y1)` | 선분 | stroke, strokeWeight, lineCap |
| `lineStrip(List<Vector2>)` | 꺾은선 | stroke, strokeWeight, lineCap, lineJoin |
| `segment(Segment2D)` / `segments(List)` | 베지에 조각 | stroke |
| `contour(ShapeContour)` / `contours(List)` | 윤곽선 | 닫힌 contour는 fill 사용, stroke 사용 |
| `shape(Shape)` / `shapes(List)` | 구멍을 포함할 수 있는 도형 | fill, stroke |

### 2.2 원

- 원은 `(x, y)`를 중심으로 그려집니다. 채움은 `drawer.fill`, 테두리는 `drawer.stroke`와 `drawer.strokeWeight`를 따릅니다.
- `fill` 또는 `stroke`에 `null`을 지정하면 해당 부분을 그리지 않습니다.

```kotlin
fun main() = application {
    configure { height = 300 }
    program {
        extend {
            drawer.clear(ColorRGBa.PINK)

            // 흰 채움 + 검은 테두리
            drawer.fill = ColorRGBa.WHITE
            drawer.stroke = ColorRGBa.BLACK
            drawer.strokeWeight = 1.0
            drawer.circle(width / 6.0, height / 2.0, width / 8.0)

            // 채움 없음, 테두리만
            drawer.fill = null
            drawer.stroke = ColorRGBa.BLACK
            drawer.circle(width / 6.0 + width / 3.0, height / 2.0, width / 8.0)

            // 채움만, 테두리 없음
            drawer.fill = ColorRGBa.WHITE
            drawer.stroke = null
            drawer.circle(width / 6.0 + 2 * width / 3.0, height / 2.0, width / 8.0)
        }
    }
}
```

- `Vector2`나 `Circle` 값을 이미 가지고 있다면 오버로드를 사용하면 편합니다.

```kotlin
drawer.circle(mouse.position, 50.0)          // Vector2 + 반지름
drawer.circle(Circle(200.0, 200.0, 40.0))    // Circle 객체
```

### 2.3 사각형

- `rectangle`은 **왼쪽 위 모서리** 좌표와 너비·높이를 받습니다. 중심 기준으로 그리려면 `Rectangle.fromCenter`를 사용합니다.

```kotlin
extend {
    drawer.clear(ColorRGBa.PINK)
    drawer.fill = ColorRGBa.WHITE
    drawer.stroke = ColorRGBa.BLACK

    // 왼쪽 위 기준
    drawer.rectangle(50.0, 50.0, 200.0, 120.0)

    // 중심 기준: Rectangle 객체를 만든 뒤 전달
    val r = Rectangle.fromCenter(Vector2(width / 2.0, height / 2.0), 200.0, 120.0)
    drawer.rectangle(r)
}
```

### 2.4 선분과 꺾은선

- 선은 `stroke`와 `strokeWeight`로 그려지며 `fill`은 영향을 주지 않습니다.
- 선 끝 모양은 `lineCap`(`BUTT`, `ROUND`, `SQUARE`), 꺾이는 부분은 `lineJoin`으로 지정합니다.

```kotlin
extend {
    drawer.clear(ColorRGBa.PINK)
    drawer.stroke = ColorRGBa.BLACK
    drawer.strokeWeight = 5.0

    drawer.lineCap = LineCap.ROUND
    drawer.lineSegment(10.0, height / 2.0 - 20.0, width - 10.0, height / 2.0 - 20.0)

    drawer.lineCap = LineCap.BUTT
    drawer.lineSegment(10.0, height / 2.0, width - 10.0, height / 2.0)

    drawer.lineCap = LineCap.SQUARE
    drawer.lineSegment(10.0, height / 2.0 + 20.0, width - 10.0, height / 2.0 + 20.0)

    // 꺾은선
    drawer.lineJoin = LineJoin.ROUND
    drawer.lineStrip(
        listOf(
            Vector2(10.0, height - 10.0),
            Vector2(width / 2.0, 10.0),
            Vector2(width - 10.0, height - 10.0)
        )
    )
}
```

### 2.5 변환(translate / rotate / scale)과 도형

- 변환은 좌표계의 **원점**을 기준으로 적용됩니다. 도형을 제자리에서 회전시키려면 도형을 원점 주위에 그리고, 변환으로 화면 위치로 옮깁니다.
- 변환 코드는 **아래에서 위로** 읽습니다. `translate`를 먼저 쓰고 `rotate`를 나중에 쓰면 실제로는 회전이 먼저 적용된 뒤 이동됩니다.
- `isolated { }`는 스타일과 변환을 저장했다가 블록이 끝나면 복원합니다.

```kotlin
extend {
    drawer.clear(ColorRGBa.PINK)
    drawer.stroke = null

    for (i in 0 until 5) {
        drawer.isolated {
            translate(120.0 + i * 140.0, height / 2.0)
            rotate(seconds * 30.0 + i * 15.0)
            scale(1.0 + i * 0.15)
            fill = ColorRGBa.WHITE.shade(1.0 - i * 0.15)
            rectangle(-40.0, -40.0, 80.0, 80.0)     // 원점 기준으로 배치
        }
    }
}
```

### 2.6 수학 객체와 Contour · Shape

OPENRNDR의 도형 표현은 세 단계로 구성됩니다.

| 단계 | 클래스 | 설명 |
| --- | --- | --- |
| 1 | `Segment2D` | 시작점, 끝점, 제어점 0~2개로 이루어진 베지에 조각(직선·2차·3차) |
| 2 | `ShapeContour` | 끝과 시작이 이어진 `Segment` 모음. 닫힌 것(O)과 열린 것(S)이 있음 |
| 3 | `Shape` | `ShapeContour`의 모음. 구멍이 있는 도형을 표현 가능 |

`Circle`, `Rectangle`, `LineSegment` 같은 수학 객체는 화면에 그리지 않아도 존재할 수 있으며, `.contour` 또는 `.shape`로 변환됩니다.

```kotlin
val c = Circle(200.0, 200.0, 50.0).contour   // ShapeContour
val s = Circle(200.0, 200.0, 50.0).shape     // Shape
```

#### 2.6.1 ContourBuilder

- SVG와 유사한 명령어(`moveTo`, `lineTo`, `curveTo`, `arcTo`, `close`)로 contour를 만듭니다.
- `cursor`는 현재 위치, `anchor`는 현재 서브패스의 시작점입니다.

```kotlin
fun main() = application {
    program {
        // 프레임마다 바뀌지 않으므로 한 번만 생성
        val triangle = contour {
            moveTo(Vector2(width / 2.0 - 120.0, height / 2.0 - 120.0))
            lineTo(cursor + Vector2(240.0, 0.0))
            lineTo(cursor + Vector2(0.0, 240.0))
            lineTo(anchor)
            close()
        }
        extend {
            drawer.clear(ColorRGBa.WHITE)
            drawer.fill = ColorRGBa.PINK
            drawer.stroke = null
            drawer.contour(triangle)
        }
    }
}
```

#### 2.6.2 점 목록에서 Contour 만들기 (정다각형, 별)

`ShapeContour.fromPoints(points, closed)`는 점들을 직선으로 연결합니다. 정다각형과 별은 이 함수로 만들 수 있습니다.

```kotlin
fun regularPolygon(center: Vector2, radius: Double, sides: Int, rotationDeg: Double = -90.0): ShapeContour {
    val points = List(sides) {
        val a = Math.toRadians(rotationDeg) + it * 2.0 * Math.PI / sides
        center + Vector2(cos(a), sin(a)) * radius
    }
    return ShapeContour.fromPoints(points, closed = true)
}

fun star(center: Vector2, outer: Double, inner: Double, tips: Int, rotationDeg: Double = -90.0): ShapeContour {
    val points = List(tips * 2) {
        val r = if (it % 2 == 0) outer else inner
        val a = Math.toRadians(rotationDeg) + it * Math.PI / tips
        center + Vector2(cos(a), sin(a)) * r
    }
    return ShapeContour.fromPoints(points, closed = true)
}
```

```kotlin
fun main() = application {
    configure { width = 800; height = 400 }
    program {
        val hexagon = regularPolygon(Vector2(200.0, 200.0), 120.0, 6)
        val starContour = star(Vector2(600.0, 200.0), 140.0, 60.0, 5)
        extend {
            drawer.clear(ColorRGBa.WHITE)
            drawer.fill = ColorRGBa.PINK
            drawer.stroke = ColorRGBa.BLACK
            drawer.strokeWeight = 3.0
            drawer.contour(hexagon)
            drawer.contour(starContour)
        }
    }
}
```

- 부드러운 곡선이 필요하면 `orx-shapes`의 `hobbyCurve(points, closed)`를 사용할 수 있습니다. 공식 가이드는 이 함수를 `ShapeContour.fromPoints`의 곡선 버전으로 소개합니다.

#### 2.6.3 Segment에서 Contour 만들기

각 `Segment`는 이전 조각이 끝나는 지점에서 시작해야 합니다.

```kotlin
val segments = listOf(
    Segment2D(Vector2(10.0, 100.0), Vector2(200.0, 80.0)),                                       // 직선
    Segment2D(Vector2(200.0, 80.0), Vector2(250.0, 280.0), Vector2(400.0, 80.0)),                // 2차
    Segment2D(Vector2(400.0, 80.0), Vector2(450.0, 180.0), Vector2(500.0, 0.0), Vector2(630.0, 80.0)) // 3차
)
val open = ShapeContour.fromSegments(segments, closed = false)
```

#### 2.6.4 Contour 속성과 변형

```kotlin
val pos = contour.position(0.1)                 // 시작 근처의 점
val normal = contour.normal(0.9)                // 끝 근처의 법선
val bounds = contour.bounds                     // Rectangle
val length = contour.length                     // 길이
val points = contour.equidistantPositions(20)   // 등간격 점 20개
val part = contour.sub(0.0, 0.5)                // 앞 절반만 잘라낸 contour
val grown = contour.offset(10.0)                // 바깥으로 오프셋
```

- `position(ut)`의 `ut`는 길이에 비례하지 않습니다. 등속 이동이 필요하면 `orx-shapes`의 `rectified()`를 사용합니다.

#### 2.6.5 Shape와 구멍

- `shape { contour { ... } contour { ... } }` 빌더로 여러 contour를 묶습니다. 공식 예제에서는 바깥 윤곽과 안쪽 윤곽을 함께 넣어 구멍이 있는 도형을 만듭니다.

```kotlin
val s = shape {
    contour {
        moveTo(Vector2(width / 2.0 - 120.0, height / 2.0 - 120.0))
        lineTo(cursor + Vector2(240.0, 0.0))
        lineTo(cursor + Vector2(0.0, 240.0))
        lineTo(anchor)
        close()
    }
    contour {
        moveTo(Vector2(width / 2.0 - 80.0, height / 2.0 - 100.0))
        lineTo(cursor + Vector2(190.0, 0.0))
        lineTo(cursor + Vector2(0.0, 190.0))
        lineTo(anchor)
        close()
    }
}
drawer.shape(s)
```

#### 2.6.6 Boolean 연산 (compound)

- `compound { }` 빌더로 합집합(`union`), 차집합(`difference`), 교집합(`intersection`)을 계산합니다. 결과는 `List<Shape>`이며 `drawer.shapes(...)`로 그립니다.

```kotlin
fun main() = application {
    configure { width = 800; height = 400 }
    program {
        val ring = compound {
            difference {
                shape(Circle(200.0, 200.0, 140.0).shape)
                shape(Circle(200.0, 200.0, 90.0).shape)
            }
        }
        val lens = compound {
            intersection {
                shape(Circle(560.0, 160.0, 120.0).shape)
                shape(Circle(560.0, 240.0, 120.0).shape)
            }
        }
        extend {
            drawer.clear(ColorRGBa.WHITE)
            drawer.fill = ColorRGBa.PINK
            drawer.stroke = ColorRGBa.PINK.shade(0.7)
            drawer.shapes(ring)
            drawer.shapes(lens)
        }
    }
}
```

- `compound`는 트리 구조를 지원하므로 `union { intersection { ... } intersection { ... } }`처럼 중첩할 수 있습니다.
- 계산 비용이 있으므로 입력이 바뀌지 않는다면 `extend` 바깥에서 한 번만 계산합니다.

### 2.7 배치(batch) 그리기

- 같은 종류의 도형을 수천 개 그릴 때는 `drawer.circle()`을 반복하는 것보다 `drawer.circles(list)`가 훨씬 빠릅니다.

```kotlin
fun main() = application {
    program {
        extend {
            drawer.clear(ColorRGBa.PINK)
            drawer.fill = ColorRGBa.WHITE
            drawer.stroke = ColorRGBa.BLACK
            drawer.strokeWeight = 1.0
            val circles = List(5000) {
                Circle(Math.random() * width, Math.random() * height, Math.random() * 10.0 + 5.0)
            }
            drawer.circles(circles)
        }
    }
}
```

- 개별 색이 필요하면 빌더를 사용합니다. 정적 배치는 `drawer.rectangleBatch { }`로 한 번만 만들어 재사용할 수 있습니다.

```kotlin
extend {
    drawer.clear(ColorRGBa.GRAY)
    drawer.rectangles {
        repeat(100) {
            fill = ColorRGBa.PINK.opacify(0.2)
            stroke = null
            rectangle(Rectangle((it * 37.0) % width, 0.0, 30.0, 5.0 + it * 3.0))
        }
    }
}
```

### 2.8 주의 사항

- `ShapeContour`와 `Shape`를 **RenderTarget**에 그릴 때는 컬러 버퍼와 함께 **깊이(스텐실) 버퍼**가 필요합니다. 없으면 `drawing ... requires a render target with a stencil attachment` 오류가 발생합니다.

```kotlin
val rt = renderTarget(width, height) {
    colorBuffer()
    depthBuffer()   // contour/shape를 그릴 때 필요
}
```

- 렌더 타깃 크기가 창과 다르면 `ortho(rt)`로 투영 행렬을 맞춰야 합니다.

---

## 3. 색 지정

### 3.1 ColorRGBa 기본

- OPENRNDR의 기본 색 클래스는 `ColorRGBa`이며 채널 값은 **0.0~1.0**입니다.
- 미리 정의된 상수: `BLACK`, `WHITE`, `RED`, `GREEN`, `BLUE`, `YELLOW`, `GRAY`, `PINK`. 이 밖에 `ORANGE`, `PURPLE`, `TRANSPARENT`도 공식 예제에 사용됩니다.

```kotlin
// 생성자: R, G, B, (A)
val red = ColorRGBa(1.0, 0.0, 0.0)
val halfBlue = ColorRGBa(0.0, 0.0, 1.0, 0.5)

// rgb / rgba 함수
val magenta = rgb(1.0, 0.0, 1.0)
val magentaHalf = rgb(1.0, 0.0, 1.0, 0.5)

// 16진수 코드 (정수 또는 문자열, 문자열의 # 은 생략 가능)
val c1 = ColorRGBa.fromHex(0xffc0cb)
val c2 = ColorRGBa.fromHex("#ffc0cb")
```

### 3.2 fill · stroke · 선 스타일

`Drawer`가 유지하는 DrawStyle 중 도형과 관련된 속성은 다음과 같습니다(공식 가이드의 표).

| 속성 | 타입 | 기본값 | 설명 |
| --- | --- | --- | --- |
| `fill` | `ColorRGBa?` | `WHITE` | 채움 색. `null`이면 채우지 않음 |
| `stroke` | `ColorRGBa?` | `BLACK` | 선 색. `null`이면 선을 그리지 않음 |
| `strokeWeight` | `Double` | `1.0` | 선 굵기 |
| `smooth` | `Boolean` | `true` | 윤곽을 부드럽게 표시할지 여부 |
| `lineCap` | `LineCap` | `BUTT` | 선 끝 모양 |
| `lineJoin` | `LineJoin` | `MITER` | 꺾이는 부분 모양 |
| `miterLimit` | `Double` | `4.0` | miter 최대 길이 |
| `shadeStyle` | `ShadeStyle?` | `null` | 셰이더 스타일 (5장) |
| `blendMode` | `BlendMode` | `OVER`(가이드 표 기준) | 합성 방식 |
| `clip` | `Rectangle?` | `null` | 직사각형 클리핑 영역 (4장) |

```kotlin
extend {
    drawer.clear(ColorRGBa.fromHex("#1E2A78"))
    drawer.fill = ColorRGBa.fromHex("#FFC0CB")
    drawer.stroke = ColorRGBa.WHITE
    drawer.strokeWeight = 6.0
    drawer.lineJoin = LineJoin.ROUND
    drawer.rectangle(100.0, 100.0, 300.0, 200.0)
}
```

### 3.3 색 조작: shade · opacify · mix

- `shade(f)`: 1.0 미만이면 어둡게, 1.0 초과이면 밝게 만듭니다(공식 예제에서 `shade(1.0 + i / 15.0)`로 밝은 톤을 만듭니다).
- `opacify(f)`: 불투명도를 조절합니다.
- `mix(a, b, t)`: 두 색을 `t`(0~1) 비율로 섞습니다.

```kotlin
extend {
    drawer.stroke = null
    val base = ColorRGBa.PINK
    val target = ColorRGBa.fromHex("#FC3549")

    for (i in 0..15) {
        val x = 35.0 + (700 / 16.0) * i
        drawer.fill = base.shade(i / 15.0)              // 어두운 톤
        drawer.rectangle(x, 32.0, 700 / 16.0, 64.0)

        drawer.fill = base.shade(1.0 + i / 15.0)        // 밝은 톤
        drawer.rectangle(x, 96.0, 700 / 16.0, 64.0)

        drawer.fill = base.opacify(i / 15.0)            // 투명도 변화
        drawer.rectangle(x, 160.0, 700 / 16.0, 64.0)

        drawer.fill = mix(base, target, i / 15.0)       // 혼합
        drawer.rectangle(x, 224.0, 700 / 16.0, 64.0)
    }
}
```

### 3.4 다른 색 공간

`ColorRGBa` 외에 다음 색 공간 클래스를 제공합니다.

| 클래스 | 설명 |
| --- | --- |
| `ColorHSVa` / `ColorHSLa` | 색상·채도·명도(밝기) |
| `ColorXSVa` / `ColorXSLa` | Kuler 방식으로 색상 축을 보정한 HSV/HSL |
| `ColorXYZa`, `ColorYxya` | CIE 색 공간 |
| `ColorLABa`, `ColorLCHABa`, `ColorLSHABa` | LAB 계열 |
| `ColorLUVa`, `ColorLCHUVa`, `ColorLSHUVa` | LUV 계열 |
| `ColorATVa` | Coloroid (부분 구현) |
| `ColorOKLABa` | OKLab. `ColorRGBa.toOKLABa()`로 변환하며 그라디언트 프리셋에서 사용됨 |

- 색상 각도(hue)는 **0~360도**입니다. 색상 환을 도는 팔레트는 HSV로 만들면 간단합니다.

```kotlin
extend {
    drawer.stroke = null
    for (i in 0 until 12) {
        drawer.fill = ColorHSVa(i * 30.0, 0.7, 0.9).toRGBa()   // hue 30도씩 증가
        drawer.circle(70.0 + i * 55.0, height / 2.0, 24.0)
    }
}
```

- 공식 가이드는 Kuler 방식의 `XSV`/`XSL`을 "화가에게 더 적합한 색 공간"으로 설명합니다. 빨강-초록, 파랑-노랑 보색축이 자연스럽게 배치되기 때문입니다.

### 3.5 스타일 스택: pushStyle · popStyle · isolated

- 스타일을 바꾼 뒤 원래대로 되돌려야 할 때 사용합니다.

```kotlin
drawer.pushStyle()
drawer.fill = ColorRGBa.PINK
drawer.rectangle(100.0, 100.0, 100.0, 100.0)
drawer.popStyle()

// 스타일과 변환을 함께 저장·복원
drawer.isolated {
    fill = ColorRGBa.PINK
    rectangle(100.0, 100.0, 100.0, 100.0)
}
```

### 3.6 블렌드 모드

- `BlendMode` 항목은 `OVER`, `BLEND`, `ADD`, `SUBTRACT`, `MULTIPLY`, `REPLACE`, `REMOVE`, `MIN`, `MAX`, `SCREEN`, `OVERLAY`, `DARKEN`, `LIGHTEN`, `COLOR_DODGE`, `COLOR_BURN`, `HARD_LIGHT`, `SOFT_LIGHT`, `DIFFERENCE`, `EXCLUSION`, `HSL_HUE`, `HSL_SATURATION`, `HSL_COLOR`, `HSL_LUMINOSITY`입니다.
- 가산 합성(`ADD`)은 겹친 부분이 밝아지므로 빛 표현에 적합합니다.

```kotlin
extend {
    drawer.clear(ColorRGBa.BLACK)
    drawer.stroke = null
    drawer.isolated {
        drawStyle.blendMode = BlendMode.ADD
        val colors = listOf(ColorRGBa.RED, ColorRGBa.GREEN, ColorRGBa.BLUE)
        colors.forEachIndexed { i, c ->
            fill = c.opacify(0.8)
            circle(width / 2.0 + cos(seconds + i * 2.094) * 80.0,
                   height / 2.0 + sin(seconds + i * 2.094) * 80.0, 120.0)
        }
    }
}
```

- 0.5.0 API 문서는 `DrawStyle.blendMode`를 `BlendMode?`(기본값 `null`)로 표기하고, 가이드 표는 기본값을 `OVER`로 표기합니다. 표기가 다르므로 합성 방식이 중요한 코드에서는 명시적으로 지정하시기 바랍니다.

### 3.7 그라디언트 채움 (orx-shade-styles)

- `orx-shade-styles`의 `gradient` 생성기로 선형·방사형·원뿔형 그라디언트를 만들 수 있습니다. 내부적으로 GLSL 코드를 생성하여 GPU에서 실행합니다.
- 그라디언트는 도형의 경계 상자를 기준으로 0~1 좌표계에서 정의됩니다.

```kotlin
// 선형: 회전하는 방향
drawer.shadeStyle = gradient<ColorRGBa> {
    stops[0.0] = ColorRGBa.PINK
    stops[0.8] = ColorRGBa.ORANGE
    stops[1.0] = ColorRGBa.RED
    linear {
        start = Polar(seconds * 60.0, 0.5).cartesian + 0.5
        end = Polar(seconds * 60.0 + 180.0, 0.5).cartesian + 0.5
    }
}
drawer.rectangle(80.0, 40.0, 200.0, 200.0)
drawer.circle(180.0, 340.0, 90.0)

// 방사형
drawer.shadeStyle = gradient<ColorRGBa> {
    stops[0.0] = ColorRGBa.PINK
    stops[1.0] = ColorRGBa.RED
    radial {
        radius = 0.5
        center = Vector2(cos(seconds), sin(seconds * 2.0)) * 0.5 + 0.5
    }
}

// 원뿔형 + OKLab 보간
drawer.shadeStyle = gradient<ColorOKLABa> {
    stops[0.0] = ColorRGBa.PINK.toOKLABa()
    stops[1.0] = ColorRGBa.PURPLE.toOKLABa()
    conic { rotation = seconds * 60.0 }
}
```

- `quantization = 16`으로 색을 단계화할 수 있고, `levelWarpFunction`, `domainWarpFunction`에 GLSL 함수 문자열을 넣어 그라디언트를 변형할 수 있습니다.
- `Polar`는 `org.openrndr.math`에 있습니다. `gradient`의 import 경로는 [12장](#12-검증-메모)을 참고하시기 바랍니다.

### 3.8 도형마다 색을 다르게 지정하기

- OPENRNDR에는 도형 객체가 색을 갖는 개념이 없습니다. 색은 **그리기 직전의 `drawer.fill`/`drawer.stroke` 상태**로 결정됩니다. 따라서 도형마다 색이 다르면 그릴 때마다 색을 바꿔 줍니다.

```kotlin
program {
    val palette = listOf("#F94144", "#F3722C", "#F9C74F", "#90BE6D", "#43AA8B", "#577590")
        .map { ColorRGBa.fromHex(it) }

    extend {
        drawer.clear(ColorRGBa.WHITE)
        drawer.stroke = null
        palette.forEachIndexed { i, color ->
            drawer.fill = color
            drawer.circle(90.0 + i * 120.0, height / 2.0, 50.0)
        }
    }
}
```

---

## 4. 도형을 마스크로 쓰는 방법

### 4.1 방식 비교

OPENRNDR의 `Drawer`는 **직사각형 클리핑 하나**만 직접 지원합니다. 임의의 도형을 마스크로 쓰려면 아래 방식 중 하나를 선택합니다.

| 방식 | 마스크 형태 | 결과 | 장점 | 한계 |
| --- | --- | --- | --- | --- |
| A. `drawStyle.clip` | 직사각형 1개 | 래스터 | 가장 단순함 | 직사각형만 가능 |
| B. `compound { intersection }` | 임의의 벡터 도형 | **벡터** | 결과가 도형이므로 stroke·셰이더 적용 가능, 경계가 선명함 | 입력이 프레임마다 바뀌면 CPU 비용 증가 |
| C. `orx-compositor` 레이어 + `clip` | 임의의 그리기 결과 | 래스터 | 이미지·텍스트·도형 모두 마스크로 사용 가능, 블러 등 후처리 결합 용이 | ORX 의존, 레이어마다 오프스크린 버퍼 사용 |
| D. RenderTarget 2장 + ShadeStyle 합성 | 임의의 그리기 결과 | 래스터 | 합성식을 직접 제어(반전, 부드러운 경계, 그레이스케일 마스크) | 코드량이 많음 |
| E. 프래그먼트 셰이더 SDF 마스크 | 수식으로 정의된 도형 | 래스터 | 버퍼가 필요 없고 GPU에서 처리되며 경계 안티앨리어싱이 좋음 | 수식으로 표현되는 도형에 한정 |

- 참고: `DrawStyle`에는 `stencil` 관련 속성(`StencilStyle`)이 있으나 가이드에 사용 예가 없고, contour/shape 채움이 스텐실 버퍼를 사용한다는 점이 오류 메시지에서 확인되므로 이 문서에서는 다루지 않았습니다.

### 4.2 방식 A: 직사각형 클리핑

```kotlin
fun main() = application {
    program {
        extend {
            drawer.stroke = null
            drawer.fill = ColorRGBa.PINK

            // 클리핑 영역 설정
            drawer.drawStyle.clip = Rectangle(100.0, 100.0, width - 200.0, height - 200.0)

            drawer.circle(
                cos(seconds) * width / 2.0 + width / 2.0,
                sin(seconds) * height / 2.0 + height / 2.0,
                200.0
            )

            // 클리핑 해제
            drawer.drawStyle.clip = null
        }
    }
}
```

- 클리핑을 해제하지 않으면 이후의 모든 그리기에 영향을 줍니다. `drawer.isolated { }` 안에서 설정하면 자동으로 복원됩니다.

### 4.3 방식 B: Boolean 교집합으로 벡터 마스킹

마스크 도형과 콘텐츠 도형의 교집합을 구하면 "마스크 안쪽에 있는 부분만 남은 도형"이 만들어집니다. 결과가 벡터이므로 이후에 stroke를 그리거나 셰이더를 적용할 수 있습니다.

```kotlin
fun main() = application {
    configure { width = 800; height = 600 }
    program {
        val mask = star(Vector2(400.0, 300.0), 260.0, 110.0, 5).shape

        // 콘텐츠: 세로 줄무늬 12개
        val stripes = List(12) { i ->
            Rectangle(40.0 + i * 60.0, 0.0, 30.0, height * 1.0).shape
        }

        // 줄무늬마다 마스크와의 교집합을 계산 (입력이 고정이므로 한 번만)
        val clipped = stripes.map { stripe ->
            compound {
                intersection {
                    shape(mask)
                    shape(stripe)
                }
            }
        }

        extend {
            drawer.clear(ColorRGBa.WHITE)
            drawer.stroke = null
            clipped.forEachIndexed { i, pieces ->
                drawer.fill = mix(ColorRGBa.PINK, ColorRGBa.fromHex("#FC3549"), i / 11.0)
                drawer.shapes(pieces)
            }

            // 마스크 윤곽선 표시
            drawer.fill = null
            drawer.stroke = ColorRGBa.BLACK
            drawer.strokeWeight = 3.0
            drawer.shape(mask)
        }
    }
}
```

- 마스크를 움직이려면 `extend` 안에서 `compound`를 다시 계산해야 합니다. 줄무늬 수가 많으면 프레임 시간이 늘어나므로, 움직이는 마스크에는 C~E 방식이 적합합니다.

### 4.4 방식 C: orx-compositor 레이어 마스크

`orx-compositor`는 레이어 기반 합성 DSL입니다. 공식 가이드의 마스킹 패턴은 다음과 같습니다.

1. 마스크가 될 레이어를 **먼저** 그립니다.
2. 콘텐츠 레이어에 `blend(Normal()) { clip = true }`를 지정합니다.
3. 두 레이어를 하나의 부모 `layer { }`로 묶어 배경과 먼저 합성되지 않도록 합니다.

```kotlin
fun main() = application {
    configure { width = 800; height = 600 }
    program {
        val composite = compose {
            // 배경
            draw { drawer.clear(ColorRGBa.PINK) }

            layer {
                // 마스크 레이어: 움직이는 원
                layer {
                    draw {
                        drawer.fill = ColorRGBa.WHITE
                        drawer.stroke = null
                        drawer.circle(width / 2.0 + cos(seconds) * 150.0, height / 2.0, 200.0)
                    }
                }
                // 콘텐츠 레이어: 마스크(하위 레이어)가 있는 부분에만 표시됨
                layer {
                    blend(Normal()) { clip = true }
                    draw {
                        drawer.stroke = null
                        for (i in 0 until 20) {
                            drawer.fill = if (i % 2 == 0) ColorRGBa.BLACK else ColorRGBa.WHITE
                            drawer.rectangle(i * 40.0, 0.0, 40.0, height * 1.0)
                        }
                    }
                }
            }
        }

        extend {
            composite.draw(drawer)
        }
    }
}
```

- 콘텐츠 레이어 `draw { }`는 매 프레임 실행되고, `compose { }` 본문과 `layer { }` 본문(리소스 로딩 등)은 한 번만 실행됩니다.
- 마스크 레이어 뒤에 `post(ApproximateGaussianBlur()) { ... }`을 추가하면 부드러운 경계의 마스크를 만들 수 있습니다. 가이드가 후처리를 레이어 단위로 적용하는 방식이기 때문에 가능한 구성이며, 적용 순서(마스크 후처리 → 콘텐츠 합성)는 실행하여 확인하시기 바랍니다.
- 가장자리가 거칠면 `layer(multisample = BufferMultisample.SampleCount(8)) { ... }`로 멀티샘플링을 지정합니다.

### 4.5 방식 D: RenderTarget 두 장과 ShadeStyle로 직접 합성

마스크 도형과 콘텐츠를 각각 오프스크린 버퍼에 그린 뒤, 셰이더에서 **콘텐츠의 RGB + 마스크의 알파**로 합성합니다. 합성식을 직접 쓰므로 마스크 반전, 부드러운 경계, 다중 마스크 곱 등 원하는 방식으로 확장할 수 있습니다.

```kotlin
fun main() = application {
    configure { width = 800; height = 600 }
    program {
        // contour를 그리므로 depthBuffer 필수
        val maskTarget = renderTarget(width, height) {
            colorBuffer()
            depthBuffer()
        }
        val contentTarget = renderTarget(width, height) {
            colorBuffer()
            depthBuffer()
        }

        val starMask = star(Vector2(400.0, 300.0), 260.0, 110.0, 5)

        val compositeStyle = shadeStyle {
            fragmentTransform = """
                vec2 uv = c_boundsPosition.xy;
                uv.y = 1.0 - uv.y;
                vec3 content = texture(p_content, uv).rgb;
                float m = texture(p_mask, uv).a;
                x_fill = vec4(content, m);
            """.trimIndent()
        }

        extend {
            // 1. 마스크 버퍼: 투명 배경에 흰색 도형
            drawer.isolatedWithTarget(maskTarget) {
                drawer.clear(ColorRGBa.TRANSPARENT)
                drawer.fill = ColorRGBa.WHITE
                drawer.stroke = null
                drawer.contour(starMask)
            }

            // 2. 콘텐츠 버퍼: 반드시 불투명하게 채움
            drawer.isolatedWithTarget(contentTarget) {
                drawer.clear(ColorRGBa.BLACK)
                drawer.stroke = null
                for (i in 0 until 40) {
                    drawer.fill = ColorRGBa.PINK.shade(0.4 + 0.6 * (0.5 + 0.5 * sin(seconds + i * 0.3)))
                    drawer.rectangle(i * 20.0, 0.0, 20.0, height * 1.0)
                }
            }

            // 3. 합성: 화면 크기의 사각형에 합성 셰이더 적용
            compositeStyle.parameter("content", contentTarget.colorBuffer(0))
            compositeStyle.parameter("mask", maskTarget.colorBuffer(0))

            drawer.clear(ColorRGBa.fromHex("#222222"))
            drawer.isolated {
                drawer.shadeStyle = compositeStyle
                drawer.fill = ColorRGBa.WHITE
                drawer.stroke = null
                drawer.rectangle(drawer.bounds)
            }
        }
    }
}
```

주의할 점은 다음과 같습니다.

- 콘텐츠 버퍼를 **불투명**하게 그려야 합니다(`clear(BLACK)`). 반투명 콘텐츠는 알파 처리 방식에 따라 색이 어두워질 수 있습니다. 반투명 콘텐츠가 필요하면 C 방식을 권장합니다.
- 위 코드의 `uv.y = 1.0 - uv.y;`는 공식 가이드의 이미지 매핑 예제(`c_boundsPosition`으로 텍스처 좌표를 만들 때 y를 뒤집는 방식)를 따른 것입니다. 결과가 위아래로 뒤집혀 보이면 이 줄을 제거하시기 바랍니다.
- 렌더 타깃 크기가 창과 다르면 각 `isolatedWithTarget` 블록 안에서 `ortho(rt)`를 호출해야 합니다.
- 렌더 타깃과 `ShadeStyle`은 `program { }` 안에서 한 번만 생성하고 재사용합니다.

### 4.6 방식 E: 프래그먼트 셰이더 SDF 마스크

버퍼 없이 셰이더 안에서 도형의 부호 있는 거리 함수(SDF)를 계산하여 알파를 조절합니다. 사각형 하나를 그리면서 그 안에 원↔둥근 사각형이 변하는 모양의 마스크를 적용하는 예입니다.

```kotlin
fun main() = application {
    configure { width = 800; height = 600 }
    program {
        val sdfMask = shadeStyle {
            fragmentPreamble = """
                float rn_sdRoundBox(vec2 p, vec2 b, float r) {
                    vec2 q = abs(p) - b + r;
                    return length(max(q, 0.0)) + min(max(q.x, q.y), 0.0) - r;
                }
            """.trimIndent()

            fragmentTransform = """
                vec2 p = c_boundsPosition.xy - 0.5;                 // 중심이 (0, 0)인 -0.5 ~ 0.5 좌표
                float circleD = length(p) - 0.4;
                float boxD = rn_sdRoundBox(p, vec2(0.4), 0.08);
                float d = mix(circleD, boxD, 0.5 + 0.5 * sin(p_time)); // 원 ↔ 둥근 사각형

                float aa = fwidth(d);                                // 화면 픽셀 기준 경계 폭
                float m = 1.0 - smoothstep(-aa, aa, d);              // 안쪽 1, 바깥쪽 0

                // 마스크 안쪽에 그릴 내용
                x_fill.rgb = mix(vec3(0.99, 0.75, 0.80), vec3(0.12, 0.16, 0.47), c_boundsPosition.y);
                x_fill.a *= m;                                       // 알파로 마스킹
            """.trimIndent()
        }

        extend {
            sdfMask.parameter("time", seconds)
            drawer.clear(ColorRGBa.WHITE)
            drawer.shadeStyle = sdfMask
            drawer.stroke = null
            drawer.rectangle(200.0, 100.0, 400.0, 400.0)
        }
    }
}
```

- 마스크는 그려지는 사각형의 **경계 상자 안**에서만 유효합니다. 마스크가 사각형보다 커지지 않게 좌표 범위를 정해야 합니다.
- `discard`를 사용하면 안티앨리어싱 없이 잘라낼 수 있으나, 위 예제처럼 `smoothstep`으로 알파를 조절하는 방식이 경계 품질 면에서 유리합니다.
- 도형 하나가 SDF로 표현되지 않는 경우(임의의 폴리곤 등)에는 B~D 방식을 사용합니다.

---

## 5. 도형 안에 GLSL 셰이더 넣기 (ShadeStyle)

### 5.1 ShadeStyle의 개념

- `ShadeStyle`은 OPENRNDR가 미리 만들어 둔 셰이더 템플릿에 **GLSL 코드 조각**을 끼워 넣는 방식입니다. 완전한 셰이더 프로그램을 작성하는 것이 아니라 `main()` 내부와 그 앞부분에 코드를 삽입합니다.
- 변환은 두 단계입니다.

| 프로퍼티 | 실행 단계 | 용도 |
| --- | --- | --- |
| `vertexTransform` | 정점 셰이더 | 지오메트리 변형 |
| `fragmentTransform` | 프래그먼트 셰이더 | 픽셀 색 변경 (도형 채움에서 가장 자주 사용) |
| `vertexPreamble` / `fragmentPreamble` | `main()` 앞 | 사용자 함수, `in`/`out` 변수 선언 |

- 적용은 `drawer.shadeStyle = ...`로 합니다. 이후 그려지는 모든 도형(circle, rectangle, contour, shape, 이미지, 텍스트)에 적용되며 `drawer.shadeStyle = null`로 해제합니다.

### 5.2 가장 단순한 예: 색 덮어쓰기

```kotlin
extend {
    drawer.shadeStyle = shadeStyle {
        fragmentTransform = "x_fill.rgb = vec3(1.0, 0.0, 0.0);"
    }
    drawer.fill = ColorRGBa.PINK
    drawer.stroke = null
    drawer.rectangle(width / 2.0 - 200.0, height / 2.0 - 200.0, 400.0, 400.0)
}
```

- `x_fill`은 채움 색, `x_stroke`는 선 색을 담은 `vec4`입니다. 값을 변경하면 그 결과가 최종 출력에 반영됩니다.
- 위 예제에서 `drawer.fill`이 PINK여도 셰이더가 RGB를 덮어쓰므로 빨간색이 출력됩니다. `x_fill.rgb`만 바꾸면 알파(가장자리 처리 등)는 그대로 유지됩니다.

### 5.3 셰이더 언어의 접두사 규칙

| 접두사 | 범위 | 의미 |
| --- | --- | --- |
| `u_` | 전체 | Drawer가 전달하는 시스템 유니폼 (`u_fill`, `u_stroke`, `u_strokeWeight`, `u_viewDimensions` 등) |
| `a_` | vertex | 정점 속성 (`a_position`, `a_normal`, `a_color`) |
| `va_` | fragment | 보간된 정점 속성 (`va_position`, `va_normal`, `va_color`, `va_texCoord0`) |
| `v_` | fragment | 보간된 값 (`v_worldPosition`, `v_viewPosition`, `v_clipPosition` 등) |
| `i_`, `vi_` | vertex / fragment | 인스턴스 속성 |
| `x_` | 전체 | **변경 가능한 값** (`x_fill`, `x_stroke`, `x_position` 등) |
| `p_` | 전체 | 사용자가 `parameter()`로 전달한 값 |
| `o_` | fragment | 출력 값 (항상 `vec4`) |
| `d_` | 전체 | 셰이더 정의 |
| `c_` | 전체 | 상수 (`c_screenPosition`, `c_boundsPosition`, `c_boundsSize`, `c_contourPosition`, `c_element`, `c_instance`) |

- 사용자 함수의 이름은 위 접두사(`x_`, `p_`, `u_` 등)로 시작하지 않게 하고, 프레임워크가 생성하는 이름과 겹치지 않도록 `rn_` 같은 고유 접두사를 붙이는 것이 안전합니다.

### 5.4 도형 안에서 사용할 좌표

도형 안쪽에 패턴을 그리려면 어떤 좌표를 쓸지 먼저 정해야 합니다.

| 좌표 | 범위 | 특징 |
| --- | --- | --- |
| `c_boundsPosition.xy` | 도형의 경계 상자 기준 **0~1** (y=0이 위쪽) | 도형이 어디에 있든, 얼마나 크든 같은 패턴이 도형에 맞춰 배치됩니다. 도형 안쪽 무늬에 가장 적합합니다. |
| `c_boundsSize.xy` | 경계 상자 크기(px) | `c_boundsPosition`과 곱하면 도형 내 픽셀 좌표가 됩니다. 무늬의 물리적 크기를 고정할 때 사용합니다. |
| `c_screenPosition` | 화면 좌표(장치 좌표) | 도형이 움직여도 패턴이 화면에 고정됩니다. 도형이 마치 "창"처럼 화면의 패턴을 보여 주는 효과를 낼 수 있습니다. |
| `va_texCoord0` | 정점 속성 보간 | contour 선에서는 `.x`가 선 폭 방향으로 0→1 변합니다(공식 가이드). |
| `c_contourPosition` | 0 ~ contour 길이 | 선/contour를 그릴 때만 0이 아닙니다. 선을 따라 흐르는 효과에 사용합니다. |

같은 스타일을 서로 다른 도형에 적용하면 `c_boundsPosition`이 도형마다 0~1로 정규화되는 것을 확인할 수 있습니다.

```kotlin
fun main() = application {
    configure { width = 960; height = 400 }
    program {
        val starShape = star(Vector2(780.0, 200.0), 140.0, 60.0, 5).shape
        val uvStyle = shadeStyle {
            fragmentTransform = """
                vec2 uv = c_boundsPosition.xy;
                x_fill.rgb = vec3(uv, 0.5);       // x → 빨강, y → 초록
            """.trimIndent()
        }
        extend {
            drawer.clear(ColorRGBa.BLACK)
            drawer.shadeStyle = uvStyle
            drawer.stroke = null
            drawer.circle(170.0, 200.0, 130.0)
            drawer.rectangle(360.0, 80.0, 240.0, 240.0)
            drawer.shape(starShape)
        }
    }
}
```

### 5.5 파라미터 전달 (Kotlin → GLSL)

- `parameter("이름", 값)`으로 전달한 값은 GLSL에서 `p_이름`으로 접근합니다.
- 지원 타입은 다음과 같습니다.

| Kotlin 타입 | GLSL 타입 |
| --- | --- |
| `Double`(`float`) | `float` |
| `Vector2` / `Vector3` / `Vector4` | `vec2` / `vec3` / `vec4` |
| `ColorRGBa` | `vec4` |
| `Matrix44` | `mat4` |
| `ColorBuffer` / `DepthBuffer` | `sampler2D` |
| `BufferTexture` | `samplerBuffer` |

- `ShadeStyle`은 `program { }` 안에서 **한 번 만들고**, 매 프레임 `style.parameter(...)`로 값만 갱신하는 방식이 효율적입니다. 공식 가이드의 3D 예제도 이 방식을 사용합니다.

```kotlin
fun main() = application {
    configure { width = 800; height = 500 }
    program {
        val waveStyle = shadeStyle {
            fragmentTransform = """
                float w = 0.5 + 0.5 * sin(p_frequency * c_boundsPosition.x * 6.2831853 + p_time);
                x_fill.rgb = mix(p_colorA.rgb, p_colorB.rgb, w);
            """.trimIndent()
        }
        extend {
            waveStyle.parameter("time", seconds)
            waveStyle.parameter("frequency", 4.0)
            waveStyle.parameter("colorA", ColorRGBa.PINK)
            waveStyle.parameter("colorB", ColorRGBa.fromHex("#1E2A78"))

            drawer.clear(ColorRGBa.WHITE)
            drawer.shadeStyle = waveStyle
            drawer.stroke = null
            drawer.circle(width / 2.0, height / 2.0, 200.0)
        }
    }
}
```

### 5.6 fragmentPreamble로 함수 선언 (노이즈 예제)

- `fragmentTransform`은 `main()` 안쪽에 들어가므로 함수를 선언할 수 없습니다. 함수, 상수, `in` 변수는 `fragmentPreamble`에 작성합니다.
- 아래 예제는 값 노이즈(value noise)와 fBm을 preamble에 정의하고, 별 모양 안쪽을 흐르는 구름 무늬로 채웁니다.

```kotlin
fun main() = application {
    configure { width = 800; height = 600 }
    program {
        val starShape = star(Vector2(400.0, 300.0), 260.0, 120.0, 5).shape

        val noiseStyle = shadeStyle {
            fragmentPreamble = """
                float rn_hash21(vec2 p) {
                    p = fract(p * vec2(123.34, 456.21));
                    p += dot(p, p + 45.32);
                    return fract(p.x * p.y);
                }
                float rn_vnoise(vec2 p) {
                    vec2 i = floor(p);
                    vec2 f = fract(p);
                    vec2 u = f * f * (3.0 - 2.0 * f);
                    float a = rn_hash21(i);
                    float b = rn_hash21(i + vec2(1.0, 0.0));
                    float c = rn_hash21(i + vec2(0.0, 1.0));
                    float d = rn_hash21(i + vec2(1.0, 1.0));
                    return mix(mix(a, b, u.x), mix(c, d, u.x), u.y);
                }
                float rn_fbm(vec2 p) {
                    float v = 0.0;
                    float a = 0.5;
                    for (int i = 0; i < 5; i++) {
                        v += a * rn_vnoise(p);
                        p = p * 2.0 + vec2(17.0, 9.0);
                        a *= 0.5;
                    }
                    return v;
                }
            """.trimIndent()

            fragmentTransform = """
                vec2 uv = c_boundsPosition.xy;
                float t = p_time * 0.2;
                float n = rn_fbm(uv * 4.0 + vec2(t, -t));
                x_fill.rgb = mix(p_colorA.rgb, p_colorB.rgb, smoothstep(0.2, 0.8, n));
            """.trimIndent()

            parameter("colorA", ColorRGBa.fromHex("#1E2A78"))
            parameter("colorB", ColorRGBa.fromHex("#FFC0CB"))
        }

        extend {
            noiseStyle.parameter("time", seconds)
            drawer.clear(ColorRGBa.BLACK)
            drawer.shadeStyle = noiseStyle
            drawer.stroke = null
            drawer.shape(starShape)
        }
    }
}
```

### 5.7 선(stroke)에 셰이더 적용: 흐르는 점선

- 선을 그릴 때는 `x_stroke`를 수정하고 `c_contourPosition`을 사용합니다. 아래 예제는 곡선을 따라 이동하는 점선입니다.
- `c_contourPosition`은 0 ~ contour 길이 범위이므로, 0~1로 정규화하려면 contour의 길이를 파라미터로 넘겨 나눕니다.

```kotlin
fun main() = application {
    configure { width = 960; height = 600 }
    program {
        val wave = contour {
            moveTo(Vector2(80.0, 300.0))
            curveTo(Vector2(300.0, 40.0), Vector2(600.0, 560.0), Vector2(880.0, 300.0))
        }

        val dashStyle = shadeStyle {
            fragmentTransform = """
                float s = c_contourPosition / p_len;                  // 0 ~ 1
                float dash = step(0.5, fract(s * 20.0 - p_time * 0.5));
                x_stroke.rgb = mix(p_colorA.rgb, p_colorB.rgb, s);
                x_stroke.a *= dash;
            """.trimIndent()
            parameter("len", wave.length)
            parameter("colorA", ColorRGBa.PINK)
            parameter("colorB", ColorRGBa.fromHex("#4CC9F0"))
        }

        extend {
            dashStyle.parameter("time", seconds)
            drawer.clear(ColorRGBa.fromHex("#111111"))
            drawer.shadeStyle = dashStyle
            drawer.fill = null
            drawer.stroke = ColorRGBa.WHITE
            drawer.strokeWeight = 14.0
            drawer.contour(wave)
        }
    }
}
```

### 5.8 배치 그리기와 c_element

- 배치 그리기에서는 `c_element`가 배치 내 요소의 인덱스입니다(공식 가이드의 상수 정의). 요소마다 위상이 다른 색 변화를 만들 때 사용합니다.

```kotlin
fun main() = application {
    configure { width = 800; height = 600 }
    program {
        val circles = List(60) {
            Circle(80.0 + (it % 10) * 70.0, 100.0 + (it / 10) * 80.0, 28.0)
        }
        val elementStyle = shadeStyle {
            fragmentTransform = """
                float phase = float(c_element) * 0.35;
                x_fill.rgb = 0.5 + 0.5 * cos(vec3(0.0, 2.0, 4.0) + p_time + phase);
            """.trimIndent()
        }
        extend {
            elementStyle.parameter("time", seconds)
            drawer.clear(ColorRGBa.BLACK)
            drawer.shadeStyle = elementStyle
            drawer.stroke = null
            drawer.circles(circles)
        }
    }
}
```

### 5.9 이미지를 도형 안에 매핑하기

- `ColorBuffer` 파라미터를 텍스처로 사용하여 이미지를 도형 안에 채울 수 있습니다. 공식 가이드 예제입니다.

```kotlin
program {
    val image = loadImage("data/images/cheeta.jpg")
    image.filter(MinifyingFilter.LINEAR_MIPMAP_NEAREST, MagnifyingFilter.LINEAR)

    extend {
        drawer.shadeStyle = shadeStyle {
            fragmentTransform = """
                vec2 texCoord = c_boundsPosition.xy;
                texCoord.y = 1.0 - texCoord.y;
                vec2 size = textureSize(p_image, 0);
                texCoord.x /= size.x/size.y;
                x_fill = texture(p_image, texCoord);
            """.trimIndent()
            parameter("image", image)
        }
        val shape = Circle(width / 2.0, height / 2.0, 110.0).shape
        drawer.translate(cos(seconds) * 100.0, sin(seconds) * 100.0)
        drawer.shape(shape)
    }
}
```

- 이 예제에서 도형은 이미지를 통해 보이는 "창"의 역할을 하며, 이는 도형을 마스크로 쓰는 또 다른 방법이기도 합니다.

### 5.10 orx-shade-styles 프리셋

- 3.7절의 `gradient`를 포함하여 `orx-shade-styles`에는 미리 만들어진 스타일이 있습니다. 직접 GLSL을 작성하기 전에 원하는 효과가 프리셋으로 존재하는지 먼저 확인하는 것이 효율적입니다.
- 그라디언트 데모: https://github.com/openrndr/orx/tree/master/orx-shade-styles/src/jvmDemo/kotlin/gradients

### 5.11 디버깅과 자주 발생하는 실수

| 증상 · 원인 | 대응 |
| --- | --- |
| 셰이더 컴파일 오류로 프로그램이 시작되지 않음 | 오류가 나면 프로젝트 루트에 `ShaderError.glsl`이 기록됩니다. 이 파일에서 OPENRNDR가 생성한 최종 셰이더와 사용 가능한 변수를 확인할 수 있습니다. 의도적으로 구문 오류를 넣어 최종 셰이더를 확인하는 방법도 공식 가이드에 소개되어 있습니다. |
| `float`에 정수 리터럴 사용 (`1`) | GLSL에서는 `1.0`처럼 소수점을 붙입니다. |
| `p_이름`을 찾을 수 없음 | Kotlin의 `parameter("이름", …)`과 GLSL의 `p_이름`이 일치해야 하며, 첫 그리기 전에 값이 설정되어 있어야 합니다. |
| 문자열에 `$`가 포함됨 | Kotlin의 `"""` 문자열은 `$`를 템플릿으로 해석합니다. GLSL에 `$`를 쓰지 않도록 하고, 값을 삽입하려면 `${값}` 템플릿을 의도적으로 사용합니다(공식 gradient 예제의 `levelWarpFunction`이 이 방식을 사용). |
| 매 프레임 `shadeStyle { }`를 새로 만들어 느림 | 스타일은 한 번만 만들고 `parameter()`로 갱신합니다. |
| 셰이더를 적용한 뒤 다른 도형도 영향을 받음 | 그린 뒤 `drawer.shadeStyle = null`로 해제하거나 `drawer.isolated { }` 안에서 사용합니다. |
| 마스크/합성 결과가 뒤집힘 | 4.5절의 `uv.y = 1.0 - uv.y;` 줄을 제거하거나 추가합니다. |

---

## 6. 종합 예제

세 종류의 도형에 서로 다른 ShadeStyle을 적용하는 예제입니다.

- 원: 줄무늬 셰이더 (5.5절 방식)
- 별 Shape: 노이즈 셰이더 (5.6절 방식)
- 사각형: SDF 마스크가 적용된 그라디언트 (4.6절 방식)

```kotlin
import org.openrndr.application
import org.openrndr.color.ColorRGBa
import org.openrndr.draw.*
import org.openrndr.math.Vector2
import org.openrndr.shape.*
import kotlin.math.cos
import kotlin.math.sin

fun star(center: Vector2, outer: Double, inner: Double, tips: Int, rotationDeg: Double = -90.0): ShapeContour {
    val points = List(tips * 2) {
        val r = if (it % 2 == 0) outer else inner
        val a = Math.toRadians(rotationDeg) + it * Math.PI / tips
        center + Vector2(cos(a), sin(a)) * r
    }
    return ShapeContour.fromPoints(points, closed = true)
}

fun main() = application {
    configure {
        width = 960
        height = 400
    }
    program {
        val starShape = star(Vector2(480.0, 200.0), 150.0, 65.0, 5).shape

        // 1) 줄무늬 (원)
        val stripeStyle = shadeStyle {
            fragmentTransform = """
                float s = c_boundsPosition.x + c_boundsPosition.y;
                float w = step(0.5, fract(s * p_count - p_time * 0.3));
                x_fill.rgb = mix(p_colorA.rgb, p_colorB.rgb, w);
            """.trimIndent()
            parameter("count", 8.0)
            parameter("colorA", ColorRGBa.fromHex("#FFC0CB"))
            parameter("colorB", ColorRGBa.fromHex("#1E2A78"))
        }

        // 2) 노이즈 (별 Shape)
        val noiseStyle = shadeStyle {
            fragmentPreamble = """
                float rn_hash21(vec2 p) {
                    p = fract(p * vec2(123.34, 456.21));
                    p += dot(p, p + 45.32);
                    return fract(p.x * p.y);
                }
                float rn_vnoise(vec2 p) {
                    vec2 i = floor(p);
                    vec2 f = fract(p);
                    vec2 u = f * f * (3.0 - 2.0 * f);
                    return mix(
                        mix(rn_hash21(i), rn_hash21(i + vec2(1.0, 0.0)), u.x),
                        mix(rn_hash21(i + vec2(0.0, 1.0)), rn_hash21(i + vec2(1.0, 1.0)), u.x),
                        u.y);
                }
                float rn_fbm(vec2 p) {
                    float v = 0.0;
                    float a = 0.5;
                    for (int i = 0; i < 5; i++) {
                        v += a * rn_vnoise(p);
                        p = p * 2.0 + vec2(17.0, 9.0);
                        a *= 0.5;
                    }
                    return v;
                }
            """.trimIndent()
            fragmentTransform = """
                float t = p_time * 0.2;
                float n = rn_fbm(c_boundsPosition.xy * 4.0 + vec2(t, -t));
                x_fill.rgb = mix(p_colorA.rgb, p_colorB.rgb, smoothstep(0.2, 0.8, n));
            """.trimIndent()
            parameter("colorA", ColorRGBa.fromHex("#F94144"))
            parameter("colorB", ColorRGBa.fromHex("#F9C74F"))
        }

        // 3) SDF 마스크 + 그라디언트 (사각형)
        val sdfStyle = shadeStyle {
            fragmentPreamble = """
                float rn_sdRoundBox(vec2 p, vec2 b, float r) {
                    vec2 q = abs(p) - b + r;
                    return length(max(q, 0.0)) + min(max(q.x, q.y), 0.0) - r;
                }
            """.trimIndent()
            fragmentTransform = """
                vec2 p = c_boundsPosition.xy - 0.5;
                float d = mix(length(p) - 0.4, rn_sdRoundBox(p, vec2(0.4), 0.08), 0.5 + 0.5 * sin(p_time));
                float aa = fwidth(d);
                x_fill.rgb = mix(vec3(0.26, 0.67, 0.55), vec3(0.34, 0.46, 0.56), c_boundsPosition.y);
                x_fill.a *= 1.0 - smoothstep(-aa, aa, d);
            """.trimIndent()
        }

        extend {
            val styles = listOf(stripeStyle, noiseStyle, sdfStyle)
            styles.forEach { it.parameter("time", seconds) }

            drawer.clear(ColorRGBa.fromHex("#111111"))
            drawer.stroke = null

            drawer.isolated {
                drawer.shadeStyle = stripeStyle
                drawer.circle(170.0, 200.0, 130.0)
            }
            drawer.isolated {
                drawer.shadeStyle = noiseStyle
                drawer.shape(starShape)
            }
            drawer.isolated {
                drawer.shadeStyle = sdfStyle
                drawer.rectangle(680.0, 70.0, 260.0, 260.0)
            }
        }
    }
}
```

---

## 7. 자주 쓰는 패턴 요약

| 하고 싶은 일 | 사용할 기능 |
| --- | --- |
| 채움만 / 선만 그리기 | `fill = null` 또는 `stroke = null` |
| 도형별로 다른 색 | 그리기 직전에 `drawer.fill` 변경 |
| 임의의 다각형·별 | `ShapeContour.fromPoints(points, closed = true)` |
| 구멍이 있는 도형 | `shape { contour { } contour { } }` 또는 `compound { difference { } }` |
| 도형끼리 자르기·합치기 | `compound { union / difference / intersection }` |
| 수천 개의 도형 | `drawer.circles(list)`, `drawer.rectangles { }` |
| 직사각형으로 잘라내기 | `drawer.drawStyle.clip` |
| 임의 도형/이미지로 마스킹 | `orx-compositor`의 `blend(Normal()) { clip = true }` |
| 마스크 합성식을 직접 제어 | RenderTarget 2장 + ShadeStyle |
| 수식으로 정의된 마스크 | 프래그먼트 셰이더 SDF |
| 도형 안 애니메이션 무늬 | `fragmentTransform` + `p_time` |
| 선을 따라 흐르는 효과 | `c_contourPosition` + `x_stroke` |
| 배치 도형마다 다른 색 | `c_element` |
| 그라디언트 | `orx-shade-styles`의 `gradient<ColorRGBa> { }` |

---

## 8. 성능 관점의 정리

1. **객체 생성 위치**: `ShapeContour`, `Shape`, `compound` 결과, `ShadeStyle`, `RenderTarget`은 입력이 바뀌지 않는 한 `extend` 바깥에서 한 번만 생성합니다.
2. **배치 그리기**: 같은 종류의 도형이 수백 개 이상이면 `circles`/`rectangles` 배치 API를 사용합니다.
3. **마스크 방식 선택**: 정적인 마스크는 B(벡터 교집합)로 미리 계산하고, 움직이는 마스크는 C~E 방식을 사용합니다.
4. **셰이더 비용**: fBm의 반복 횟수, 마스크 SDF의 복잡도는 도형이 차지하는 픽셀 수에 비례하여 비용이 증가합니다. 큰 도형에 무거운 셰이더를 적용할 때는 반복 횟수를 줄입니다.
5. **상태 누수 방지**: `shadeStyle`, `clip`, `blendMode`는 다음 그리기에도 유지됩니다. `isolated { }`로 범위를 제한합니다.

---

## 9. 문서에서 다루지 않은 항목

- 텍스트, 이미지, SVG 그리기 (도형 범위 밖)
- 3D 메시와 `vertexTransform` 기반 지오메트리 변형
- `StencilStyle`을 이용한 스텐실 마스킹 (가이드에 예제가 없어 제외)
- Compute shader, 이미지 필터 체인(`orx-fx`의 블러 등 후처리 상세)

---

## 10. 참고 자료

공식 가이드 (guide.openrndr.org)

- Drawing circles, rectangles and lines: https://guide.openrndr.org/drawing/circlesRectanglesLines.html
- Color: https://guide.openrndr.org/drawing/color.html
- Managing draw style: https://guide.openrndr.org/drawing/managingDrawStyle.html
- Curves and shapes: https://guide.openrndr.org/drawing/curvesAndShapes.html
- Drawing primitives batched: https://guide.openrndr.org/drawing/drawingPrimitivesBatched.html
- Transformations: https://guide.openrndr.org/drawing/transformations.html
- Render targets: https://guide.openrndr.org/drawing/renderTargets.html
- Clipping: https://guide.openrndr.org/drawing/clipping.html
- Shade styles: https://guide.openrndr.org/drawing/shadeStyles.html
- Compositor (ORX): https://guide.openrndr.org/ORX/compositor.html
- Shade style presets (ORX): https://guide.openrndr.org/ORX/shadeStylePresets.html

릴리즈·API

- OPENRNDR 0.5.0 릴리즈 노트: https://github.com/openrndr/openrndr/releases/tag/v0.5.0
- OPENRNDR 0.5.0 공지: https://openrndr.discourse.group/t/openrndr-0-5-0-is-out/786
- API 문서 (openrndr-draw): https://api.openrndr.org/openrndr-draw/org.openrndr.draw/
- 예제 저장소: https://github.com/openrndr/openrndr-examples

---

## 11. 0.5.0 마이그레이션 시 확인할 점

- 0.4.x 코드에서 도형 그리기 부분은 대체로 그대로 동작할 것으로 예상되지만, 릴리즈 노트만으로 이를 보증할 수 없습니다. 컴파일 오류가 나면 API 문서에서 해당 함수의 시그니처를 확인하시기 바랍니다.
- 창 생성이 SDL3 기반으로 바뀌었으므로, 그리기 결과가 아닌 **창 동작**(콘텐츠 스케일, 입력 이벤트 등)에서 차이가 나타날 수 있습니다. GLFW 백엔드는 deprecated 상태로 남아 있습니다.
- macOS에서 Angle 기반 GLES 백엔드를 사용하며 shape/contour fill이 깨져 보이던 경우 0.5.0에서 수정되었습니다.

---

## 12. 검증 메모

이 문서를 발행하기 전에 실행 환경에서 확인이 필요한 항목입니다.

| 항목 | 내용 |
| --- | --- |
| 코드 실행 여부 | 공식 가이드의 예제(2.2, 2.6.1, 2.6.3, 2.6.5, 3.3, 3.7, 4.2, 5.2, 5.9 등)를 기반으로 하되, 본문의 조합·확장 코드(정다각형/별 헬퍼, 4.3, 4.4, 4.5, 4.6, 5.6~5.8, 6장)는 0.5.0에서 직접 컴파일·실행하지 않았습니다. |
| ORX import 경로 | `gradient`, `compose`, `Normal`, `ApproximateGaussianBlur`, `hobbyCurve`의 패키지는 IDE 자동 import로 확인하시기 바랍니다. |
| `blendMode` 기본값 | 가이드 표는 `OVER`, 0.5.0 API 문서는 `BlendMode? = null`입니다. 명시적으로 지정하면 문제가 없습니다. |
| 렌더 타깃 y 방향 | 4.5절의 `uv.y = 1.0 - uv.y;`는 이미지 매핑 예제의 방식을 적용한 것입니다. 결과가 뒤집히면 제거합니다. |
| compositor 후처리 순서 | 4.4절에서 마스크 레이어에 블러를 결합했을 때의 결과는 실행하여 확인해야 합니다. |
| `c_element` | 배치 그리기 시 인덱스 동작은 가이드의 상수 정의에 근거합니다. `circles(list)`에서의 동작은 실행으로 확인해야 합니다. |
| API 문서 버전 표기 | API 문서 사이트는 `0.5.0-dev.2+6c5525b`로 표시되며, 이 식별자는 v0.5.0 릴리즈 커밋(`6c5525b`)과 일치합니다. |
