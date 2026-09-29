# OPENRNDR 시작하기 (10): Object와 싱글턴 — 전역 상태 관리

지금까지 우리는 **여러 개의 인스턴스**를 만들었습니다.

```kotlin
val particle1 = Particle(...)
val particle2 = Particle(...)
val particle3 = Particle(...)
```

하지만 때로는 **정확히 하나의 인스턴스만 필요한 경우**가 있습니다.

예를 들어:
- **색상 팔레트** — 전체 프로젝트에서 같은 색상 사용
- **설정 값** — 프로그램 전체에서 공유하는 파라미터
- **리소스 관리** — 폰트, 이미지 캐시
- **로거** — 하나의 로그 시스템

이것을 **싱글턴(Singleton)** 패턴이라고 합니다.

이 글에서는:
- **싱글턴이란 무엇인가**
- **`object` 키워드로 싱글턴 만들기**
- **OPENRNDR에서의 실제 패턴**

을 다루겠습니다.

---

## 싱글턴이란?

**싱글턴**은 **프로그램 전체에서 단 하나의 인스턴스만 존재하는 객체**입니다.

### 문제: 색상을 매번 정의하면?

```kotlin
// 색상이 여러 곳에서 다르게 정의됨
extend {
    fill = ColorRGBa(1.0, 0.0, 0.0)  // 빨강
    circle(100.0, 100.0, 50.0)
}

extend {
    fill = ColorRGBa(1.0, 0.0, 0.0)  // 같은 빨강, 하지만 다시 정의
    circle(200.0, 200.0, 50.0)
}

// 만약 색을 바꾸려면?
// 모든 곳에서 수정해야 함... 위험함!
```

### 해결: 싱글턴으로 중앙 관리

```kotlin
object ColorPalette {
    val primary = ColorRGBa(1.0, 0.0, 0.0)
    val secondary = ColorRGBa(0.0, 0.0, 1.0)
}

// 어디서나 같은 색상 사용
extend {
    fill = ColorPalette.primary
    circle(100.0, 100.0, 50.0)
}

extend {
    fill = ColorPalette.primary  // 자동으로 같은 색상
    circle(200.0, 200.0, 50.0)
}

// 색을 바꾸려면 한 곳만 수정
// object ColorPalette의 primary 값을 바꾸면 끝!
```

---

## `object` 키워드

코틀린에서는 **`object` 키워드로 싱글턴을 만듭니다.**

```kotlin
object Settings {
    val width = 800
    val height = 800
    val frameRate = 60
}

// 사용
println(Settings.width)   // 800
println(Settings.height)  // 800
```

### 생성자가 없습니다

```kotlin
object Database {
    init {
        println("Database initialized once!")  // 프로그램 실행 중 딱 한 번
    }
}

// 사용할 때 자동으로 초기화
val db = Database  // ✅ OK, "Database initialized once!" 출력
val db2 = Database // 같은 인스턴스, 출력 안 됨
```

### 프로퍼티와 메서드

```kotlin
object GameState {
    var score = 0
    var level = 1
    var isPaused = false
    
    fun reset() {
        score = 0
        level = 1
        isPaused = false
    }
    
    fun addScore(points: Int) {
        score += points
    }
}

// 사용
GameState.score = 100
GameState.addScore(50)
println(GameState.score)  // 150
GameState.reset()
println(GameState.score)  // 0
```

---

## p5.js와의 비교

### p5.js: 전역 변수

```javascript
// 전역 네임스페이스에 직접 정의
let primaryColor = color(255, 0, 0);
let secondaryColor = color(0, 0, 255);

function setup() {
    createCanvas(800, 800);
}

function draw() {
    fill(primaryColor);
    circle(100, 100, 50);
}

// 문제: 어디서나 primaryColor를 수정할 수 있음 (실수하기 쉬움)
primaryColor = color(0, 255, 0);  // 예상 밖의 변경!
```

**문제점:**
- 전역 변수가 언제 어디서 변경될지 알 수 없음
- 네임스페이스 오염
- 테스트 어려움

### OPENRNDR: 싱글턴 객체

```kotlin
object ColorPalette {
    val primary = ColorRGBa.RED
    val secondary = ColorRGBa.BLUE
}

program {
    extend {
        fill = ColorPalette.primary
        circle(100.0, 100.0, 50.0)
    }
}

// ColorPalette.primary = ColorRGBa.GREEN  // ❌ 에러! val이므로 변경 불가
```

**장점:**
- 명시적인 싱글턴 (object로 분명함)
- 불변성 (`val`로 변경 방지)
- 조직화된 네임스페이스

---

## 실전 예제 1: 색상 팔레트

```kotlin
object ColorPalette {
    // 기본 색상
    val background = ColorRGBa.BLACK
    val foreground = ColorRGBa.WHITE
    
    // 주제 색상
    val primary = ColorRGBa.fromHex(0xFF6B6B)     // 붉은색
    val secondary = ColorRGBa.fromHex(0x4ECDC4)  // 청록색
    val accent = ColorRGBa.fromHex(0xFFE66D)     // 노란색
    
    // 그래디에이션
    val colors = listOf(
        ColorRGBa.RED,
        ColorRGBa.YELLOW,
        ColorRGBa.GREEN,
        ColorRGBa.CYAN,
        ColorRGBa.BLUE
    )
}

program {
    extend {
        clear(ColorPalette.background)
        
        // 팔레트 사용
        fill = ColorPalette.primary
        circle(150.0, 150.0, 50.0)
        
        fill = ColorPalette.secondary
        circle(650.0, 150.0, 50.0)
        
        fill = ColorPalette.accent
        circle(400.0, 600.0, 50.0)
        
        // 색 리스트 사용
        for (i in 0 until 10) {
            val color = ColorPalette.colors[i % ColorPalette.colors.size]
            fill = color
            circle(i * 80.0, 300.0, 20.0)
        }
    }
}
```

---

## 실전 예제 2: 게임 설정

```kotlin
object GameSettings {
    // 화면 설정
    val screenWidth = 800
    val screenHeight = 600
    val targetFPS = 60
    
    // 게임 설정
    val maxParticles = 1000
    val defaultParticleLife = 100.0
    val particleSpawnRate = 5
    
    // 난이도 설정
    val difficulty = "NORMAL"  // "EASY", "NORMAL", "HARD"
    val enemySpeed = when (difficulty) {
        "EASY" -> 1.0
        "NORMAL" -> 2.0
        "HARD" -> 3.0
        else -> 2.0
    }
    
    // 디버그
    val showDebugInfo = true
}

program {
    configure {
        width = GameSettings.screenWidth
        height = GameSettings.screenHeight
    }
    
    program {
        val particles = mutableListOf<Particle>()
        
        extend {
            clear(ColorRGBa.BLACK)
            
            // 파티클 생성 (설정에 따라)
            if (particles.size < GameSettings.maxParticles) {
                repeat(GameSettings.particleSpawnRate) {
                    particles.add(Particle(
                        position = Vector2(random(width.toDouble()), random(height.toDouble())),
                        velocity = Vector2(random(-2.0, 2.0), random(-2.0, 2.0)),
                        lifespan = GameSettings.defaultParticleLife
                    ))
                }
            }
            
            particles.forEach { p ->
                p.update()
                p.draw(drawer)
            }
            
            particles.removeIf { !it.isAlive }
            
            // 디버그 정보 표시
            if (GameSettings.showDebugInfo) {
                fill = ColorRGBa.YELLOW
                text("Particles: ${particles.size}", 20.0, 30.0)
                text("Difficulty: ${GameSettings.difficulty}", 20.0, 60.0)
            }
        }
    }
}
```

---

## 실전 예제 3: 카메라 설정

```kotlin
object CameraSettings {
    var posX = 0.0
    var posY = 0.0
    var zoom = 1.0
    
    fun reset() {
        posX = 0.0
        posY = 0.0
        zoom = 1.0
    }
    
    fun moveBy(dx: Double, dy: Double) {
        posX += dx
        posY += dy
    }
    
    fun zoomIn() {
        zoom *= 1.1
    }
    
    fun zoomOut() {
        zoom /= 1.1
    }
    
    fun apply(drawer: Drawer) {
        drawer.translate(-posX, -posY)
        drawer.scale(zoom)
    }
}

program {
    extend {
        clear(ColorRGBa.BLACK)
        
        // 키보드로 카메라 제어
        when {
            keyboard.pressedKeys.contains(KEY_up) -> CameraSettings.moveBy(0.0, -10.0)
            keyboard.pressedKeys.contains(KEY_down) -> CameraSettings.moveBy(0.0, 10.0)
            keyboard.pressedKeys.contains(KEY_left) -> CameraSettings.moveBy(-10.0, 0.0)
            keyboard.pressedKeys.contains(KEY_right) -> CameraSettings.moveBy(10.0, 0.0)
            keyboard.pressedKeys.contains(KEY_w) -> CameraSettings.zoomIn()
            keyboard.pressedKeys.contains(KEY_s) -> CameraSettings.zoomOut()
            keyboard.pressedKeys.contains(KEY_r) -> CameraSettings.reset()
        }
        
        // 카메라 적용
        CameraSettings.apply(drawer)
        
        // 장면 그리기
        fill = ColorRGBa.WHITE
        circle(0.0, 0.0, 100.0)
        circle(300.0, 300.0, 80.0)
        circle(-300.0, -300.0, 60.0)
    }
}
```

---

## 실전 예제 4: 키 바인딩 설정

```kotlin
object KeyBindings {
    // 게임 제어
    val moveUp = listOf(KEY_w, KEY_up)
    val moveDown = listOf(KEY_s, KEY_down)
    val moveLeft = listOf(KEY_a, KEY_left)
    val moveRight = listOf(KEY_d, KEY_right)
    
    // 액션
    val action1 = KEY_space
    val action2 = KEY_shift
    
    // UI
    val pause = KEY_p
    val menu = KEY_escape
    val debug = KEY_f1
    
    fun isMovingUp(keys: Set<Int>) = keys.any { it in moveUp }
    fun isMovingDown(keys: Set<Int>) = keys.any { it in moveDown }
    fun isMovingLeft(keys: Set<Int>) = keys.any { it in moveLeft }
    fun isMovingRight(keys: Set<Int>) = keys.any { it in moveRight }
}

program {
    var playerX = 400.0
    var playerY = 400.0
    
    extend {
        clear(ColorRGBa.BLACK)
        
        val keys = keyboard.pressedKeys
        
        // 키 바인딩 사용
        if (KeyBindings.isMovingUp(keys)) playerY -= 5.0
        if (KeyBindings.isMovingDown(keys)) playerY += 5.0
        if (KeyBindings.isMovingLeft(keys)) playerX -= 5.0
        if (KeyBindings.isMovingRight(keys)) playerX += 5.0
        
        fill = ColorRGBa.WHITE
        circle(playerX, playerY, 20.0)
    }
}
```

---

## 실전 예제 5: 리소스 관리

```kotlin
// 주의: 실제 OPENRNDR에서는 더 복잡한 리소스 관리가 필요합니다.
// 이 예제는 개념을 보여주기 위한 것입니다.

object Resources {
    // 프로그램 실행 중 로드되는 리소스
    private val _colors = mutableMapOf<String, ColorRGBa>()
    private val _patterns = mutableMapOf<String, (Drawer) -> Unit>()
    
    init {
        // 색상 등록
        _colors["sky"] = ColorRGBa.LIGHT_GRAY
        _colors["sea"] = ColorRGBa.BLUE
        _colors["sand"] = ColorRGBa.YELLOW
        
        // 패턴 등록
        _patterns["grid"] = { drawer ->
            for (i in 0 until 10) {
                drawer.line(i * 80.0, 0.0, i * 80.0, 800.0)
                drawer.line(0.0, i * 80.0, 800.0, i * 80.0)
            }
        }
        
        _patterns["dots"] = { drawer ->
            for (x in 0 until 10) {
                for (y in 0 until 10) {
                    drawer.circle(x * 80.0, y * 80.0, 5.0)
                }
            }
        }
    }
    
    fun getColor(name: String): ColorRGBa? = _colors[name]
    fun getPattern(name: String): ((Drawer) -> Unit)? = _patterns[name]
    
    fun listColors() = _colors.keys.toList()
    fun listPatterns() = _patterns.keys.toList()
}

program {
    extend {
        clear(Resources.getColor("sky") ?: ColorRGBa.BLACK)
        
        stroke = Resources.getColor("sea") ?: ColorRGBa.WHITE
        strokeWeight = 2.0
        
        // 패턴 그리기
        Resources.getPattern("grid")?.invoke(drawer)
        
        // 또는
        Resources.listPatterns().forEach { name ->
            println("Available pattern: $name")
        }
    }
}
```

---

## Companion Object: 클래스 내의 싱글턴

때로는 **특정 클래스와 함께하는 싱글턴**이 필요합니다.

```kotlin
data class Particle(
    var position: Vector2,
    var velocity: Vector2
) {
    companion object {
        // Particle 클래스와 함께하는 싱글턴
        val defaultRadius = 10.0
        val defaultLifespan = 100.0
        
        fun createRandom(width: Double, height: Double): Particle {
            return Particle(
                position = Vector2(random(width), random(height)),
                velocity = Vector2(random(-2.0, 2.0), random(-2.0, 2.0))
            )
        }
    }
}

// 사용
val p = Particle.createRandom(800.0, 600.0)
println(Particle.defaultRadius)
```

---

## 싱글턴의 장점과 주의점

### 장점

| 장점 | 설명 |
|------|------|
| **중앙 관리** | 한 곳에서 모든 설정/리소스 관리 |
| **일관성** | 전체 프로그램에서 같은 값 사용 |
| **수정 용이** | 한 곳만 수정하면 모두 반영 |
| **명시성** | `object`로 싱글턴임이 분명 |

### 주의점

```kotlin
// ❌ 주의: 싱글턴의 상태가 변경되면 전체에 영향
object Settings {
    var difficulty = "NORMAL"  // var은 변경 가능
}

// 어디선가 변경하면
Settings.difficulty = "HARD"

// 다른 곳에서 갑자기 난이도가 바뀜!

// ✅ 권장: val로 불변으로 만들기
object Settings {
    val difficulty = "NORMAL"  // val은 불변
}

// 또는 필요하면 메서드로 제어
object Settings {
    private var _difficulty = "NORMAL"
    
    fun setDifficulty(level: String) {
        if (level in listOf("EASY", "NORMAL", "HARD")) {
            _difficulty = level
        }
    }
    
    fun getDifficulty() = _difficulty
}
```

---

## 실전 예제 6: 복합 싱글턴

```kotlin
object GameEngine {
    // 게임 상태
    object State {
        var isRunning = false
        var isPaused = false
        var level = 1
    }
    
    // 색상
    object Colors {
        val background = ColorRGBa.BLACK
        val player = ColorRGBa.GREEN
        val enemy = ColorRGBa.RED
    }
    
    // 설정
    object Config {
        val screenWidth = 800
        val screenHeight = 600
        val playerSpeed = 5.0
        val enemySpeed = 3.0
    }
    
    // 유틸리티 메서드
    fun reset() {
        State.isRunning = false
        State.isPaused = false
        State.level = 1
    }
    
    fun pause() {
        State.isPaused = !State.isPaused
    }
    
    fun nextLevel() {
        State.level++
    }
}

program {
    configure {
        width = GameEngine.Config.screenWidth
        height = GameEngine.Config.screenHeight
    }
    
    program {
        GameEngine.State.isRunning = true
        
        extend {
            clear(GameEngine.Colors.background)
            
            if (keyboard.pressedKeys.contains(KEY_p)) {
                GameEngine.pause()
            }
            
            if (!GameEngine.State.isPaused) {
                fill = GameEngine.Colors.player
                text("Level: ${GameEngine.State.level}", 20.0, 30.0)
                
                if (keyboard.pressedKeys.contains(KEY_n)) {
                    GameEngine.nextLevel()
                }
            } else {
                fill = ColorRGBa.YELLOW
                text("PAUSED", 350.0, 300.0)
            }
        }
    }
}
```

---

## 싱글턴과 메모리

**좋은 소식:** 싱글턴은 메모리 효율적입니다.

```kotlin
object Settings {
    val colors = listOf(ColorRGBa.RED, ColorRGBa.BLUE, ColorRGBa.GREEN)
    // colors 리스트는 메모리에 단 한 번만 로드됨
}

// 여러 곳에서 접근해도 같은 리스트
println(Settings.colors)  // 첫 번째 접근: 메모리 로드
println(Settings.colors)  // 두 번째 접근: 이미 로드된 것 사용
```

---

## 정리: 싱글턴 사용 가이드

| 상황 | 싱글턴 사용? | 예제 |
|------|-----------|------|
| 전역 색상 팔레트 | ✅ 사용 | `object ColorPalette` |
| 게임 설정 | ✅ 사용 | `object GameSettings` |
| 리소스 캐시 | ✅ 사용 | `object Resources` |
| 카메라 상태 | ✅ 사용 | `object CameraSettings` |
| 여러 입자 | ❌ 사용 안 함 | `mutableListOf<Particle>()` |
| 여러 플레이어 | ❌ 사용 안 함 | `listOf<Player>()` |

---

## 다음 단계: Scope 함수

다음 글에서는 **Scope 함수**를 다룹니다.

Scope 함수는 객체의 범위(scope) 안에서만 유효한 함수들입니다:

```kotlin
val settings = Settings().apply {
    width = 800
    height = 600
    frameRate = 60
}

val result = mutableListOf<Int>().also { list ->
    list.add(1)
    list.add(2)
    list.add(3)
}
```

이들은 객체 초기화와 체이닝을 매우 깔끔하게 만듭니다.

---

## 연습 문제

### 1. 기본 싱글턴

```kotlin
object ThemeSettings {
    val darkMode = true
    val fontSize = 14
    val fontFamily = "Arial"
}

// 사용
println(ThemeSettings.fontSize)
```

### 2. 색상 팔레트

```kotlin
object MyPalette {
    val bg = ColorRGBa.BLACK
    val primary = ColorRGBa.RED
    val secondary = ColorRGBa.BLUE
    val accent = ColorRGBa.YELLOW
}

extend {
    clear(MyPalette.bg)
    fill = MyPalette.primary
    circle(400.0, 400.0, 100.0)
}
```

### 3. 게임 설정

```kotlin
object GameConfig {
    val maxEnemies = 10
    val playerHealth = 100
    val enemyDamage = 10.0
    
    fun calculateTotalDamage(numEnemies: Int): Double {
        return numEnemies * enemyDamage
    }
}

extend {
    val totalDamage = GameConfig.calculateTotalDamage(GameConfig.maxEnemies)
    text("Total Damage: $totalDamage", 20.0, 30.0)
}
```

### 4. OPENRNDR: 상태 싱글턴

```kotlin
object AppState {
    var selectedTool = "draw"
    var brushSize = 5.0
    var brushColor = ColorRGBa.WHITE
    
    fun setTool(tool: String) {
        selectedTool = tool
    }
}

program {
    extend {
        clear(ColorRGBa.BLACK)
        
        when {
            keyboard.pressedKeys.contains(KEY_1) -> AppState.setTool("draw")
            keyboard.pressedKeys.contains(KEY_2) -> AppState.setTool("erase")
            keyboard.pressedKeys.contains(KEY_3) -> AppState.setTool("select")
        }
        
        fill = ColorRGBa.YELLOW
        text("Current Tool: ${AppState.selectedTool}", 20.0, 30.0)
    }
}
```

### 5. 심화: 복합 싱글턴 시스템

지난 "복합 싱글턴" 예제를 확장해서:
- 더 많은 게임 상태 추가
- 레벨별 다른 설정
- 점수 시스템 추가

---

**이전 글**: [OPENRNDR 시작하기 (9): 클래스와 데이터 클래스 — 상태 관리](./openrndr_09_classes_dataclasses.md)  
**다음 글**: OPENRNDR 시작하기 (11): Scope 함수 — `apply`, `run`, `let`, `also`
