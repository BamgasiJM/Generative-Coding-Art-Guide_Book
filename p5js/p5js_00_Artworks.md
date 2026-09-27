# Creative Code Expert

## 📑 목차

1. [infographic\_01\_moving](#001-infographic_01_moving)
   * `251119_infographic\infographic_01_moving.js`
2. [infographic\_01\_static](#002-infographic_01_static)
   * `251119_infographic\infographic_01_static.js`
3. [mold](#003-mold)
   * `260114_slime_mold_patt_vira\mold.js`
4. [opt\_art\_circle](#004-opt_art_circle)
   * `251120_op_art\opt_art_circle.js`
5. [opt\_art\_sine\_curve](#005-opt_art_sine_curve)
   * `251120_op_art\opt_art_sine_curve.js`
6. [script](#006-script)
   * `250000_PRACTICE\script.js`
7. [script](#007-script)
   * `251125_maze_game\script.js`
8. [script](#008-script)
   * `251126_6lines_art\script.js`
9. [script](#009-script)
   * `251126_particle_collision\script.js`
10. [script](#010-script)
* `251126_particle_contagion\script.js`
11. [script](#011-script)
* `251126_revolving_circles_by_garabatospr\script.js`
12. [script](#012-script)
* `251126_sprouts_by_garabatospr\script.js`
13. [script](#013-script)
* `251129_slime_mold_simulation\script.js`
14. [script](#014-script)
* `251129_star_size_comparison\script.js`
15. [script](#015-script)
* `251130_blooming_flowers\script.js`
16. [script](#016-script)
* `251130_wobbling_anemone\script.js`
17. [script](#017-script)
* `251203_tadpole\script.js`
18. [script](#018-script)
* `251207_foam_bubble_density\script.js`
19. [script](#019-script)
* `251215_geometric_tiles\script.js`
20. [script](#020-script)
* `251215_geometric_wave\script.js`
21. [script](#021-script)
* `251219_map_constrain\script.js`
22. [script](#022-script)
* `251219_sound_interaction\script.js`
23. [script](#023-script)
* `251221_greeting_dots\script.js`
24. [script](#024-script)
* `251221_make_your_constellation\script.js`
25. [script](#025-script)
* `251221_particle_flower_blooming\script.js`
26. [script](#026-script)
* `251222_pointing_triangle\script.js`
27. [script](#027-script)
* `251222_shooting_marbles\script.js`
28. [script](#028-script)
* `251223_arc_animation\script.js`
29. [script](#029-script)
* `251223_fake_sphere_particle\script.js`
30. [script](#030-script)
* `251226_2d_3d_allinone\script.js`
31. [script](#031-script)
* `260102_vanishing_picture_particle\script.js`
32. [script](#032-script)
* `260102_variable_rectangles\script.js`
33. [script](#033-script)
* `260111_gravity_comparison\script.js`
34. [script](#034-script)
* `260114_slime_mold_patt_vira\script.js`
35. [script](#035-script)
* `260115_slime_mold_2\script.js`
36. [script](#036-script)
* `260406_simple_dot_torus\script.js`
37. [script\_1](#037-script_1)
* `251222_tile_grid_animation\script_1.js`
38. [script\_1](#038-script_1)
* `251226_spirograph_cookies\script_1.js`
39. [script\_2](#039-script_2)
* `251222_tile_grid_animation\script_2.js`
40. [script\_2](#040-script_2)
* `251223_arc_animation\script_2.js`
41. [script\_2](#041-script_2)
* `251226_spirograph_cookies\script_2.js`
42. [script\_3](#042-script_3)
* `251226_spirograph_cookies\script_3.js`
43. [script\_blue\_noise](#043-script_blue_noise)
* `251214_exploding_blackhole\script_blue_noise.js`
44. [script\_class](#044-script_class)
* `251215_geometric_tiles\script_class.js`
45. [script\_comment](#045-script_comment)
* `251222_pointing_triangle\script_comment.js`
46. [script\_COMMENT](#046-script_comment)
* `251223_fake_sphere_particle\script_COMMENT.js`
47. [script\_develop](#047-script_develop)
* `251126_6lines_art\script_develop.js`
48. [script\_exploding\_blackhole](#048-script_exploding_blackhole)
* `251214_exploding_blackhole\script_exploding_blackhole.js`
49. [script\_exploding\_blackhole\_instance](#049-script_exploding_blackhole_instance)
* `251214_exploding_blackhole\script_exploding_blackhole_instance.js`
50. [script\_old](#050-script_old)
* `251215_geometric_tiles\script_old.js`
51. [script\_red\_rotating](#051-script_red_rotating)
* `251214_exploding_blackhole\script_red_rotating.js`
52. [script\_sound\_vis\_1](#052-script_sound_vis_1)
* `251223_keyboard_sound_visualizer\script_sound_vis_1.js`
53. [script\_sound\_vis\_2](#053-script_sound_vis_2)
* `251223_keyboard_sound_visualizer\script_sound_vis_2.js`

***

## 1. infographic\_01\_moving

**파일 경로:** `251119_infographic\infographic_01_moving.js`

**설명:** infographic\_01\_moving.js - p5.js 스케치

```javascript
let numConcentric = 20;
let numRays = 60;
let maxRadius;
let rotationAngle = 0;
let rayData = []; // 각 선의 정보를 저장할 배열

function setup() {
  createCanvas(1080, 1080);
  maxRadius = width * 0.45;

  // 각 선의 데이터 미리 생성
  for (let i = 0; i < numRays; i++) {
    let baseAngle = map(i, 0, numRays, 0, TWO_PI);
    let len = random(30, maxRadius);

    // 이 선 위에 그려질 원들의 정보
    let circles = [];
    let steps = int(random(3, 8));
    for (let j = 1; j <= steps; j++) {
      let t = j / (steps + 1);
      let distance = len * t; // 중심으로부터의 거리

      // 중심에서 30px 이내면 제외
      if (distance < 30) continue;

      circles.push({
        distance: distance,
        size: random(2, 8),
        color:
          random(100) > 85
            ? color(220, 180, 80, 200)
            : color(240, 240, 240, 180),
      });
    }

    rayData.push({
      baseAngle: baseAngle,
      length: len,
      circles: circles,
    });
  }
}

function draw() {
  background(40, 30, 40);

  // 동심원 그리기
  noFill();
  stroke(100, 90, 100, 180);
  strokeWeight(0.5);
  for (let i = 1; i <= numConcentric; i++) {
    let r = map(i, 1, numConcentric, 20, maxRadius);
    circle(width / 2, height / 2, r * 2);
  }

  // 중심부 강조
  noFill();
  stroke(255, 255, 255, 100);
  strokeWeight(1.5);
  circle(width / 2, height / 2, 60);

  // 회전 각도 업데이트
  rotationAngle = (millis() * 0.05) / 1000;

  let cx = width / 2;
  let cy = height / 2;

  // 저장된 데이터로 선과 원 그리기
  for (let ray of rayData) {
    let angle = ray.baseAngle + rotationAngle;

    // 선 끝점 계산
    let x2 = cx + cos(angle) * ray.length;
    let y2 = cy + sin(angle) * ray.length;

    // 선 그리기
    stroke(200, 200, 220, 150);
    line(cx, cy, x2, y2);

    // 원들 그리기
    noStroke();
    for (let c of ray.circles) {
      let px = cx + cos(angle) * c.distance;
      let py = cy + sin(angle) * c.distance;

      fill(c.color);
      circle(px, py, c.size);
    }
  }
}

```

***

## 2. infographic\_01\_static

**파일 경로:** `251119_infographic\infographic_01_static.js`

**설명:** infographic\_01\_static.js - p5.js 스케치

```javascript
// ─────────────────────────────────────────────────────────────
// 전역 변수 선언 (모든 함수에서 접근 가능한 설정값)
// ─────────────────────────────────────────────────────────────

let numConcentric = 20; // 동심원 개수: 20개
let numRays = 60;       // 방사형 선 개수: 60개
let maxRadius;          // 최대 반지름 변수 설정. setup()에서 실제 값 할당.

// ─────────────────────────────────────────────────────────────
// setup(): p5.js의 초기화 함수. 프로그램 시작 시 딱 한 번 실행됨.
// → 모든 그림을 여기서 한 번만 그립니다.
// ─────────────────────────────────────────────────────────────

function setup() {
  // 캔버스 크기 설정: 가로 1080px × 세로 1080px (정사각형)
  createCanvas(1080, 1080);

  // 최대 반지름 계산: 캔버스 가로 너비(width)의 45% → 여백 확보 (10%는 테두리 공간)
  // 예: width=1080 → maxRadius = 1080 × 0.45 = 486px
  maxRadius = width * 0.45;

  // 캔버스 중심 좌표 → 반복 사용되므로 변수로 저장 (코드 가독성 ↑, 성능 ↑)
  let cx = width / 2; // 중심 x = 1080 / 2 = 540
  let cy = height / 2; // 중심 y = 1080 / 2 = 540

  // ───────────────────────────────────────────────────────────
  // 배경색 설정: RGB + Alpha (투명도)
  // (40, 30, 40) → 짙은 보라-남색 계열, alpha 없음 → 완전 불투명
  // → 전체 캔버스를 이 색으로 채움
  // ───────────────────────────────────────────────────────────
  background(40, 30, 40);

  // ───────────────────────────────────────────────────────────
  // 동심원 그리기
  // ───────────────────────────────────────────────────────────

  // 도형 내부를 칠하지 않음 (원 테두리만)
  noFill();

  // 선 색상: (R=100, G=90, B=100) → 회색 계열의 연보라, alpha=180 → 약간 반투명
  stroke(100, 90, 100, 180);

  // 선 두께: 0.5px
  strokeWeight(0.5);

  // 동심원 20개 반복 생성: i = 1 ~ 20
  for (let i = 1; i <= numConcentric; i++) {
    // map(값, 최소입력, 최대입력, 최소출력, 최대출력):
    // i가 1 → r = 20px, i가 20 → r = maxRadius(=486px)가 되도록 비율 변환
    // → 작은 원부터 큰 원까지 균등한 간격으로 배치
    let r = map(i, 1, numConcentric, 20, maxRadius);

    // circle(중심x, 중심y, 지름) → 반지름(r)의 2배를 지름으로 전달
    // (p5.js의 circle 함수는 지름(diameter)을 받음, 반지름(radius) 아님!)
    circle(cx, cy, r * 2);
  }

  // ───────────────────────────────────────────────────────────
  // 중심부 강조하는 원
  // ───────────────────────────────────────────────────────────

  // 도형 내부를 칠하지 않음 (원 테두리만)
  noFill();

  // 흰색 계열, alpha=120
  stroke(255, 255, 255, 120);

  // 선 두께를 1.5px로 약간 강조
  strokeWeight(1.5);

  // 고정된 크기(지름 60px)의 원을 중심에 그림
  // → 반지름 30px, 즉 중심에서 30px 이내는 비워둠 (다른 요소와 겹침 방지)
  circle(cx, cy, 60);

  // ───────────────────────────────────────────────────────────
  // 방사형 선(rays)과 그 위의 점(circles) 그리기
  // ───────────────────────────────────────────────────────────

  // 선 개수(numRays=60)만큼 반복
  for (let i = 0; i < numRays; i++) {
    // 현재 선의 기준 각도 계산:
    // i=0 → 0 rad (→ 0°), i=59 → 약 6.18 rad (→ 354°), i=60이면 2π(360°)
    // map(i, 0, 60, 0, TWO_PI) → 60등분된 원형 배열
    let baseAngle = map(i, 0, numRays, 0, TWO_PI);

    // 이 선의 전체 길이: 30px(중심 원 테두리 바깥) ~ maxRadius(486px) 사이 랜덤
    // → 중심에 겹치지 않고, 캔버스 밖으로 나가지도 않음
    let len = random(30, maxRadius);

    // 선의 끝점 좌표 계산 (삼각함수: x = cosθ * r, y = sinθ * r)
    let x2 = cx + cos(baseAngle) * len;
    let y2 = cy + sin(baseAngle) * len;

    // ───── 선 그리기 ─────────────────────────────────────────

    // 선 색상: (200,200,220), alpha=150
    stroke(200, 200, 220, 150);

    // 중심(cx,cy)에서 끝점(x2,y2)까지 직선 그리기
    line(cx, cy, x2, y2);

    // ───── 선 위에 점(circles) 그리기 ───────────────────────

    // 이 선 위에 그릴 점 개수: 3~7개 사이 랜덤 (int()로 소수점 버림 → 3,4,5,6,7)
    let steps = int(random(3, 8));

    // 점에는 선 테두리를 그리지 않음 (채우기만)
    noStroke();

    // 점을 선 위에 균등히 배치: j = 1 ~ steps
    for (let j = 1; j <= steps; j++) {
      // t = j / (steps + 1):
      // 예: steps=4 → t = 1/5, 2/5, 3/5, 4/5 → 선의 20%, 40%, 60%, 80% 지점
      // → 시작점(0)과 끝점(1)은 건너뛰어 중심/끝에 겹치지 않음
      let t = j / (steps + 1);

      // 중심으로부터의 실제 거리 = 전체 길이(len) × 비율(t)
      let distance = len * t;

      // ⚠️ 중심에서 30px 이내는 건너뜀 → 중심 강조 원과 겹치지 않게
      if (distance < 30) continue;

      // 점의 크기: 지름 2~8px 사이 랜덤
      let size = random(2, 8);

      // 점의 색상:
      // random(100) → 0~100 사이 난수, 85보다 크면(15% 확률) 따뜻한 노란색,
      // 아니면(85% 확률) 흰색 계열
      let col =
        random(100) > 85
          ? color(220, 180, 80, 200) // 노란색: R=220, G=180, B=80, alpha=200
          : color(240, 240, 240, 180); // 거의 흰색, alpha=180

      // 점의 절대 좌표 계산 (중심 + 방향×거리)
      let px = cx + cos(baseAngle) * distance;
      let py = cy + sin(baseAngle) * distance;

      // 점 색상 적용
      fill(col);

      // 점 그리기: circle(중심x, 중심y, 지름)
      circle(px, py, size);
    }
  }

  // ───────────────────────────────────────────────────────────
  // 주석 해제 시 실행하면 파일 저장
  // ───────────────────────────────────────────────────────────
  // save("infographic_style_01.png");
}

function draw() {
  // 이 함수가 비어 있으므로, setup()에서 그린 이미지가 고정되어 유지됨.
  // 애니메이션 없음, CPU 부하 없음, 완전한 정지 화면.
}

```

***

## 3. mold

**파일 경로:** `260114_slime_mold_patt_vira\mold.js`

**설명:** mold.js - p5.js 스케치

```javascript
class Mold {
  constructor() {
    // Mold 객체의 초기 위치: 랜덤한 캔버스 내 좌표
    this.x = random(width);
    this.y = random(height);
    this.r = 0.5; // Mold의 반지름

    // 이동 방향: 랜덤한 각도(0~359도)로 초기화
    this.heading = random(360);
    this.vx = cos(this.heading); // x축 이동 속도 (cos: 각도의 x 성분)
    this.vy = sin(this.heading); // y축 이동 속도 (sin: 각도의 y 성분)
    this.rotAngle = 45;          // 회전 각도 (방향 전환 시 사용)
    this.stop = false;           // 이동 정지 여부

    // 센서 위치 계산용 변수
    this.rSensorPos = createVector(0, 0); // 오른쪽 센서 위치
    this.lSensorPos = createVector(0, 0); // 왼쪽 센서 위치
    this.fSensorPos = createVector(0, 0); // 앞쪽 센서 위치
    this.sensorAngle = 35;                // 센서 각도 (기준 방향에서 ±45도)
    this.sensorDist = 10;                 // 센서 거리 (Mold로부터 10픽셀)
  }

  update() {
    // stop이 true이면 이동 정지
    if (this.stop) {
      this.vx = 0;
      this.vy = 0;
    } else {
      this.vx = cos(this.heading); // 현재 방향의 x 성분
      this.vy = sin(this.heading); // 현재 방향의 y 성분
    }

    // 캔버스 경계를 넘어가면 반대편에서 등장 (% 연산자 사용)
    this.x = (this.x + this.vx + width) % width;
    this.y = (this.y + this.vy + height) % height;

    // 센서 위치 계산 (현재 방향을 기준으로 왼쪽, 오른쪽, 앞쪽 센서 위치)
    this.getSensorPos(this.rSensorPos, this.heading + this.sensorAngle);
    this.getSensorPos(this.lSensorPos, this.heading - this.sensorAngle);
    this.getSensorPos(this.fSensorPos, this.heading);

    // 센서 위치의 픽셀 색상 값 가져오기
    let index, l, r, f;
    index =
      4 * (d * floor(this.rSensorPos.y)) * (d * width) +
      4 * (d * floor(this.rSensorPos.x));
    r = pixels[index]; // 오른쪽 센서 위치의 픽셀 색상 값 (빨강 성분)

    index =
      4 * (d * floor(this.lSensorPos.y)) * (d * width) +
      4 * (d * floor(this.lSensorPos.x));
    l = pixels[index]; // 왼쪽 센서 위치의 픽셀 색상 값 (빨강 성분)

    index =
      4 * (d * floor(this.fSensorPos.y)) * (d * width) +
      4 * (d * floor(this.fSensorPos.x));
    f = pixels[index]; // 앞쪽 센서 위치의 픽셀 색상 값 (빨강 성분)

    // 센서 값에 따른 방향 전환 로직
    if (f > l && f > r) {
      this.heading += 0; // 앞쪽 센서 값이 가장 크면 방향 유지
    } else if (f < l && f < r) {
      // 앞쪽 센서 값이 가장 작으면 랜덤하게 왼쪽 또는 오른쪽으로 회전
      if (random(1) < 0.5) {
        this.heading += this.rotAngle;
      } else {
        this.heading -= this.rotAngle;
      }
    } else if (l > r) {
      this.heading -= this.rotAngle; // 왼쪽 센서 값이 더 크면 오른쪽으로 회전
    } else if (r > l) {
      this.heading += this.rotAngle; // 오른쪽 센서 값이 더 크면 왼쪽으로 회전
    }
  }

  // Mold를 하얀 테두리 없는 원으로 그리기
  display() {
    noStroke(); 
    fill(255);  
    ellipse(this.x, this.y, this.r * 2, this.r * 2);
  }

  // 센서 위치 계산 함수
  getSensorPos(sensor, angle) {
    sensor.x = (this.x + this.sensorDist * cos(angle) + width) % width;
    sensor.y = (this.y + this.sensorDist * sin(angle) + height) % height;
  }
}

```

***

## 4. opt\_art\_circle

**파일 경로:** `251120_op_art\opt_art_circle.js`

**설명:** opt\_art\_circle.js - p5.js 스케치

```javascript
/**
사각형이 원형 궤도를 따라서 프레임 단위로 생성. 흑백 컬러 교차하면서 옵아트. 
**/

let angle = 0;    // 현재 각도 변수
let radius = 400; // 원의 반지름
let speed = 0.069; // 회전 속도

function setup() {
  createCanvas(800, 800);
  background(15); // 한 번만 그리기
  rectMode(CENTER); // 사각형의 기준점을 중앙으로 설정
  noStroke(); // 외곽선 없음
}

function draw() {
  // 1. 캔버스 중심 좌표
  let centerX = width;
  let centerY = height / 2;

  // 2. 원형 경로 계산 (Polar to Cartesian)
  // x = r * cos(theta), y = r * sin(theta)
  let x = centerX + cos(angle) * radius;
  let y = centerY + sin(angle) * radius;

  // 3. 그리기 상태 저장 (push/pop 패턴 사용)
  push();

  // 위치 이동 및 회전
  translate(x, y);

  // 사각형이 진행 방향을 바라보도록 회전 (접선 각도)
  // 원의 경우 현재 각도(angle)에 90도(HALF_PI)를 더하거나 빼면 진행 방향이 됨
  rotate(angle);

  // 4. 색상 교차 로직
  if (frameCount % 2 === 0) {
    fill(60, 200, 190);
  } else {
    fill(15);
  }

  // 5. 사각형 그리기
  rect(0, 0, 600, 400);

  pop(); // 변환 상태 복구

  // 6. 각도 업데이트 (한 바퀴 2*PI 돌면 멈추거나 계속 돌거나 선택)
  angle += speed;

  // (옵션) 한 바퀴 다 돌면 멈추고 싶다면 주석 해제
  if (angle > TWO_PI) noLoop();
}

```

***

## 5. opt\_art\_sine\_curve

**파일 경로:** `251120_op_art\opt_art_sine_curve.js`

**설명:** opt\_art\_sine\_curve.js - p5.js 스케치

```javascript
/**
 * 위에서 아래로 내려오며 사인파(Sine Wave)를 그림
 */

let yPos = 0;           // Y축 시작 위치
let angle = 0;          // 사인파 계산을 위한 각도
let amplitude = 100;    // 진폭 (좌우로 흔들리는 폭)
let frequency = 0.06;   // 진동수 (곡선의 촘촘함)

function setup() {
  createCanvas(800, 800);
  background(25);  
  rectMode(CENTER);
  noStroke();
}

function draw() {
  // 캔버스 세로길이 + alpha 너머로 벗어나면 멈춤
  if (yPos > height + 400) {
    noLoop();
    return;
  }

  // 1. 좌표 계산
  // X좌표는 사인값에 따라 좌우로 진동
  let x = width / 2 + sin(angle) * amplitude;
  let y = yPos;

  push();
  translate(x, y);

  // 2. 회전 계산 (미분 개념 활용)
  // 사인 곡선의 접선 각도를 구하기 위해 코사인 값을 사용하거나,
  // 이전 좌표와의 차이를 이용할 수 있습니다. 여기서는 간단히 코사인으로 근사합니다.
  // sin(x)의 미분은 cos(x)이므로, 이를 이용해 기울기를 구합니다.
  let rotationAngle = cos(angle) * 0.5; // 0.5은 회전 강도 조절 계수
  rotate(rotationAngle);

  // 3. 색상 교차
  if (frameCount % 2 === 0) {
    fill(60, 200, 190);
  } else {
    fill(25);
  }

  // 4. 사각형 그리기
  rect(0, 0, 200, 200);
  pop();

  // 5. 변수 업데이트
  yPos += 2;            // 아래로 내려가는 속도
  angle += frequency;   // 곡선의 흐름 속도
}

```

***

## 6. script

**파일 경로:** `250000_PRACTICE\script.js`

**설명:** script.js - p5.js 스케치

```javascript
let ringRadius = 140;
let minorRadius = 75;

let ringStep = 3;
let tubeStep = 3;

let rotY = 0;
let rotSpeed = 0.05;

function setup() {
  createCanvas(800, 600, WEBGL);
  angleMode(DEGREES);
  colorMode(HSB, 360, 100, 100);
  strokeWeight(1.5);
  noFill();
  smooth();
}

function draw() {
  background(0);
  orbitControl(1.5, 1.5);

  rotY = (rotY + rotSpeed) % 360;
  rotateY(rotY);

  let h = rotY;

  beginShape(POINTS);
  for (let ringAngle = 0; ringAngle < 360; ringAngle += ringStep) {
    for (let tubeAngle = 0; tubeAngle < 360; tubeAngle += tubeStep) {
      let x = (ringRadius + minorRadius * cos(tubeAngle)) * cos(ringAngle);
      let y = minorRadius * sin(tubeAngle);
      let z = (ringRadius + minorRadius * cos(tubeAngle)) * sin(ringAngle);

      stroke(h, 100, 100);
      vertex(x, y, z);
    }
  }
  endShape();
}

```

***

## 7. script

**파일 경로:** `251125_maze_game\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// p5.js 전용 스크립트 (자바스크립트만)
// 미로 영역 고정: 1080 x 1080, 점수는 좌우, 트레일은 칸 중앙의 작은 원

const GRID_SIZE = 16;
const MAZE_PIXEL = 1200; // 미로 영역을 1080x1080으로 고정
const CELL_SIZE = MAZE_PIXEL / GRID_SIZE;
const WALL_THICKNESS = 6;

const SCORE_WIDTH = 150;
const CANVAS_WIDTH = SCORE_WIDTH * 2 + MAZE_PIXEL;
const CANVAS_HEIGHT = MAZE_PIXEL;

const TIMER_CENTER_X = Math.floor(GRID_SIZE / 2);
const TIMER_CENTER_Y = Math.floor(GRID_SIZE / 2);

let wallsH, wallsV;
let player1, player2;
let trailsP1 = new Set();
let trailsP2 = new Set();
let scoreP1 = 0,
  scoreP2 = 0;

let timeLeft = 60;
let gameActive = false;
let gameStarted = false;
let gameOver = false;
let timerId = null;

function setup() {
  createCanvas(CANVAS_WIDTH, CANVAS_HEIGHT);
  textFont("Helvetica");
  resetGame();
}

function resetGame() {
  if (timerId) {
    clearInterval(timerId);
    timerId = null;
  }

  timeLeft = 45;
  scoreP1 = 0;
  scoreP2 = 0;
  trailsP1.clear();
  trailsP2.clear();
  gameActive = true;
  gameOver = false;

  generateMaze();

  player1 = { x: 0, y: 0, color: color(255, 0, 0, 150) };
  player2 = {
    x: GRID_SIZE - 1,
    y: GRID_SIZE - 1,
    color: color(0, 255, 0, 150),
  };

  trailsP1.add(`${player1.x},${player1.y}`);
  trailsP2.add(`${player2.x},${player2.y}`);
}

function generateMaze() {
  // wallsH: horizontal walls. size (GRID_SIZE+1) x GRID_SIZE
  wallsH = Array.from({ length: GRID_SIZE + 1 }, () =>
    Array.from({ length: GRID_SIZE }, () => true)
  );
  // wallsV: vertical walls. size GRID_SIZE x (GRID_SIZE+1)
  wallsV = Array.from({ length: GRID_SIZE }, () =>
    Array.from({ length: GRID_SIZE + 1 }, () => true)
  );

  // ensure outer borders true (already true, but keep explicit)
  for (let x = 0; x < GRID_SIZE; x++) {
    wallsH[0][x] = true;
    wallsH[GRID_SIZE][x] = true;
  }
  for (let y = 0; y < GRID_SIZE; y++) {
    wallsV[y][0] = true;
    wallsV[y][GRID_SIZE] = true;
  }

  // Prim-like random maze generation
  const visited = Array.from({ length: GRID_SIZE }, () =>
    Array.from({ length: GRID_SIZE }, () => false)
  );
  const walls = [];

  function addWalls(cx, cy) {
    if (cy > 0 && !visited[cy - 1][cx]) walls.push([cx, cy - 1, "S"]);
    if (cy < GRID_SIZE - 1 && !visited[cy + 1][cx])
      walls.push([cx, cy + 1, "N"]);
    if (cx > 0 && !visited[cy][cx - 1]) walls.push([cx - 1, cy, "E"]);
    if (cx < GRID_SIZE - 1 && !visited[cy][cx + 1])
      walls.push([cx + 1, cy, "W"]);
  }

  visited[0][0] = true;
  addWalls(0, 0);

  while (walls.length > 0) {
    const idx = floor(random(walls.length));
    const [wx, wy, dir] = walls.splice(idx, 1)[0];
    if (visited[wy][wx]) continue;
    visited[wy][wx] = true;

    if (dir === "N") {
      // remove wall between (wx,wy) and (wx,wy-1)
      wallsH[wy][wx] = false;
    } else if (dir === "S") {
      wallsH[wy + 1][wx] = false;
    } else if (dir === "W") {
      wallsV[wy][wx] = false;
    } else if (dir === "E") {
      wallsV[wy][wx + 1] = false;
    }

    addWalls(wx, wy);
  }

  // 중앙 타이머 영역 주변(4x4) 벽 제거 — 범위 체크 철저히
  for (let yy = TIMER_CENTER_Y - 1; yy <= TIMER_CENTER_Y + 2; yy++) {
    for (let xx = TIMER_CENTER_X - 1; xx <= TIMER_CENTER_X + 2; xx++) {
      if (yy >= 0 && yy <= GRID_SIZE && xx >= 0 && xx < GRID_SIZE) {
        wallsH[yy][xx] = false;
      }
      if (yy >= 0 && yy < GRID_SIZE && xx >= 0 && xx <= GRID_SIZE) {
        wallsV[yy][xx] = false;
      }
    }
  }
}

function draw() {
  background("#1a1a1a");

  if (!gameStarted) {
    drawStartScreen();
    return;
  }

  if (gameOver) {
    drawGameOver();
    return;
  }

  drawScores();

  push();
  translate(SCORE_WIDTH, 0);

  // 미로 영역 배경 (선택적)
  noStroke();
  fill(24);
  rect(0, 0, MAZE_PIXEL, MAZE_PIXEL);

  drawTrails();
  drawMaze();
  drawPlayers();
  drawTimer();

  pop();
}

function drawStartScreen() {
  fill(255);
  textAlign(CENTER, CENTER);
  textSize(48);
  text("Press SPACE to start", width / 2, height / 2);
}

function drawScores() {
  // 왼쪽 P1
  textAlign(LEFT, TOP);
  fill(player1.color);
  textSize(36);
  text("P1", 20, 30);
  textSize(64);
  fill(player1.color);
  text(scoreP1, 20, 70);

  // 오른쪽 P2
  textAlign(RIGHT, TOP);
  fill(player2.color);
  textSize(36);
  text("P2", width - 20, 30);
  textSize(64);
  fill(player2.color);
  text(scoreP2, width - 20, 70);
}

function drawTrails() {
  noStroke();
  const r = CELL_SIZE * 0.25; // 트레일 반지름 (칸 크기에 비례)
  for (let s of trailsP1) {
    const [x, y] = s.split(",").map(Number);
    fill(255, 0, 0, 50);
    const cx = x * CELL_SIZE + CELL_SIZE / 2;
    const cy = y * CELL_SIZE + CELL_SIZE / 2;
    circle(cx, cy, r);
  }
  for (let s of trailsP2) {
    const [x, y] = s.split(",").map(Number);
    fill(0, 255, 0, 50);
    const cx = x * CELL_SIZE + CELL_SIZE / 2;
    const cy = y * CELL_SIZE + CELL_SIZE / 2;
    circle(cx, cy, r);
  }
}

function drawMaze() {
  stroke(255);
  strokeWeight(WALL_THICKNESS);
  strokeJoin(ROUND);
  noFill();

  // horizontal walls
  for (let y = 0; y <= GRID_SIZE; y++) {
    for (let x = 0; x < GRID_SIZE; x++) {
      if (wallsH[y][x]) {
        const x1 = x * CELL_SIZE;
        const y1 = y * CELL_SIZE;
        const x2 = (x + 1) * CELL_SIZE;
        const y2 = y * CELL_SIZE;
        line(x1, y1, x2, y2);
      }
    }
  }

  // vertical walls
  for (let y = 0; y < GRID_SIZE; y++) {
    for (let x = 0; x <= GRID_SIZE; x++) {
      if (wallsV[y][x]) {
        const x1 = x * CELL_SIZE;
        const y1 = y * CELL_SIZE;
        const x2 = x * CELL_SIZE;
        const y2 = (y + 1) * CELL_SIZE;
        line(x1, y1, x2, y2);
      }
    }
  }
}

function drawPlayers() {
  noStroke();
  const pad = max(6, Math.floor(CELL_SIZE * 0.12)); // 플레이어 사각형 패딩
  fill(player1.color);
  rect(
    player1.x * CELL_SIZE + pad,
    player1.y * CELL_SIZE + pad,
    CELL_SIZE - pad * 2,
    CELL_SIZE - pad * 2
  );
  fill(player2.color);
  rect(
    player2.x * CELL_SIZE + pad,
    player2.y * CELL_SIZE + pad,
    CELL_SIZE - pad * 2,
    CELL_SIZE - pad * 2
  );
}

function drawTimer() {
  noStroke();
  fill(255, 255, 255, 230);
  textAlign(CENTER, CENTER);
  textSize(72);
  text(timeLeft, MAZE_PIXEL / 2, MAZE_PIXEL / 2);
}

function movePlayer(player, trails, otherTrails) {
  const key = `${player.x},${player.y}`;
  if (!trails.has(key)) {
    if (otherTrails.has(key)) {
      trails.add(key);
      return 2; // 상대 트레일 점수
    } else {
      trails.add(key);
      return 1; // 일반 점수
    }
  }
  return 0; // 이미 방문한 곳
}

function checkCollision() {
  if (player1.x === player2.x && player1.y === player2.y) {
    scoreP1 = max(0, scoreP1 - 1);
    scoreP2 = max(0, scoreP2 - 1);
  }
}

function drawGameOver() {
  push();
  fill(0, 0, 0, 200);
  rect(0, 0, width, height);

  fill(255);
  textAlign(CENTER, CENTER);
  textSize(72);
  text("GAME OVER", width / 2, height / 2 - 150);

  textSize(48);
  if (scoreP1 > scoreP2) {
    fill(255, 50, 50);
    text("Player 1 Wins!", width / 2, height / 2 - 50);
  } else if (scoreP2 > scoreP1) {
    fill(50, 255, 50);
    text("Player 2 Wins!", width / 2, height / 2 - 50);
  } else {
    fill(255);
    text("Draw!", width / 2, height / 2 - 50);
  }

  fill(255);
  textSize(36);
  text(`P1: ${scoreP1}  -  P2: ${scoreP2}`, width / 2, height / 2 + 50);

  textSize(28);
  text("Press SPACE to restart", width / 2, height / 2 + 150);
  pop();
}

function keyPressed() {
  // 스페이스로 시작/재시작
  if (key === " ") {
    if (!gameStarted || gameOver) {
      gameStarted = true;
      resetGame();

      timerId = setInterval(() => {
        if (gameActive && timeLeft > 0) timeLeft--;
        if (timeLeft === 0) {
          gameActive = false;
          gameOver = true;
          clearInterval(timerId);
          timerId = null;
        }
      }, 1000);
    }
    return false;
  }

  if (!gameStarted || !gameActive) return;

  let p1Moved = false;
  let p2Moved = false;

  const k = key.toLowerCase();

  // Player 1 (WASD)
  if (k === "w" && player1.y > 0 && !wallsH[player1.y][player1.x]) {
    player1.y--;
    p1Moved = true;
  } else if (
    k === "s" &&
    player1.y < GRID_SIZE - 1 &&
    !wallsH[player1.y + 1][player1.x]
  ) {
    player1.y++;
    p1Moved = true;
  } else if (k === "a" && player1.x > 0 && !wallsV[player1.y][player1.x]) {
    player1.x--;
    p1Moved = true;
  } else if (
    k === "d" &&
    player1.x < GRID_SIZE - 1 &&
    !wallsV[player1.y][player1.x + 1]
  ) {
    player1.x++;
    p1Moved = true;
  }

  if (p1Moved) {
    scoreP1 += movePlayer(player1, trailsP1, trailsP2);
  }

  // Player 2 (Arrow keys)
  if (keyCode === UP_ARROW && player2.y > 0 && !wallsH[player2.y][player2.x]) {
    player2.y--;
    p2Moved = true;
  } else if (
    keyCode === DOWN_ARROW &&
    player2.y < GRID_SIZE - 1 &&
    !wallsH[player2.y + 1][player2.x]
  ) {
    player2.y++;
    p2Moved = true;
  } else if (
    keyCode === LEFT_ARROW &&
    player2.x > 0 &&
    !wallsV[player2.y][player2.x]
  ) {
    player2.x--;
    p2Moved = true;
  } else if (
    keyCode === RIGHT_ARROW &&
    player2.x < GRID_SIZE - 1 &&
    !wallsV[player2.y][player2.x + 1]
  ) {
    player2.x++;
    p2Moved = true;
  }

  if (p2Moved) {
    scoreP2 += movePlayer(player2, trailsP2, trailsP1);
  }

  if (p1Moved || p2Moved) checkCollision();

  return false; // 브라우저 기본 키동작 차단
}

```

***

## 8. script

**파일 경로:** `251126_6lines_art\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// setup=_=>createCanvas(w=800,w)
// draw=_=>{
// x=random(-w/8,w+w/8);y=random(-w/8,w+w/8)
// stroke(random(255))
// for(i=0;i<w*2;i++)point(x+=cos(5*y/w*10)+cos(3*y/w*10),y+=sin(3*x/w*10)-cos(5*x/w*10))}


let w = 800;

function setup() {
  createCanvas(w, w);
}

function draw() {
  let x = random(-w / 8, w + w / 8);
  let y = random(-w / 8, w + w / 8);

  stroke(random(255));
  strokeWeight(1);

  for (let i = 0; i < w * 2; i++) {
    x += cos(((5 * y) / w) * 10) + cos(((3 * y) / w) * 10);
    y += sin(((3 * x) / w) * 10) - cos(((5 * x) / w) * 10);

    point(x, y);
  }
}


```

***

## 9. script

**파일 경로:** `251126_particle_collision\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// 전역 변수
let particles = [];
const PARTICLE_COUNT = 200;
const MINT_PERCENTAGE = 0.5; // 비율 (0~1)
const CANVAS_SIZE = 1000;
const BG_COLOR = 10;

function setup() {
  createCanvas(CANVAS_SIZE, CANVAS_SIZE);

  // 파티클 초기화
  const MintCount = Math.floor(PARTICLE_COUNT * MINT_PERCENTAGE);

  for (let i = 0; i < PARTICLE_COUNT; i++) {
    particles.push({
      x: random(CANVAS_SIZE),
      y: random(CANVAS_SIZE),
      vx: random(-1, 1),
      vy: random(-1, 1),
      r: floor(random(5, 8)),
      isYellow: i < MintCount,
    });
  }
}

function draw() {
  background(BG_COLOR);
  noStroke();

  // 파티클 개수 카운트
  let MintCount = 0;
  let BlueCount = 0;

  // 파티클 업데이트 및 그리기
  for (let i = 0; i < particles.length; i++) {
    let p = particles[i];

    // 개수 카운트
    if (p.isYellow) {
      MintCount++;
    } else {
      BlueCount++;
    }

    // 위치 업데이트
    p.x += p.vx;
    p.y += p.vy;

    // 벽 충돌 처리
    if (p.x - p.r < 0 || p.x + p.r > CANVAS_SIZE) {
      p.vx *= -1;
      p.x = constrain(p.x, p.r, CANVAS_SIZE - p.r);
    }
    if (p.y - p.r < 0 || p.y + p.r > CANVAS_SIZE) {
      p.vy *= -1;
      p.y = constrain(p.y, p.r, CANVAS_SIZE - p.r);
    }

    // 파티클 간 충돌 처리
    for (let j = i + 1; j < particles.length; j++) {
      let other = particles[j];
      let dx = other.x - p.x;
      let dy = other.y - p.y;
      let distSq = dx * dx + dy * dy;
      let minDist = p.r + other.r;

      if (distSq < minDist * minDist) {
        // 충돌 발생 - 색상 교환
        let temp = p.isYellow;
        p.isYellow = other.isYellow;
        other.isYellow = temp;

        // 반사 처리
        let dist = sqrt(distSq);
        let nx = dx / dist;
        let ny = dy / dist;

        let dvx = p.vx - other.vx;
        let dvy = p.vy - other.vy;
        let dotProduct = dvx * nx + dvy * ny;

        p.vx -= dotProduct * nx;
        p.vy -= dotProduct * ny;
        other.vx += dotProduct * nx;
        other.vy += dotProduct * ny;

        // 겹침 해소
        let overlap = minDist - dist;
        let separateX = nx * overlap * 0.5;
        let separateY = ny * overlap * 0.5;
        p.x -= separateX;
        p.y -= separateY;
        other.x += separateX;
        other.y += separateY;
      }
    }

    // 그리기
    fill(p.isYellow ? color(30, 210, 180) : color(60, 40, 200));
    circle(p.x, p.y, p.r * 2);
  }

  // 하단 정보 표시
  fill(255);
  textSize(20);
  textAlign(LEFT);
  text(`Mint Team : ${MintCount}`, 20, CANVAS_SIZE - 40);
  text(`Blue Team : ${BlueCount}`, 20, CANVAS_SIZE - 15);
}

```

***

## 10. script

**파일 경로:** `251126_particle_contagion\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// 전역 변수
let particles = [];
const PARTICLE_COUNT = 2000;
const YELLOW_PERCENTAGE = 0.01; // 초기 노란색 비율 (0~1)
const CANVAS_SIZE = 1000;
const BG_COLOR = 10;

let startTime;
let endTime = null;
let isInfectionComplete = false;

function setup() {
  createCanvas(CANVAS_SIZE, CANVAS_SIZE);

  // 파티클 초기화
  const yellowCount = Math.floor(PARTICLE_COUNT * YELLOW_PERCENTAGE);

  for (let i = 0; i < PARTICLE_COUNT; i++) {
    particles.push({
      x: random(CANVAS_SIZE),
      y: random(CANVAS_SIZE),
      vx: random(-1, 1),
      vy: random(-1, 1),
      r: floor(random(3, 5)),
      isYellow: i < yellowCount,
    });
  }

  startTime = millis();
}

function draw() {
  background(BG_COLOR);
  noStroke();

  // 파티클 개수 카운트
  let yellowCount = 0;
  let greenCount = 0;

  // 파티클 업데이트 및 그리기
  for (let i = 0; i < particles.length; i++) {
    let p = particles[i];

    // 개수 카운트
    if (p.isYellow) {
      yellowCount++;
    } else {
      greenCount++;
    }

    // 전염이 완료되지 않았을 때만 이동
    if (!isInfectionComplete) {
      // 위치 업데이트
      p.x += p.vx;
      p.y += p.vy;

      // 벽 충돌 처리
      if (p.x - p.r < 0 || p.x + p.r > CANVAS_SIZE) {
        p.vx *= -1;
        p.x = constrain(p.x, p.r, CANVAS_SIZE - p.r);
      }
      if (p.y - p.r < 0 || p.y + p.r > CANVAS_SIZE) {
        p.vy *= -1;
        p.y = constrain(p.y, p.r, CANVAS_SIZE - p.r);
      }

      // 파티클 간 충돌 처리
      for (let j = i + 1; j < particles.length; j++) {
        let other = particles[j];
        let dx = other.x - p.x;
        let dy = other.y - p.y;
        let distSq = dx * dx + dy * dy;
        let minDist = p.r + other.r;

        if (distSq < minDist * minDist) {
          // 충돌 발생 - 전염 로직
          if (p.isYellow && !other.isYellow) {
            other.isYellow = true;
          } else if (!p.isYellow && other.isYellow) {
            p.isYellow = true;
          }

          // 반사 처리
          let dist = sqrt(distSq);
          let nx = dx / dist;
          let ny = dy / dist;

          let dvx = p.vx - other.vx;
          let dvy = p.vy - other.vy;
          let dotProduct = dvx * nx + dvy * ny;

          p.vx -= dotProduct * nx;
          p.vy -= dotProduct * ny;
          other.vx += dotProduct * nx;
          other.vy += dotProduct * ny;

          // 겹침 해소
          let overlap = minDist - dist;
          let separateX = nx * overlap * 0.5;
          let separateY = ny * overlap * 0.5;
          p.x -= separateX;
          p.y -= separateY;
          other.x += separateX;
          other.y += separateY;
        }
      }
    }

    // 그리기
    fill(p.isYellow ? color(255, 255, 0) : color(0, 100, 0));
    circle(p.x, p.y, p.r * 2);
  }

  // 전염 완료 체크
  if (!isInfectionComplete && greenCount === 0) {
    isInfectionComplete = true;
    endTime = millis();
  }

  // 하단 왼쪽 정보 표시
  fill(255);
  textSize(20);
  textAlign(LEFT);
  text(`Yellow Team : ${yellowCount}`, 20, CANVAS_SIZE - 40);
  text(`Green Team : ${greenCount}`, 20, CANVAS_SIZE - 15);

  // 하단 오른쪽 타이머 표시
  textAlign(RIGHT);
  textSize(50);
  let elapsedTime;
  if (isInfectionComplete) {
    elapsedTime = (endTime - startTime) / 1000;
  } else {
    elapsedTime = (millis() - startTime) / 1000;
  }
  text(`Time : ${elapsedTime.toFixed(1)}s`, CANVAS_SIZE - 20, CANVAS_SIZE - 15);
}

```

***

## 11. script

**파일 경로:** `251126_revolving_circles_by_garabatospr\script.js`

**설명:** script.js - p5.js 스케치

```javascript
let colors = ["#226699", "#dd2e44", "#ffcc4d", "#FFFFFF"];
let mySeed;
let nonStop = true;
let ringCache = {};

function setup() {
  createCanvas(1000, 1000);
  // createCanvas(windowWidth, windowHeight);

  rectMode(CENTER);
  ellipseMode(CENTER);
  colorMode(HSB, 360, 100, 100, 100);
  mySeed = floor(random(1, 1000));
}

function draw() {
  randomSeed(mySeed);
  fill(30);
  square(width / 2, height / 2, width);

  let mySize = width * 0.45;

  if (!nonStop) {
    noLoop();
  }
  let xx = width / 2;
  let yy = height / 2;

  {
    push();
    translate(xx, yy);
    fill(90);
    circle(0, 0, mySize * 1.75);
    noFill();
    strokeWeight(10);
    ring(0, 0, mySize / 2, mySize / 3.4);
    let signo = random([-1, 1]);
    rotate(signo * 0.01 * frameCount);
    for (i = 2; i < 4.5; i += 0.5) {
      drawCircles(-mySize / 1.72, 0, mySize / i, random(0.02, 0.01));
      drawCircles(mySize / 1.72, 0, mySize / i, random(0.02, 0.01));
      drawCircles(0, -mySize / 1.72, mySize / i, random(0.02, 0.01));
      drawCircles(0, mySize / 1.72, mySize / i, random(0.02, 0.01));
      drawCircles(0, 0, mySize / i, random(0.02, 0.015));
    }
    noFill();
    pop();
  }
}

function drawCircles(x, y, mySize, freq) {
  noStroke();
  let dir = random([-1, 1]);
  let rphase = random(0, 2 * PI);
  let col1 = color(generateColor());
  fill(col1);
  let temp;
  let d = 1;
  if (d < 10) {
    temp = frameCount;
  } else {
    temp = 1;
  }
  let xx = x + (mySize / 2) * cos(dir * freq * temp + rphase);
  let yy = y + (mySize / 2) * sin(dir * freq * temp + rphase);
  stroke(0);
  strokeWeight(1);
  line(x, y, xx, yy);
  ring(x, y, mySize / 6, mySize / 12);
  ring(xx, yy, mySize / 12, mySize / 24);
  ring(xx, yy, mySize / 24, mySize / 75);
}

function addBlur() {
  drawingContext.shadowOffsetX = -2;
  drawingContext.shadowOffsetY = -2;
  drawingContext.shadowBlur = 5;
  drawingContext.shadowColor = "black";
}

function generateColor() {
  let myColor = color(random(colors));
  //myColor.setAlpha(random(0,100));
  return myColor;
}

function mousePressed() {
  // toggle
  nonStop = !nonStop;
  if (!nonStop) {
    noLoop();
  } else {
    loop();
  }
}

function ring(x, y, rOuter, rInner, detail = 60) {
  let col = generateColor();

  let key = `${rOuter}-${rInner}-${detail}-${col}`;

  if (!ringCache[key]) {
    let size = rOuter * 2 + 4; // un poco de margen
    let pg = createGraphics(size, size);

    pg.fill(col);
    pg.translate(size / 2, size / 2);

    pg.circle(0, 0, rInner / 2);

    pg.beginShape();
    for (let i = 0; i < detail; i++) {
      let a = (TWO_PI * i) / detail;
      pg.vertex(rOuter * cos(a), rOuter * sin(a));
    }

    pg.beginContour();
    for (let i = detail; i >= 0; i--) {
      let a = (TWO_PI * i) / detail;
      pg.vertex(rInner * cos(a), rInner * sin(a));
    }
    pg.endContour();
    pg.endShape(CLOSE);

    ringCache[key] = pg;
  }

  push();
  imageMode(CENTER);
  image(ringCache[key], x, y);
  pop();
}

```

***

## 12. script

**파일 경로:** `251126_sprouts_by_garabatospr\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// "sprouts" by garabatospr

// color palette

var colors = ["#ff0000", "#feb30f", "#0aa4f7", "#000000", "#ffffff"];

// set weights for each color

// red, blue, and white dominates

var weights = [5, 5, 10, 1, 6, 3];

var weights1 = [1, 1, 1, 1, 6, 3];

var weights2 = [1, 1, 1, 1, 3, 6];

// scale of the vector field
// smaller values => bigger structures
// bigger values  ==> smaller structures

var myScale = 3;

// number of drawing agents

var nAgents = 10000;

var border = -100;

let agent = [];

function setup() {
  createCanvas(800, 800);
  colorMode(HSB, 360, 100, 100, 100);
  strokeCap(SQUARE);

  background(100);

  for (let i = 0; i < nAgents; i++) {
    agent.push(new Agent());
  }

  randomSeed(1966);
}

function draw() {
  //if (frameCount > 500)
  //{
  //noLoop();
  //translate(width/2,height/2);
  //scale(0.9);
  //}

  push();

  translate(0, 0);

  scale(1);

  //translate(0,0);

  for (let i = 0; i < agent.length; i++) {
    agent[i].update();
  }

  pop();
}

// select random colors with weights from palette

function myRandom(colors, weights) {
  let sum = 0;

  for (let i = 0; i < colors.length; i++) {
    sum += weights[i];
  }

  let rr = random(0, sum);

  for (let j = 0; j < weights.length; j++) {
    if (weights[j] >= rr) {
      return colors[j];
    }
    rr -= weights[j];
  }
}

// paintining agent

class Agent {
  constructor() {
    //this.p     = createVector(1,height*0.50 + randomGaussian()*400);

    this.p = createVector(
      random(border, width - border),
      random(border, height - border)
    );

    this.directionX = 1;
    this.directionY = 1;

    this.pOld = createVector(this.p.x, this.p.y);

    this.step = 5;

    let temp = myRandom(colors, weights);

    this.color = color(
      hue(temp) + randomGaussian() * 10,
      saturation(temp) + randomGaussian() * 10,
      brightness(temp) - 10,
      random(80, 90)
    );

    this.strokeWidth = 5;
  }

  update() {
    this.p.x +=
      this.directionX * vector_field(this.p.x, this.p.y).x * this.step;
    this.p.y +=
      this.directionY * vector_field(this.p.x, this.p.y).y * this.step;

    strokeWeight(this.strokeWidth);
    stroke(this.color);
    line(this.pOld.x, this.pOld.y, this.p.x, this.p.y);

    this.pOld.set(this.p);
  }
}

// vector field function
// the painting agents follow the flow defined
// by this function

function vector_field(x, y) {
  x = map(x, 0, width, 0, myScale);
  y = map(y, 0, height, 0, myScale);

  let k1 = 5;
  let k2 = 3;

  let u = sin(k1 * y) + cos(k2 * x);
  let v = sin(k2 * x) - sin(k1 * x);

  //if (v < 0)
  //{
  //   v = -v;
  //}

  return createVector(u, v);
}

// function to select

function myRandom(colors, weights) {
  let sum = 0;

  for (let i = 0; i < colors.length; i++) {
    sum += weights[i];
  }

  let rr = random(0, sum);

  for (let j = 0; j < weights.length; j++) {
    if (weights[j] >= rr) {
      return colors[j];
    }
    rr -= weights[j];
  }
}

```

***

## 13. script

**파일 경로:** `251129_slime_mold_simulation\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// Slime Mold Simulation
let xMotion = 0;
let yMotion = 0;

const agents = [];
let trailMap;

const numX = 500;
const numY = 8;

function setup() {
  createCanvas(800, 800);

  colorMode(HSB, 360, 255, 255);
  background(0);
  noiseDetail(7, 0.7);

  // Use Float32Array for memory efficiency
  trailMap = new Float32Array(width * height).fill(0);

  // Initialize Agents
  for (let nx = 0; nx < numX; nx++) {
    let nmx = nx / numX;
    for (let ny = 0; ny < numY; ny++) {
      let nmy = ny / numY;

      const noiseVal = noise(nmx * nmy);

      const startX = width / 2 + sin(nmx * PI * 2) * (width / 3.5);
      const startY = height / 2 + cos(nmx * PI * 2) * (height / 3.5);
      const angle = nmx * PI * 2;

      agents.push(new Agent(startX, startY, angle, noiseVal));
    }
  }

  ellipseMode(CENTER);
  rectMode(CENTER);
  noStroke();
}

function draw() {
  // 1. Process Agents
  for (let agent of agents) {
    agent.sense(trailMap);
    agent.move();
    agent.deposit(trailMap); // Deposit logic usually happens after move
    agent.display();
  }

  // 2. Decay Trail
  // TypedArray optimized loop
  for (let i = 0; i < trailMap.length; i++) {
    trailMap[i] -= 0.1;
    if (trailMap[i] < 0) trailMap[i] = 0;
  }

  // 3. Global Motion
  xMotion += 0.05;
  yMotion += 0.0002;
}

// --- Agent Class (Strict Original Math) ---
class Agent {
  constructor(x, y, angle, noiseVal) {
    this.x = x; // Using simple x, y instead of p5.Vector for raw control
    this.y = y;
    this.angle = angle;
    this.vel = 1;
    this.depositVal = 1;
    this.noiseOffset = noiseVal;

    this.sensorOffset = 5;
    this.sensorAngle = 45; // degrees
  }

  sense(mapData) {
    const sensorRad = radians(this.sensorAngle);

    // round(sin(...))을 먼저 수행하여 오프셋을 정수로 만든 뒤 위치에 더함
    const getSensorIndex = (angleOffset) => {
      const angle = this.angle + angleOffset;

      // 1. Calculate Integer Offset first (Crucial for symmetry)
      const offX = Math.round(Math.sin(angle) * this.sensorOffset);
      const offY = Math.round(Math.cos(angle) * this.sensorOffset);

      // 2. Add to position and truncate using parseInt
      let sx = parseInt(this.x + offX);
      let sy = parseInt(this.y + offY);

      // 3. Boundary Wrap (Manual implementation to match original)
      if (sx < 0) sx += width;
      if (sy < 0) sy += height;
      sx %= width;
      sy %= height;

      return sx + sy * width;
    };

    const idxL = getSensorIndex(-sensorRad); // Left
    const idxF = getSensorIndex(0); // Forward
    const idxR = getSensorIndex(sensorRad); // Right

    const sL = mapData[idxL];
    const sF = mapData[idxF];
    const sR = mapData[idxR];

    // Behavior Logic
    if (sF < sL && sF < sR) {
      // Stay same (Random turn was commented out in original)
    } else if (sL < sR) {
      this.angle -= PI / 8; // Turn Left
    } else if (sR < sL) {
      this.angle += PI / 8; // Turn Right
    }
  }

  move() {
    this.x += this.vel * Math.sin(this.angle);
    this.y += this.vel * Math.cos(this.angle);
  }

  deposit(mapData) {
    // Boundary check (Original used 'continue', effectively skipping)
    if (this.x < 0 || this.x > width || this.y < 0 || this.y > height) return;

    // [중요] Diffusion 위치 계산 시 parseInt 사용
    const ix = parseInt(this.x);
    const iy = parseInt(this.y);
    const index = ix + iy * width;

    // Diffusion (3x3 Blur)
    const b = mapData[index] / 2;

    // Neighbor indices with manual wrapping
    // 원본의 (lx - 1), (lx + 1) 로직을 그대로 구현
    const prevX = ix - 1 < 0 ? width - 1 : ix - 1;
    const nextX = (ix + 1) % width;
    const prevY = iy - 1 < 0 ? height - 1 : iy - 1;
    const nextY = (iy + 1) % height;

    mapData[prevX + iy * width] = b;
    mapData[nextX + iy * width] = b;
    mapData[prevX + prevY * width] = b;
    mapData[prevX + nextY * width] = b;
    mapData[nextX + prevY * width] = b;
    mapData[nextX + nextY * width] = b;
    mapData[ix + prevY * width] = b;
    mapData[ix + nextY * width] = b;

    // Deposit current
    mapData[index] = this.depositVal;
  }

  display() {
    if (this.x < 0 || this.x > width || this.y < 0 || this.y > height) return;

    const ll = 400;
    // Magic visual formula strictly preserved
    // noise(this.noiseOffset...) -> ensures agents in the same ring flicker together
    const t =
      noise(this.noiseOffset + xMotion * 4) *
      2 *
      pow(
        1 - min(ll, frameCount) / ll,
        5.5 * (0.5 + (1 - this.y / height) / 2)
      );

    const sph = 1 - abs((0.5 - this.x / width) * 2);
    const colVal = 224 + noise(this.noiseOffset + xMotion * 2) * 32 * sph;

    fill(0, 0, colVal, t / 3);
    // Draw 1x1 rectangle at float position (p5 handles sub-pixel rendering, but logic is integer based)
    rect(this.x, this.y, 1, 1);
  }
}

```

***

## 14. script

**파일 경로:** `251129_star_size_comparison\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// 별 데이터 (1/10으로 조정된 반지름 및 온도 기반 색상)
let stars = [
  { name: "Sun (태양)", radiusSun: 1, temp: 5778, color: "#FFFF00" },
  { name: "Sirius A", radiusSun: 1.7, temp: 9940, color: "#B0E0E6" },
  { name: "Vega", radiusSun: 2.3, temp: 9600, color: "#B0E0E6" },
  { name: "Pollux", radiusSun: 8.8, temp: 4868, color: "#FFDEAD" },
  { name: "Arcturus", radiusSun: 25.7, temp: 4286, color: "#FFA07A" },
  { name: "Aldebaran", radiusSun: 44, temp: 3900, color: "#FF8C00" },
  { name: "Rigel A", radiusSun: 78, temp: 12100, color: "#4169E1" },
  { name: "Deneb", radiusSun: 100, temp: 8525, color: "#ADD8E6" },
  { name: "VV Cephei A", radiusSun: 120, temp: 3600, color: "#B22222" },
  { name: "VY Canis Majoris", radiusSun: 130, temp: 3490, color: "#A52A2A" },
  { name: "Betelgeuse", radiusSun: 140, temp: 3500, color: "#D2691E" },
  { name: "KY Cygni", radiusSun: 150, temp: 3500, color: "#8B4513" },
  { name: "AH Scorpii", radiusSun: 155, temp: 3600, color: "#800000" },
  { name: "V354 Cephei", radiusSun: 160, temp: 3500, color: "#A52A2A" },
  { name: "UY Scuti", radiusSun: 170, temp: 3365, color: "#FFA07A" },
  { name: "LGGS J0045+4147", radiusSun: 190, temp: 3500, color: "#FF4500" },
  { name: "RSGC1-F01", radiusSun: 195, temp: 3400, color: "#E9967A" },
  { name: "VX Sagittarii", radiusSun: 205, temp: 3300, color: "#DAA520" },
  { name: "Stephenson 2-18", radiusSun: 215, temp: 3200, color: "#FF4500" },
];

let CANVAS_WIDTH;
let CANVAS_HEIGHT;

// 줌 관련 상수 및 변수
const MIN_SCALE = 0.1;
const MAX_SCALE = 50.0;
let scaleFactor = MIN_SCALE;

// 캐싱을 위한 변수 (메모리 재할당 방지)
let maxDim;

function setup() {
  CANVAS_WIDTH = windowWidth;
  CANVAS_HEIGHT = windowHeight;
  createCanvas(CANVAS_WIDTH, CANVAS_HEIGHT);

  // [최적화 1] 정렬을 setup으로 이동
  // 데이터가 변하지 않으므로 한 번만 정렬하면 됩니다. (큰 별 -> 작은 별 순서)
  stars.sort((a, b) => b.radiusSun - a.radiusSun);

  // 텍스트 정렬 설정 (루프 밖에서 한 번만 설정해도 되는 경우)
  textAlign(CENTER, BOTTOM);
  noStroke();
}

function draw() {
  background(0);

  // [최적화 2] 좌표계 이동
  // 매번 x + centerX를 계산하는 대신 캔버스 원점을 중앙으로 이동합니다.
  translate(width / 2, height / 2);

  // 성능을 위해 루프 밖에서 max 계산
  maxDim = (width > height ? width : height) * 4;

  // 역방향 for문 사용 (선택 사항이나, JS 엔진에 따라 미세하게 빠를 수 있음)
  // 여기서는 가독성을 위해 일반 for문을 사용하되, stars.length를 캐싱하는 것이 좋습니다.
  // 하지만 stars는 const에 가까우므로 일반 루프로 처리합니다.

  for (let i = 0; i < stars.length; i++) {
    // 직접 접근이 함수 호출보다 빠릅니다.
    const star = stars[i];
    const diameter = star.radiusSun * scaleFactor * 2;

    // [최적화 3] 그리기 제한 (Culling)
    // 1. 화면 전체를 덮을 정도로 너무 커서 배경이 된 경우 (과도한 Fill Rate 방지)
    //    단, 가장 큰 별이 배경색 역할을 해야 하므로 이 조건은 신중해야 합니다.
    //    Jai님의 원래 의도대로 4배 이상 크면 그리지 않습니다.
    if (diameter > maxDim) continue;

    // 2. 너무 작아서 1픽셀도 안 되는 경우 그리지 않음 (GPU 호출 절약)
    if (diameter < 0.5) continue;

    fill(star.color);
    circle(0, 0, diameter); // translate를 했으므로 (0,0)에 그립니다.

    // 텍스트 렌더링 (비용이 비싼 작업이므로 조건부 실행)
    if (diameter > 10) {
      fill(255);
      // 텍스트 크기 설정 등은 상태 변경 비용이 들지만, 여기서는 필요하므로 유지
      // textSize(14)를 루프 밖으로 뺄 수 있다면 좋겠지만, 아래 분기(Sun) 때문에 유지
      textSize(14);
      // 템플릿 리터럴 사용
      text(`${star.name} (${star.radiusSun} R☉)`, 0, -diameter / 2 - 5);
    } else if (star.name === "Sun (태양)" && diameter > 2) {
      // Sun은 작아도 조금 더 오래 표시 (1px -> 2px로 가시성 조정)
      fill(255);
      textSize(12);
      // textAlign을 setup에서 BOTTOM으로 했으므로 CENTER로 일시 변경 필요
      textAlign(CENTER, CENTER);
      text("Sun", 0, 0);
      textAlign(CENTER, BOTTOM); // 다시 원복
    }
  }
}

function mouseWheel(event) {
  // [최적화 4] 줌 로직 개선 (Speed & UX)
  // 기존의 덧셈 방식 대신 곱셈 방식(Exponential scaling)을 사용하면
  // 스케일이 작을 땐 정밀하게, 클 땐 빠르게 줌인/아웃이 되어 연산과 반응성이 좋아집니다.

  let zoomSensitivity = 0.05; // 민감도 조절

  if (event.deltaY > 0) {
    scaleFactor *= 1 - zoomSensitivity; // 줌 아웃
  } else {
    scaleFactor *= 1 + zoomSensitivity; // 줌 인
  }

  scaleFactor = constrain(scaleFactor, MIN_SCALE, MAX_SCALE);

  // HTML 업데이트 (DOM 조작은 p5 루프와 독립적이므로 유지)
  const scaleDisplay = document.getElementById("scale-display");
  if (scaleDisplay) {
    // toFixed 연산은 가볍지만 필요할 때만 호출
    scaleDisplay.textContent = scaleFactor.toFixed(6);
  }

  // 기본 스크롤 동작 방지 (선택 사항)
  return false;
}

function windowResized() {
  CANVAS_WIDTH = windowWidth;
  CANVAS_HEIGHT = windowHeight;
  resizeCanvas(windowWidth, windowHeight);
}

// 상세 수정 내역 및 기술적 설명
// 배열 정렬 (stars.sort) 위치 변경:

// JavaScript의 sort는 $O(N \log N)$의 시간 복잡도를 가집니다. 데이터가 20개 정도로 적더라도, 이를 60FPS마다 매번 실행하는 것은 "메모리 할당(Allocation)"과 "가비지 컬렉션(GC)"을 유발하여 미세한 끊김(jank)의 원인이 됩니다. setup()으로 옮겨서 프로그램 시작 시 딱 한 번만 수행하도록 했습니다.

// translate(width/2, height/2) 사용:

// 기존에는 centerX, centerY 변수를 매 프레임 할당하고, 그리기 명령마다 더하기 연산을 수행했습니다.

// p5.js(WebGL/Canvas)의 행렬 변환 기능인 translate를 사용하면, 이후 모든 좌표를 (0, 0) 기준으로 생각할 수 있어 코드가 깔끔해지고 내부적으로 GPU에 최적화된 방식으로 처리됩니다.

// mouseWheel 로직 변경 (선형 -> 지수형):

// 기존 코드: scaleFactor -= event.deltaY * 0.0001

// 문제점: 스케일이 0.1일 때 0.0001을 빼는 것과, 스케일이 100일 때 0.0001을 빼는 것은 체감 속도가 완전히 다릅니다. (큰 별을 볼 때 줌이 멈춘 것처럼 느껴질 수 있음)

// 해결: 현재 스케일에 비례하여 곱하는 방식(*= 1.05)을 사용하여, 어느 구간에서든 부드러운 줌 속도를 보장합니다. 이는 UX뿐만 아니라, 불필요하게 많은 스크롤 이벤트를 발생시키지 않으므로 성능에도 이점이 있습니다.

// 텍스트 렌더링 상태 관리:

// textAlign이나 noStroke 같은 상태 변경 함수는 캔버스 컨텍스트를 갱신하므로 비용이 듭니다. 반복문 내에서 상태 변경을 최소화하고, 가능한 setup이나 루프 밖에서 공통 속성을 정의했습니다.
```

***

## 15. script

**파일 경로:** `251130_blooming_flowers\script.js`

**설명:** script.js - p5.js 스케치

```javascript
let shapes = [];
const shapesNum = 5000;
let maxRadius;

function setup() {
  // createCanvas(windowWidth, windowHeight);
  createCanvas(800, 800);

  maxRadius = min(width, height) * 0.3;

  for (let i = 0; i < shapesNum; i++) {
    shapes.push(new Shape(i));
  }

  background("#232323");
}

function draw() {
  // 잔상을 위해 매 프레임마다 배경 그림
  background("#2323231A");  // 뒤에서 두 자리는 알파값

  noStroke();
  fill(0);

  for (let i = 0; i < shapes.length; i++) {
    shapes[i].move();
    shapes[i].display();
  }
}

class Shape {
  constructor(i) {
    // 각 도형의 고유한 시간/위상 값으로 사용됨
    this.i = i * 0.01;

    // 반지름 배율
    this.radiusMultiplier = 0;

    // 각도
    this.angle = 0;

    // currentRadius
    this.currentRadius = i % maxRadius;
  }

  move() {
    // 현재 반지름을 점점 키움
    this.currentRadius += 1;
    // 최대 반지름에 도달하면 다시 0으로 리셋
    if (this.currentRadius > maxRadius) {
      this.currentRadius = 0;
    }
  }

  display() {
    // 고유한 i 값을 이용해 복잡한 반지름 배율 계산
    this.radiusMultiplier = (cos(this.i * 3) - cos(this.i * 6) + 9) * 0.45;

    // 고유한 i 값을 이용해 각도 계산
    this.angle = this.i / 2 + (sin(this.i * 3) - sin(this.i * 6)) / 3;

    // 각도와 시간(frameCount)에 따라 색상이 변하게 함
    fill(200 * sin(this.angle * 10 + frameCount * 0.1), 120, 100);

    // 극좌표계를 이용해 원의 위치 계산 및 그리기
    circle(
      this.currentRadius * this.radiusMultiplier * cos(this.angle) + width / 2,
      this.currentRadius * this.radiusMultiplier * sin(this.angle) + height / 2,
      // 원의 크기도 현재 반지름과 고유 값에 따라 변하게 함
      this.currentRadius * 0.2 * sin(this.i * 11)
    );
  }
}
```

***

## 16. script

**파일 경로:** `251130_wobbling_anemone\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// 전역 변수
let lines = [];
const NUM_LINES = 24;

function setup() {
  createCanvas(windowWidth, windowHeight);
  colorMode(HSL, 360, 100, 100, 1);
  // LineSegment 인스턴스 생성
  for (let i = 0; i < NUM_LINES; i++) {
    lines.push(new LineSegment(i, NUM_LINES));
  }
}

function draw() {
  // 트레일 효과
  fill(0, 0, 0, 0.02);
  noStroke();
  rect(0, 0, width, height);

  const cx = width / 2;
  const cy = height / 2;
  const mx = mouseX;
  const my = mouseY;

  // 모든 선 업데이트 & 렌더
  for (let line of lines) {
    line.update(cx, cy, mx, my, frameCount);
    line.display(cx, cy);
  }

  // 마우스 피드백 (작은 고리)
  if (dist(mx, my, cx, cy) < 250) {
    noFill();
    stroke(360, 0, 100, 0.7);
    strokeWeight(1.5);
    ellipse(mx, my, 14, 14);
  }
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}

// LineSegment 클래스 정의
class LineSegment {
  constructor(index, total) {
    this.index = index;
    this.angle = (TWO_PI * index) / total; // 원형 배치
    this.len = 0;
    this.targetLen = random(100, 350);
    this.hue = (index * (360 / total)) % 360;
    this.baseAngle = this.angle; // 원래 각도 (마우스 영향 계산용 기준)
  }

  update(cx, cy, mx, my, frameCount) {
    const dx = mx - cx;
    const dy = my - cy;
    const mouseDist = dist(mx, my, cx, cy);
    const mouseAngle = atan2(dy, dx);
    const dMax = 250;

    // 마우스 근접도 (0~1)
    const influence = constrain(map(mouseDist, 0, dMax, 1, 0), 0, 1);

    // 이 선과 마우스 각도의 차이 → 비슷한 방향일수록 영향 ↑
    let angleDiff = abs(((this.baseAngle - mouseAngle + PI) % TWO_PI) - PI);
    const proximity = map(angleDiff, 0, PI, 1, 0);

    // 길이 목표 갱신 + 부드럽게 보간
    this.targetLen = map(influence * proximity, 0, 1, 120, 450);
    this.len = lerp(this.len, this.targetLen, 0.12);

    // 색상 계산
    this.h = (this.hue + influence * 150) % 360;
    this.s = map(influence, 0, 1, 60, 100);
    this.b = map(influence, 0, 1, 40, 95);
    this.a = map(influence * proximity, 0, 1, 0.3, 0.95);
    this.weight = map(influence * proximity, 0, 1, 1.5, 8);

    // 회전값 (sin cos 혼합)
    this.angle =
      this.baseAngle +
      sin(frameCount * 0.06 + this.index * 0.2) * 0.2 +
      cos(frameCount * 0.03) * 0.3;
  }

  display(cx, cy) {
    push();
    translate(cx, cy);
    rotate(this.angle);

    // 선
    stroke(this.h, this.s, this.b, this.a);
    strokeWeight(this.weight);
    line(0, 0, this.len, 0);

    // 끝점 강조
    fill(this.h, this.s, this.b, this.a * 0.7);
    noStroke();
    ellipse(this.len, 0, 5, 5);
    pop();
  }
}

```

***

## 17. script

**파일 경로:** `251203_tadpole\script.js`

**설명:** script.js - p5.js 스케치

```javascript
const NUM_PARTICLES = 800;
const RADIUS = 2;
const SPEED_MIN = 1.2;
const SPEED_MAX = 2.1;
const RECOVERY_FRAMES = 90;

let particles = [];

function setup() {
  createCanvas(1000, 1000);
  background(255);

  // 서로 겹치지 않게 파티클 생성
  let attempts = 0;
  while (particles.length < NUM_PARTICLES) {
    let x = random(RADIUS, width - RADIUS);
    let y = random(RADIUS, height - RADIUS);
    let valid = true;

    for (let p of particles) {
      if (dist(x, y, p.x, p.y) < RADIUS * 2) {
        valid = false;
        break;
      }
    }

    if (valid) {
      particles.push(new Particle(x, y));
    } else {
      attempts++;
      if (attempts > 10000) {
        console.warn("초기 배치 실패");
        break;
      }
    }
  }
}

function draw() {
  background(255); // 트레일 효과

  // 충돌 체크
  for (let i = 0; i < particles.length; i++) {
    for (let j = i + 1; j < particles.length; j++) {
      particles[i].checkCollision(particles[j]);
    }
  }

  // 업데이트 및 그리기
  for (let p of particles) {
    p.update();
    p.display();
  }
}

class Particle {
  constructor(x, y) {
    this.x = x;
    this.y = y;

    // 랜덤 방향, 랜덤 속도
    let angle = random(TWO_PI);
    let speed = random(SPEED_MIN, SPEED_MAX);
    this.vx = cos(angle) * speed;
    this.vy = sin(angle) * speed;

    this.hit = false;
    this.hitFrame = 0;
    this.history = []; // 트레일 위치 기록
    this.noiseOffset = random(1000); // 노이즈 오프셋 (흔들림용)
  }

  checkCollision(other) {
    let d = dist(this.x, this.y, other.x, other.y);
    if (d < RADIUS * 2) {
      this.hit = true;
      this.hitFrame = 0;
      other.hit = true;
      other.hitFrame = 0;

      let dx = other.x - this.x;
      let dy = other.y - this.y;
      let distSq = dx * dx + dy * dy;

      if (distSq === 0) {
        dx = random(-1, 1);
        dy = random(-1, 1);
        distSq = dx * dx + dy * dy;
      }

      let nx = dx / sqrt(distSq);
      let ny = dy / sqrt(distSq);
      let dvx = other.vx - this.vx;
      let dvy = other.vy - this.vy;
      let dvn = dvx * nx + dvy * ny;

      if (dvn > 0) return;

      let impulse = dvn;
      this.vx += impulse * nx;
      this.vy += impulse * ny;
      other.vx -= impulse * nx;
      other.vy -= impulse * ny;

      let overlap = RADIUS * 2 - sqrt(distSq);
      let push = overlap * 0.5;
      this.x -= nx * push;
      this.y -= ny * push;
      other.x += nx * push;
      other.y += ny * push;
    }
  }

  update() {
    this.x += this.vx;
    this.y += this.vy;

    // 트레일 위치 기록 (현재 위치 추가)
    this.history.push({ x: this.x, y: this.y });
    if (this.history.length > 20) {
      this.history.shift();
    }

    // 벽 충돌
    if (this.x <= RADIUS) {
      this.x = RADIUS;
      this.vx *= -1;
    } else if (this.x >= width - RADIUS) {
      this.x = width - RADIUS;
      this.vx *= -1;
    }

    if (this.y <= RADIUS) {
      this.y = RADIUS;
      this.vy *= -1;
    } else if (this.y >= height - RADIUS) {
      this.y = height - RADIUS;
      this.vy *= -1;
    }

    if (this.hit) {
      this.hitFrame++;
      if (this.hitFrame >= RECOVERY_FRAMES) {
        this.hit = false;
      }
    }
  }

  display() {
    noStroke();

    // 흔들리는 트레일 그리기
    if (this.history.length > 1) {
      for (let i = 0; i < this.history.length - 1; i++) {
        let pos = this.history[i];
        let nextPos = this.history[i + 1];

        // 각 점마다 시간에 따라 변하는 흔들림 계산
        let shakeAmount = random(0.5, 4.5); // 흔들림 강도
        let time = frameCount * 0.1 + i * 0.5 + this.noiseOffset;
        let shakeX = noise(time) * shakeAmount - shakeAmount / 2;
        let shakeY = noise(time + 100) * shakeAmount - shakeAmount / 2;
        let nextShakeX = noise(time + 0.3) * shakeAmount - shakeAmount / 2;
        let nextShakeY = noise(time + 100.3) * shakeAmount - shakeAmount / 2;

        // 투명도와 두께
        let alpha = map(i, 0, this.history.length - 1, 0, 150);
        let weight = map(i, 0, this.history.length - 1, 0.5, 3);

        // 흔들린 위치에 선 그리기
        strokeWeight(weight);
        if (this.hit) {
          let t = this.hitFrame / RECOVERY_FRAMES;
          let gray = lerp(0, 255, t);
          stroke(gray, alpha);
        } else {
          stroke(255, alpha);
        }

        line(
          pos.x + shakeX,
          pos.y + shakeY,
          nextPos.x + nextShakeX,
          nextPos.y + nextShakeY
        );
      }
    }

    // 메인 파티클 그리기
    noStroke();
    if (this.hit) {
      let t = this.hitFrame / RECOVERY_FRAMES;
      let gray = lerp(0, 255, t);
      fill(gray);
    } else {
      fill(255);
    }
    circle(this.x, this.y, RADIUS * 2);
  }
}

```

***

## 18. script

**파일 경로:** `251207_foam_bubble_density\script.js`

**설명:** script.js - p5.js 스케치

```javascript
const CONFIG = {
  canvasSize: 1000,
  bgColor: 15,
  fillColor: [30, 190, 180],
  maxAttempts: 5000,
  minRadius: 5,
};

let circles = [];
let attempts = 0;

function setup() {
  createCanvas(CONFIG.canvasSize, CONFIG.canvasSize);
  background(CONFIG.bgColor);
  noStroke();
}

function draw() {
  if (attempts >= CONFIG.maxAttempts) {
    noLoop();
    return;
  }

  const pos = { x: random(width), y: random(height) };
  const maxR = findMaxRadius(pos.x, pos.y);

  if (maxR >= CONFIG.minRadius) {
    addCircle(pos.x, pos.y, maxR);
    attempts = 0;
  } else {
    attempts++;
  }
}

function addCircle(x, y, r) {
  circles.push({ x, y, r });
  fill(...CONFIG.fillColor, random(100, 255));
  circle(x, y, r * 2);
}

function findMaxRadius(x, y) {
  let maxR = min(x, y, width - x, height - y);

  for (const c of circles) {
    const d = dist(x, y, c.x, c.y);
    maxR = min(maxR, d - c.r);
  }

  return max(maxR, 0);
}

function mousePressed() {
  circles = [];
  attempts = 0;
  background(CONFIG.bgColor);
  loop();
}

```

***

## 19. script

**파일 경로:** `251215_geometric_tiles\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// ============================
// Configuration
// ============================
const CANVAS_SIZE = 800;
const TILE_SIZE = 50;
const GRID_NOISE_SCALE = 0.01;
const TIME_NOISE_SCALE = 0.01;
const TIME_STEP = 0.2;

// ============================
// State
// ============================
let tiles = [];
let time = 0;

// ============================
// p5 Lifecycle
// ============================
function setup() {
  createCanvas(CANVAS_SIZE, CANVAS_SIZE);
  colorMode(HSB, 360, 100, 100, 100);
  angleMode(DEGREES);

  tiles = createTiles();
}

function draw() {
  background(5, 5, 5, 100);
  time += TIME_STEP;

  for (const tile of tiles) {
    updateTile(tile);
    renderTile(tile);
  }
}

// ============================
// Tile Creation
// ============================
function createTiles() {
  const result = [];

  for (let x = 0; x < width; x += TILE_SIZE) {
    for (let y = 0; y < height; y += TILE_SIZE) {
      result.push({
        position: createVector(x, y),
        rotation: random(270),
        rotationSpeed: random(-4, 4),
        hueBase: random(20),
        shapeType: floor(random(4)),
        noiseOffset: random(1000),
      });
    }
  }
  return result;
}

// ============================
// Tile Update
// ============================
function updateTile(tile) {
  const { x, y } = tile.position;

  tile.noiseValue = noise(
    x * GRID_NOISE_SCALE,
    y * GRID_NOISE_SCALE,
    time * TIME_NOISE_SCALE + tile.noiseOffset
  );

  tile.rotation += tile.rotationSpeed * tile.noiseValue;
}

// ============================
// Tile Rendering
// ============================
function renderTile(tile) {
  const { x, y } = tile.position;
  const centerX = x + TILE_SIZE * 0.5;
  const centerY = y + TILE_SIZE * 0.5;
  const n = tile.noiseValue;

  const hue = (tile.hueBase + time * 0.5 + n * 100) % 360;
  const saturation = 40 + sin(time * 6 + x * 0.01) * 20;
  const brightness = 50 + cos(time * 3 + y * 0.1) * 50;
  const size = TILE_SIZE * 0.5 * (0.8 + n * 0.4);

  push();
  translate(centerX, centerY);
  rotate(tile.rotation + n * 20);

  noStroke();
  fill(hue, saturation, brightness, 100);

  SHAPE_RENDERERS[tile.shapeType](size);

  drawInnerDot(size);
  pop();
}

// ============================
// Shape Renderers
// ============================
const SHAPE_RENDERERS = [drawSquare, drawCircle, drawTriangle, drawCross];

function drawSquare(size) {
  rect(-size / 2, -size / 2, size, size);
}

function drawCircle(size) {
  ellipse(0, 0, size, size);
}

function drawTriangle(size) {
  beginShape();
  for (let i = 0; i < 3; i++) {
    const angle = 120 * i - 90;
    vertex(cos(angle) * size * 0.5, sin(angle) * size * 0.5);
  }
  endShape(CLOSE);
}

function drawCross(size) {
  rect(-size / 2, -size / 6, size, size / 3);
  rect(-size / 6, -size / 2, size / 3, size);
}

function drawInnerDot(size) {
  fill(0, 0, 100, 100);
  ellipse(10, 0, size * 0.2);
}

// ============================
// Interaction
// ============================
function keyPressed() {
  if (key === " ") {
    for (const tile of tiles) {
      tile.rotationSpeed = random(-3, 3);
      tile.hueBase = random(360);
      tile.shapeType = floor(random(4));
    }
  }
}

```

***

## 20. script

**파일 경로:** `251215_geometric_wave\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// HTML: <div id="sketch-container"></div>

const geometricWaveSketch = (p) => {
  let particles = [];
  let flowField = [];
  let fieldResolution = 20;
  let noiseOffset = 0;

  p.setup = () => {
    // const container = document.getElementById("sketch-container");
    const canvas = p.createCanvas(800, 800);
    // canvas.parent(container);
    p.colorMode(p.HSB, 360, 100, 100, 100);
    p.noStroke();

    // 입자 초기화
    for (let i = 0; i < 200; i++) {
      particles.push({
        x: p.random(p.width),
        y: p.random(p.height),
        size: p.random(8, 15),
        speed: p.random(0.5, 1),
        hue: p.random(60),
        angle: 0,
        trail: [],
      });
    }

    // 흐름장 초기화
    initFlowField();
  };

  function initFlowField() {
    flowField = [];
    for (let x = 0; x < p.width; x += fieldResolution) {
      for (let y = 0; y < p.height; y += fieldResolution) {
        let angle = p.noise(x * 0.01, y * 0.01) * p.TWO_PI * 2;
        flowField.push({
          x: x,
          y: y,
          angle: angle,
        });
      }
    }
  }

  p.draw = () => {
    // 반투명 배경으로 궤적 효과
    p.push();
    p.fill(0, 0, 0, 5);
    p.rect(0, 0, p.width, p.height);
    p.pop();

    // 흐름장 업데이트
    updateFlowField();

    // 입자 업데이트 및 그리기
    for (let particle of particles) {
      updateParticle(particle);
      drawParticle(particle);
    }

    // 간헐적으로 새로운 입자 추가
    if (p.frameCount % 60 === 0) {
      particles.push({
        x: p.random(p.width),
        y: p.random(p.height),
        size: p.random(3, 10),
        speed: p.random(0.3, 1.5),
        hue: p.random(360),
        angle: 0,
        trail: [],
      });

      // 입자 수 제한
      if (particles.length > 300) {
        particles.splice(0, 50);
      }
    }

    noiseOffset += 0.01;
  };

  function updateFlowField() {
    for (let cell of flowField) {
      // 노이즈로 각도 업데이트 (미로처럼 변화)
      let noiseVal = p.noise(cell.x * 0.005, cell.y * 0.005, noiseOffset);
      cell.angle = noiseVal * p.TWO_PI * 3;
    }
  }

  function updateParticle(particle) {
    // 가장 가까운 흐름장 셀 찾기
    let nearestCell = getNearestFlowField(particle.x, particle.y);

    // 입자 각도 업데이트
    particle.angle = p.lerp(particle.angle, nearestCell.angle, 0.1);

    // 위치 업데이트
    particle.x += p.cos(particle.angle) * particle.speed;
    particle.y += p.sin(particle.angle) * particle.speed;

    // 궤적 기록 (최근 10개만 유지)
    particle.trail.push({ x: particle.x, y: particle.y });
    if (particle.trail.length > 10) {
      particle.trail.shift();
    }

    // 화면 경계 처리
    if (particle.x < 0) particle.x = p.width;
    if (particle.x > p.width) particle.x = 0;
    if (particle.y < 0) particle.y = p.height;
    if (particle.y > p.height) particle.y = 0;
  }

  function getNearestFlowField(x, y) {
    let nearest = flowField[0];
    let minDist = p.dist(x, y, nearest.x, nearest.y);

    for (let cell of flowField) {
      let d = p.dist(x, y, cell.x, cell.y);
      if (d < minDist) {
        minDist = d;
        nearest = cell;
      }
    }

    return nearest;
  }

  function drawParticle(particle) {
    // 궤적 그리기
    p.push();
    for (let i = 0; i < particle.trail.length; i++) {
      let point = particle.trail[i];
      let alpha = p.map(i, 0, particle.trail.length, 20, 100);
      let size = p.map(
        i,
        0,
        particle.trail.length,
        particle.size * 0.5,
        particle.size
      );

      p.fill(particle.hue, 80, 90, alpha);
      p.ellipse(point.x, point.y, size, size);
    }
    p.pop();

    // 입자 본체 그리기
    p.push();
    p.translate(particle.x, particle.y);
    p.rotate(particle.angle);

    // 색상 변화
    let hueShift =
      p.sin(p.frameCount * 0.05 + particle.hue * 0.01) * 30 + particle.hue;
    p.fill(hueShift % 360, 80, 100);

    // 모양 그리기 (사각형과 원 번갈아가며)
    if (p.frameCount % 120 < 60) {
      p.rect(0, 0, particle.size * 2, particle.size * 2);
    } else {
      p.ellipse(0, 0, particle.size * 2, particle.size * 2);
    }

    p.pop();
  }

  p.mousePressed = () => {
    // 클릭 위치에 새 입자 추가
    particles.push({
      x: p.mouseX,
      y: p.mouseY,
      size: p.random(5, 15),
      speed: p.random(0.5, 2),
      hue: p.random(360),
      angle: 0,
      trail: [],
    });
  };

  p.keyPressed = () => {
    if (p.key === " ") {
      // 스페이스바로 흐름장 재생성
      initFlowField();
    } else if (p.key === "c") {
      // C 키로 화면 클리어
      p.background(0);
    }
  };
};

new p5(geometricWaveSketch);

```

***

## 21. script

**파일 경로:** `251219_map_constrain\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// 도형의 최소/최대 크기 설정
const MIN_SIZE = 300;
const MAX_SIZE = 700;

// 다각형의 최소/최대 정점 수 설정 (3~128 사이)
const MIN_VERTICES = 3;
const MAX_VERTICES = 128;

function setup() {
  createCanvas(800, 800);
  angleMode(DEGREES); // 각도 단위를 Degree(도)로 설정
  noStroke();
}

function draw() {
  background(25); // 어두운 배경

  // 1. 화면 중앙을 기준으로 마우스 위치 계산
  // centerDeltaX: 마우스 X좌표와 중심 X좌표 간의 차이 (-width/2 ~ +width/2)
  let centerDeltaX = mouseX - width / 2;
  // centerDeltaY: 마우스 Y좌표와 중심 Y좌표 간의 차이 (-height/2 ~ +height/2)
  let centerDeltaY = mouseY - height / 2;

  // --- 매핑 및 제한 ---

  // 2. X축 위치를 '색상 값 (Hue)'으로 매핑 및 제한
  // centerDeltaX 범위: -width/2 ~ width/2
  // 목표 색상 범위 (HUE): 0 ~ 360 (색상환 전체)
  let mappedHue = map(centerDeltaX, -width / 2, width / 2, 0, 360);

  // map() 함수는 stop1, stop2를 벗어난 값도 비례적으로 계산하므로,
  // 원하지 않는다면 constrain으로 범위를 명확히 제한합니다. (옵션)
  let finalHue = constrain(mappedHue, 0, 360);

  // 3. Y축 위치를 '크기'로 매핑 및 제한
  // centerDeltaY 범위: -height/2 ~ height/2
  // 목표 크기 범위: MIN_SIZE ~ MAX_SIZE
  let mappedSize = map(
    centerDeltaY,
    -height / 2,
    height / 2,
    MIN_SIZE,
    MAX_SIZE
  );
  let finalSize = constrain(mappedSize, MIN_SIZE, MAX_SIZE);

  // 4. 마우스와 중앙 사이의 '거리'를 '정점 수'로 매핑 및 제한
  // dist() 함수로 마우스와 중앙(width/2, height/2) 사이의 거리 계산 (0 ~ max_diagonal)
  let distFromCenter = dist(mouseX, mouseY, width / 2, height / 2);
  let maxDist = dist(0, 0, width / 2, height / 2); // 최대 거리 (코너까지)

  // 거리 범위: 0 ~ maxDist
  // 목표 정점 수 범위: MIN_VERTICES ~ MAX_VERTICES
  let mappedVertices = map(
    distFromCenter,
    0,
    maxDist,
    MIN_VERTICES,
    MAX_VERTICES
  );

  // 정점 수는 정수여야 하므로, mappedVertices를 정수화 (round, floor, ceil 중 택 1)
  let rawVertices = round(mappedVertices);

  // 다각형 정점 수를 3~128 사이로 최종 제한
  let finalVertices = constrain(rawVertices, MIN_VERTICES, MAX_VERTICES);

  // --- 도형 그리기 ---

  // HSB(Hue, Saturation, Brightness) 컬러 모드 설정
  colorMode(HSB, 360, 100, 100);
  fill(finalHue, 100, 100); // 매핑된 HUE 값으로 채우기

  // 도형을 화면 중앙에 위치시킵니다.
  push();
  translate(width / 2, height / 2);

  // 다각형 그리기 함수 호출
  drawPolygon(0, 0, finalSize / 2, finalVertices); // finalSize를 지름으로 사용

  pop();

  // --- 정보 표시 ---
  colorMode(RGB); // 다시 기본 RGB 모드로 전환
  fill(255);
  textAlign(CENTER, TOP);
  textSize(25);
  text(`Hue (by X): ${round(finalHue)}`, width / 2, 20);
  text(`Size (by Y): ${round(finalSize)}`, width / 2, 50);
  text(`Vertices (by Dist): ${finalVertices}`, width / 2, 80);
  text(`Distance from centre: ${round(distFromCenter)}`, width / 2, 750);

  noStroke();
  fill(15);
  ellipse(width / 2, height / 2, 20);
}

/**
 * 지정된 위치에 정다각형을 그리는 함수
 * @param x x 좌표
 * @param y y 좌표
 * @param radius 반지름
 * @param npoints 정점 수
 */
function drawPolygon(x, y, radius, npoints) {
  let angle = 360 / npoints; // 각 정점 사이의 각도
  beginShape();
  for (let a = 0; a < 360; a += angle) {
    let sx = x + cos(a) * radius;
    let sy = y + sin(a) * radius;
    vertex(sx, sy);
  }
  endShape(CLOSE);
}
```

***

## 22. script

**파일 경로:** `251219_sound_interaction\script.js`

**설명:** script.js - p5.js 스케치

```javascript
let mic;
let amp;
let started = false;

function setup() {
  createCanvas(800, 800);
  background(15);

  mic = new p5.AudioIn();
  amp = new p5.Amplitude();
}

function draw() {
  background(15, 40);

  if (!started) {
    fill(200);
    textAlign(CENTER, CENTER);
    textSize(16);
    text("화면을 클릭하면 마이크가 활성화됩니다", width / 2, height / 2);
    return;
  }

  let level = amp.getLevel();

  let radius = map(level, 0.002, 0.07, 30, 330);
  radius = constrain(radius, 30, 330);

  noStroke();
  fill(255, 150);
  ellipse(width / 2, height / 2, radius * 2, radius * 2);
}

function mousePressed() {
  // 오디오 컨텍스트 강제 시작
  userStartAudio();

  mic.start(() => {
    amp.setInput(mic);
    started = true;
    console.log("마이크 시작됨");
  });
}

```

***

## 23. script

**파일 경로:** `251221_greeting_dots\script.js`

**설명:** script.js - p5.js 스케치

```javascript
const WIN_SIZE = 800;
const NUM_POINTS = 1500;
const RANGE = 200.0;

let points = [];

function setup() {
  createCanvas(WIN_SIZE, WIN_SIZE);
  noStroke();
  for (let i = 0; i < NUM_POINTS; i++) {
    points.push(randomPoint());
  }
}

function draw() {
  background(0);
  translate(width / 2, height / 2);

  
  // 점 업데이트
  for (let i = points.length - 1; i >= 0; i--) {
    points[i].pos.y += points[i].speed;
    
    // 화면 아래로 나간 점 제거
    if (points[i].pos.y > height / 2 + 50) {
      points.splice(i, 1);
    }
  }
  
  // 점 개수 유지
  while (points.length < NUM_POINTS) {
    points.push(randomPoint());
  }
  
  // 점 그리기
  for (let p of points) {
    let dist = p5.Vector.dist(p.pos, createVector(0, 0));
    if (dist < RANGE) {
      fill(200);
      ellipse(p.pos.x, p.pos.y, 4, 4);
      
      // 거리 비율에 따른 알파값 계산
      let alpha = map(dist, 0, RANGE, 255, 0);
      stroke(255, alpha);
      strokeWeight(0.5);
      line(0, 0, p.pos.x, p.pos.y);
      noStroke();
    } else {
      fill(128);
      ellipse(p.pos.x, p.pos.y, 3, 3);
    }
  }
  // 중앙 노란색 원 그리기
  fill(200, 170, 0);
  noStroke();
  ellipse(0, 0, 12, 12);
}

function randomPoint() {
  return {
    pos: createVector(
      random(-WIN_SIZE / 2, WIN_SIZE / 2),
      random(-WIN_SIZE / 2 - 100, -WIN_SIZE / 2)
    ),
    speed: random(0.5, 2.0),
  };
}

```

***

## 24. script

**파일 경로:** `251221_make_your_constellation\script.js`

**설명:** script.js - p5.js 스케치

```javascript
let particles = [];
let showTitle = true;
let isFullscreen = false;
let grid = {};
let cellSize = 150;
let now = 0;
let lastTime = 0;

function setup() {
  createCanvas(1080, 1080);
  colorMode(HSB, 1.0);
  textAlign(CENTER, CENTER);
  frameRate(60);
  noLoop();
}

function draw() {
  let dt = millis() / 1000.0 - lastTime;
  lastTime = millis() / 1000.0;
  now = millis() / 1000.0;
  background(0);

  // 파티클 업데이트
  for (let i = particles.length - 1; i >= 0; i--) {
    particles[i].update(dt);
    if (!particles[i].alive(now)) {
      particles.splice(i, 1);
    }
  }

  // 파티클 그리기
  for (let p of particles) {
    let lr = p.lifeRatio(now);
    if (lr <= 0) continue;
    fill(p.color[0], p.color[1], p.color[2], lr);
    noStroke();
    ellipse(p.pos.x, p.pos.y, p.size * 2);
  }

  // 파티클 연결선 그리기
  for (let cellKey in grid) {
    let indices = grid[cellKey];
    if (!indices) continue;

    for (let i of indices) {
      let a = particles[i];
      if (!a || !a.alive(now)) continue;

      let [cellX, cellY] = cellKey.split(",").map(Number);
      for (let dx = -1; dx <= 1; dx++) {
        for (let dy = -1; dy <= 1; dy++) {
          let neighborCellKey = `${cellX + dx},${cellY + dy}`;
          let neighborIndices = grid[neighborCellKey];
          if (!neighborIndices) continue;

          for (let j of neighborIndices) {
            if (j <= i) continue;
            let b = particles[j];
            if (!b || !b.alive(now)) continue;

            let dSq = distSquared(a.pos, b.pos);
            if (dSq <= cellSize * cellSize) {
              let d = sqrt(dSq);
              let distAlpha = 1.0 - d / cellSize;
              let alpha = distAlpha * a.lifeRatio(now) * b.lifeRatio(now);

              if (alpha > 0.01) {
                stroke(
                  (a.color[0] + b.color[0]) / 2,
                  (a.color[1] + b.color[1]) / 2,
                  (a.color[2] + b.color[2]) / 2,
                  alpha
                );
                strokeWeight(0.8);
                line(a.pos.x, a.pos.y, b.pos.x, b.pos.y);
              }
            }
          }
        }
      }
    }
  }

  // 타이틀 화면
  if (showTitle) {
    fill(1.0);
    textSize(120);
    text("CONSTELLATION", width / 2, 150);

    fill(0.75, 0.75, 1.0);
    textSize(36);
    text("Create your own star constellations", width / 2, 0);

    fill(0.5);
    textSize(24);
    text(
      "CLICK TO CREATE STARS  •  F: FULLSCREEN  •  S: SCREENSHOT  •  N: NEW",
      width / 2,
      height - 150
    );
  } else {
    fill(0.5);
    textSize(18);
    text(`Particles: ${particles.length}`, 50, height - 50);
  }
}

function mousePressed() {
  if (showTitle) {
    showTitle = false;
    loop();
    return;
  }

  let count = floor(random(15, 31));
  for (let i = 0; i < count; i++) {
    particles.push(new Particle(createVector(mouseX, mouseY), now));
  }
  updateGrid();
}

function keyPressed() {
  if (key === "F" || key === "f") {
    let fs = fullscreen();
    fullscreen(!fs);
    isFullscreen = !fs;
  } else if (key === "S" || key === "s") {
    saveCanvas(`constellation_${floor(now)}`, "png");
    console.log("📸 Screenshot saved!");
  } else if (key === "N" || key === "n") {
    particles = [];
    grid = {};
    showTitle = true;
    noLoop();
    redraw();
  } else if (key === "C" || key === "c") {
    particles = particles.filter((p) => p.alive(now));
    updateGrid();
  }
}

function distSquared(a, b) {
  let dx = a.x - b.x;
  let dy = a.y - b.y;
  return dx * dx + dy * dy;
}

class Particle {
  constructor(origin, now) {
    this.pos = origin.copy();
    this.born = now;
    this.lifetime = random(7.0, 10.0);
    this.size = random(1.0, 5.0);

    let angle = random(TWO_PI);
    let speed = random(10.0, 80.0);
    this.vel = createVector(cos(angle) * speed, sin(angle) * speed);

    let hue = random(0.5, 0.72);
    this.color = [hue, 0.7, 0.5];
  }

  age(now) {
    return now - this.born;
  }

  lifeRatio(now) {
    return 1.0 - constrain(this.age(now) / this.lifetime, 0.0, 1.0);
  }

  alive(now) {
    return this.age(now) < this.lifetime;
  }

  update(dt) {
    this.pos.add(p5.Vector.mult(this.vel, dt));
    this.vel.mult(0.995);
  }
}

function updateGrid() {
  grid = {};
  for (let i = 0; i < particles.length; i++) {
    let p = particles[i];
    let cellX = floor(p.pos.x / cellSize);
    let cellY = floor(p.pos.y / cellSize);
    let cellKey = `${cellX},${cellY}`;
    if (!grid[cellKey]) grid[cellKey] = [];
    grid[cellKey].push(i);
  }
}

function windowResized() {
  resizeCanvas(1080, 1080);
}

```

***

## 25. script

**파일 경로:** `251221_particle_flower_blooming\script.js`

**설명:** script.js - p5.js 스케치

```javascript
let particles = [];
let perlin;
let time = 0;
let frameCount = 0;

function setup() {
  createCanvas(800, 800);
  colorMode(HSB, 1.0);
  perlin = new p5.Noise();
  spawnParticles();
}

function spawnParticles() {
  particles = [];
  const center = createVector(width / 2, height / 2);

  for (let i = 0; i < 1000; i++) {
    let angle = random(TWO_PI);
    let velocity = p5.Vector.fromAngle(angle).mult(random(1, 3));

    particles.push({
      position: center.copy(),
      velocity: velocity,
      history: [],
      hue: random(1.0),
    });
  }
}

function draw() {
  time += 0.02;
  frameCount++;

  // 일정 시간(300프레임 ≈ 5초)마다 중앙에서 다시 폭발
  if (frameCount % 300 === 0) {
    spawnParticles();
  }

  // 반투명 검은 배경 (잔상 효과)
  background(0, 0, 0, 0.1);

  // 파티클 업데이트
  for (let p of particles) {
    let n = noise(
      p.position.x * 0.5,
      p.position.y * 0.5,
      time
    );

    let angleOffset = map(n, 0, 1, -0.3, 0.3);

    let currentAngle = p.velocity.heading();
    let newAngle = currentAngle + angleOffset;
    let speed = p.velocity.mag() * 1.01; // 점점 빨라짐

    p.velocity = p5.Vector.fromAngle(newAngle).mult(speed);
    p.position.add(p.velocity);

    p.history.push(p.position.copy());
    if (p.history.length > 25) {
      p.history.shift();
    }

    // 궤적 그리기
    if (p.history.length > 1) {
      stroke(p.hue, 0.9, 0.6, 0.6);
      strokeWeight(0.8);
      noFill();
      beginShape();
      for (let pt of p.history) {
        vertex(pt.x, pt.y);
      }
      endShape();
    }
  }
}

// p5.Noise 클래스 정의 (p5.js에 기본적으로 없으므로 추가)
p5.Noise = function() {
  this.octaves = 4;
  this.falloff = 0.5;
};

p5.Noise.prototype.get = function(x, y, z) {
  return noise(x, y, z);
};

let p = new p5.Noise();
noiseDetail(2, 0.5);

```

***

## 26. script

**파일 경로:** `251222_pointing_triangle\script.js`

**설명:** script.js - p5.js 스케치

```javascript
const TRI_SIZE = 30;
const SPACING = 30;
const BRIGHT_RANGE = 600;
const ROTATION_EASING = 0.1;

const HUE_MIN = 180;
const HUE_MAX = 240;
const BRIGHTNESS_NEAR = 90;
const BRIGHTNESS_FAR = 10;

let grid;

function setup() {
  createCanvas(windowWidth, windowHeight);
  colorMode(HSB, 360, 100, 100);
  grid = new TriangleGrid();
}

function draw() {
  background(0);
  grid.update();
  grid.display();
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
  grid = new TriangleGrid();
}

class TriangleGrid {
  constructor() {
    this.cols = ceil(width / SPACING);
    this.rows = ceil(height / SPACING);
    this.triangles = [];
    this.initializeTriangles();
  }

  initializeTriangles() {
    for (let i = 0; i < this.cols; i++) {
      for (let j = 0; j < this.rows; j++) {
        let x = SPACING / 2 + i * SPACING;
        let y = SPACING / 2 + j * SPACING;
        this.triangles.push(new Triangle(x, y));
      }
    }
  }

  update() {
    for (let tri of this.triangles) {
      tri.update();
    }
  }

  display() {
    for (let tri of this.triangles) {
      tri.display();
    }
  }
}

class Triangle {
  constructor(x, y) {
    this.x = x;
    this.y = y;
    this.currentAngle = 0;
  }

  update() {
    let targetAngle = atan2(mouseY - this.y, mouseX - this.x);

    let diff = targetAngle - this.currentAngle;
    if (diff > PI) diff -= TWO_PI;
    if (diff < -PI) diff += TWO_PI;

    this.currentAngle += diff * ROTATION_EASING;
  }

  display() {
    let distance = dist(mouseX, mouseY, this.x, this.y);

    let hue = map(distance, 0, BRIGHT_RANGE, HUE_MIN, HUE_MAX, true);

    let brightness;
    if (distance < BRIGHT_RANGE) {
      let t = distance / BRIGHT_RANGE;
      let easedT = 1 - pow(1 - t, 3);
      brightness = lerp(BRIGHTNESS_NEAR, BRIGHTNESS_FAR, easedT);
    } else {
      brightness = BRIGHTNESS_FAR;
    }

    noStroke();
    fill(hue, 100, brightness);

    push();

    translate(this.x, this.y);
    rotate(this.currentAngle);
    beginShape();
    vertex(TRI_SIZE / 2, 0);
    vertex(-TRI_SIZE / 2, -TRI_SIZE / 6);
    vertex(-TRI_SIZE / 2, TRI_SIZE / 6);
    endShape(CLOSE);

    pop();
  }
}

```

***

## 27. script

**파일 경로:** `251222_shooting_marbles\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// 게임 설정 상수
const CONFIG = {
  PLAYER: {
    SIZE: 100,
    MAX_SPEED: 8,
    Y_RATIO: 7 / 8,
  },
  BULLET: {
    INITIAL_SIZE: 30,
    INITIAL_ALPHA: 100,
    MIN_SIZE: 2,
    MIN_ALPHA: 15,
    LERP_SPEED: 0.05,
    TARGET_X_RATIO: 0.5,
    TARGET_Y_RATIO: 0.25,
  },
  ENEMY: {
    INITIAL_SIZE_MIN: 10,
    INITIAL_SIZE_MAX: 15,
    MAX_SIZE_MIN: 30,
    MAX_SIZE_MAX: 40,
    INITIAL_ALPHA: 100,
    MAX_ALPHA: 255,
    SIZE_LERP: 0.02,
    ALPHA_LERP: 0.03,
    POSITION_LERP: 0.01,
    BOTTOM_THRESHOLD: 10,
    MIN_X: 50,
  },
  PARTICLE: {
    COUNT_MIN: 30,
    COUNT_MAX: 51,
    SIZE_MIN: 3,
    SIZE_MAX: 8,
    SPEED_MIN: 2,
    SPEED_MAX: 8,
    ALPHA_DECAY: 4,
    GRAVITY: 0.1,
  },
  COLLISION: {
    COOLDOWN: 1000,
    ENERGY_LOSS: 10,
  },
  ENERGY: {
    MAX: 100,
    BAR_WIDTH: 300,
    BAR_HEIGHT: 20,
    BAR_Y: 120,
  },
  HUE: {
    MIN: 0,
    MAX: 60,
  },
};

// 게임 상태
let gameState = {
  player: null,
  bullets: [],
  enemies: [],
  particles: [],
  playerSpeed: 0,
  targetSpeed: 0,
  score: 0,
  energy: CONFIG.ENERGY.MAX,
  gameOver: false,
  keys: {},
  lastCollisionTime: 0,
};

function setup() {
  createCanvas(windowWidth, windowHeight);
  initGame();
  colorMode(HSB, 360, 100, 100, 255);
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
  if (gameState.player) {
    gameState.player.y = height * CONFIG.PLAYER.Y_RATIO;
  }
}

function draw() {
  background(15);

  if (gameState.gameOver) {
    displayGameOver();
    return;
  }

  updateGame();
  renderGame();
  displayUI();
}

function initGame() {
  gameState = {
    player: {
      x: width / 2,
      y: height * CONFIG.PLAYER.Y_RATIO,
      size: CONFIG.PLAYER.SIZE,
      maxSpeed: CONFIG.PLAYER.MAX_SPEED,
    },
    bullets: [],
    enemies: [],
    particles: [],
    playerSpeed: 0,
    targetSpeed: 0,
    score: 0,
    energy: CONFIG.ENERGY.MAX,
    gameOver: false,
    keys: {},
    lastCollisionTime: 0,
  };
}

function updateGame() {
  handlePlayerInput();
  updatePlayer();
  updateBullets();
  updateEnemies();
  updateParticles();
  checkPlayerEnemyCollision();
  checkEnergyGameOver();
}

function updatePlayer() {
  const { player } = gameState;
  gameState.playerSpeed = lerp(
    gameState.playerSpeed,
    gameState.targetSpeed,
    0.2
  );
  player.x += gameState.playerSpeed;
  player.x = constrain(player.x, player.size / 2, width - player.size / 2);
}

function updateBullets() {
  const targetX = width * CONFIG.BULLET.TARGET_X_RATIO;
  const targetY = height * CONFIG.BULLET.TARGET_Y_RATIO;

  for (let i = gameState.bullets.length - 1; i >= 0; i--) {
    const b = gameState.bullets[i];

    // 위치 및 속성 업데이트
    b.x = lerp(b.x, targetX, CONFIG.BULLET.LERP_SPEED);
    b.y = lerp(b.y, targetY, CONFIG.BULLET.LERP_SPEED);
    b.size = lerp(b.size, 0, CONFIG.BULLET.LERP_SPEED);
    b.alpha = lerp(b.alpha, 10, CONFIG.BULLET.LERP_SPEED);

    // 총알과 적 충돌 체크
    if (checkBulletEnemyCollision(b, i)) {
      continue; // 충돌 시 다음 총알로
    }

    // 총알 제거 및 적 생성
    if (b.size < CONFIG.BULLET.MIN_SIZE || b.alpha < CONFIG.BULLET.MIN_ALPHA) {
      spawnEnemy(b.x, b.y);
      gameState.bullets.splice(i, 1);
    }
  }
}

function updateEnemies() {
  for (let i = gameState.enemies.length - 1; i >= 0; i--) {
    const e = gameState.enemies[i];

    // 위치 및 속성 업데이트
    e.x = lerp(e.x, e.targetX, CONFIG.ENEMY.POSITION_LERP);
    e.y = lerp(e.y, height, CONFIG.ENEMY.POSITION_LERP);
    e.size = lerp(e.size, e.maxSize, CONFIG.ENEMY.SIZE_LERP);
    e.alpha = lerp(e.alpha, CONFIG.ENEMY.MAX_ALPHA, CONFIG.ENEMY.ALPHA_LERP);

    // 바닥 충돌 체크 - 게임오버
    if (e.y >= height - CONFIG.ENEMY.BOTTOM_THRESHOLD) {
      gameState.gameOver = false;
      return;
    }
  }
}

function updateParticles() {
  for (let i = gameState.particles.length - 1; i >= 0; i--) {
    const p = gameState.particles[i];

    p.x += p.vx;
    p.y += p.vy;
    p.vy += CONFIG.PARTICLE.GRAVITY;
    p.alpha -= CONFIG.PARTICLE.ALPHA_DECAY;

    if (p.alpha <= 0) {
      gameState.particles.splice(i, 1);
    }
  }
}

function checkBulletEnemyCollision(bullet, bulletIndex) {
  for (let j = gameState.enemies.length - 1; j >= 0; j--) {
    const e = gameState.enemies[j];
    const distance = dist(bullet.x, bullet.y, e.x, e.y);

    if (distance < bullet.size / 2 + e.size / 2) {
      createParticles(bullet.x, bullet.y, e.hue);
      gameState.score++;
      gameState.bullets.splice(bulletIndex, 1);
      gameState.enemies.splice(j, 1);
      return true;
    }
  }
  return false;
}

function checkPlayerEnemyCollision() {
  const { player } = gameState;
  const currentTime = millis();

  // 무적시간 체크
  if (currentTime - gameState.lastCollisionTime < CONFIG.COLLISION.COOLDOWN) {
    return;
  }

  for (let i = gameState.enemies.length - 1; i >= 0; i--) {
    const e = gameState.enemies[i];
    const distance = dist(player.x, player.y, e.x, e.y);

    if (distance < player.size / 2 + e.size / 2) {
      gameState.energy -= CONFIG.COLLISION.ENERGY_LOSS;
      gameState.lastCollisionTime = currentTime;
      createParticles(player.x, player.y, e.hue);
      gameState.enemies.splice(i, 1);

      // 충돌 피드백
      background(255, 50, 50, 50);
    }
  }
}

function checkEnergyGameOver() {
  if (gameState.energy <= 0) {
    gameState.gameOver = true;
  }
}

function renderGame() {
  renderPlayer();
  renderBullets();
  renderEnemies();
  renderParticles();
  renderInvincibilityEffect();
}

function renderPlayer() {
  const { player } = gameState;
  colorMode(RGB);
  fill(255);
  noStroke();
  circle(player.x, player.y, player.size);
}

function renderBullets() {
  colorMode(RGB);
  gameState.bullets.forEach((b) => {
    fill(255, b.alpha);
    circle(b.x, b.y, b.size);
  });
}

function renderEnemies() {
  colorMode(HSB);
  gameState.enemies.forEach((e) => {
    fill(e.hue, 100, 100, e.alpha);
    circle(e.x, e.y, e.size);
  });
}

function renderParticles() {
  gameState.particles.forEach((p) => {
    if (p.type === "white") {
      colorMode(RGB);
      fill(255, p.alpha);
    } else {
      colorMode(HSB);
      fill(p.hue, 100, 100, p.alpha);
    }
    circle(p.x, p.y, p.size);
  });
}

function renderInvincibilityEffect() {
  const { player } = gameState;
  const timeSinceCollision = millis() - gameState.lastCollisionTime;

  if (timeSinceCollision < CONFIG.COLLISION.COOLDOWN) {
    const pulse = sin(timeSinceCollision * 0.02) * 0.5 + 0.5;

    colorMode(RGB);
    noFill();
    stroke(255, 0, 0, 100 * pulse);
    strokeWeight(5);
    circle(player.x, player.y, player.size + 10);

    // 깜빡임 효과
    const blink = floor(timeSinceCollision / 100) % 2;
    if (blink === 0) {
      fill(255, 150);
      noStroke();
      circle(player.x, player.y, player.size);
    }
  }
}

function displayUI() {
  displayScore();
  displayEnergy();
}

function displayScore() {
  colorMode(RGB);
  fill(255);
  textSize(32);
  textAlign(CENTER);
  text(`Score: ${gameState.score}`, width / 2, 50);
}

function displayEnergy() {
  const { energy } = gameState;
  colorMode(RGB);
  fill(255);
  textSize(32);
  textAlign(CENTER);
  text(`Energy: ${energy}`, width / 2, 90);

  // 에너지 바
  const barX = width / 2 - CONFIG.ENERGY.BAR_WIDTH / 2;
  const energyPercent = energy / CONFIG.ENERGY.MAX;

  // 배경
  fill(50);
  rect(
    barX,
    CONFIG.ENERGY.BAR_Y,
    CONFIG.ENERGY.BAR_WIDTH,
    CONFIG.ENERGY.BAR_HEIGHT,
    5
  );

  // 에너지 바 색상
  let energyColor;
  if (energyPercent > 0.7) {
    energyColor = color(0, 255, 0);
  } else if (energyPercent > 0.3) {
    energyColor = color(255, 255, 0);
  } else {
    energyColor = color(255, 0, 0);
  }

  fill(energyColor);
  rect(
    barX,
    CONFIG.ENERGY.BAR_Y,
    CONFIG.ENERGY.BAR_WIDTH * energyPercent,
    CONFIG.ENERGY.BAR_HEIGHT,
    5
  );
}

function displayGameOver() {
  colorMode(RGB);
  fill(30, 30, 200, 100);
  rect(0, 0, width, height);

  fill(255);
  textSize(64);
  textAlign(CENTER);
  text("GAME OVER", width / 2, height / 2 - 50);

  textSize(32);
  text(`Final Score: ${gameState.score}`, width / 2, height / 2 + 20);

  textSize(24);
  fill(200);
  text("Press R to restart", width / 2, height / 2 + 80);
}

function handlePlayerInput() {
  const { keys } = gameState;
  gameState.targetSpeed = 0;

  if (keys[LEFT_ARROW] || keys[65]) {
    gameState.targetSpeed = -gameState.player.maxSpeed;
  }
  if (keys[RIGHT_ARROW] || keys[68]) {
    gameState.targetSpeed = gameState.player.maxSpeed;
  }

  // 양방향 동시 입력 시 취소
  if ((keys[LEFT_ARROW] || keys[65]) && (keys[RIGHT_ARROW] || keys[68])) {
    gameState.targetSpeed = 0;
  }
}

function keyPressed() {
  if (gameState.gameOver && (key === "r" || key === "R")) {
    initGame();
    return;
  }

  if (gameState.gameOver) return;

  gameState.keys[keyCode] = true;

  if (key === "a" || key === "A") gameState.keys[65] = true;
  if (key === "d" || key === "D") gameState.keys[68] = true;

  if (key === " ") {
    shootBullet();
  }
}

function keyReleased() {
  gameState.keys[keyCode] = false;

  if (key === "a" || key === "A") gameState.keys[65] = false;
  if (key === "d" || key === "D") gameState.keys[68] = false;
}

function shootBullet() {
  const { player } = gameState;
  gameState.bullets.push({
    x: player.x,
    y: player.y,
    size: CONFIG.BULLET.INITIAL_SIZE,
    alpha: CONFIG.BULLET.INITIAL_ALPHA,
  });
}

function spawnEnemy(x, y) {
  const startX = width * CONFIG.BULLET.TARGET_X_RATIO;
  const startY = height * CONFIG.BULLET.TARGET_Y_RATIO;
  const targetX = random(CONFIG.ENEMY.MIN_X, width - CONFIG.ENEMY.MIN_X);
  const hueValue = random(CONFIG.HUE.MIN, CONFIG.HUE.MAX);
  const initialSize = random(
    CONFIG.ENEMY.INITIAL_SIZE_MIN,
    CONFIG.ENEMY.INITIAL_SIZE_MAX
  );
  const maxSize = random(CONFIG.ENEMY.MAX_SIZE_MIN, CONFIG.ENEMY.MAX_SIZE_MAX);

  gameState.enemies.push({
    x: startX,
    y: startY,
    size: initialSize,
    maxSize: maxSize,
    alpha: CONFIG.ENEMY.INITIAL_ALPHA,
    hue: hueValue,
    targetX: targetX,
  });
}

function createParticles(x, y, hue = null) {
  const particleCount = floor(
    random(CONFIG.PARTICLE.COUNT_MIN, CONFIG.PARTICLE.COUNT_MAX)
  );

  for (let i = 0; i < particleCount; i++) {
    const angle = random(TWO_PI);
    const speed = random(CONFIG.PARTICLE.SPEED_MIN, CONFIG.PARTICLE.SPEED_MAX);

    gameState.particles.push({
      x: x,
      y: y,
      vx: cos(angle) * speed,
      vy: sin(angle) * speed,
      size: random(CONFIG.PARTICLE.SIZE_MIN, CONFIG.PARTICLE.SIZE_MAX),
      alpha: 100,
      hue: hue,
      type: hue !== null ? "enemy" : "white",
    });
  }
}

```

***

## 28. script

**파일 경로:** `251223_arc_animation\script.js`

**설명:** script.js - p5.js 스케치

```javascript
const num = 20;
let step, theta;

function setup() {
  createCanvas(600, 600);
  strokeWeight(5);
  step = 22;
  theta = 0;
}

function draw() {
  background(20);
  translate(width / 2, height * 0.75);

  for (let i = 0; i < num; i++) {
    stroke(200);
    noFill();
    const size = i * step;
    const offSet = (TWO_PI / num) * i;
    // sin 값을 0.001~1 범위로 매핑하여 arcEnd가 항상 PI보다 크도록 설정
    const arcEnd = map(sin(theta + offSet), -1, 1, PI + 0.001, TWO_PI);
    arc(0, 0, size, size, PI, arcEnd);
  }
  resetMatrix();
  theta += 0.03;
}

```

***

## 29. script

**파일 경로:** `251223_fake_sphere_particle\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// ==========================================
// [설정 영역] 전역 상수 및 변수
// ==========================================

const PARTICLE_COUNT = 8000; 
const ATTRACTION = 0.01; 
const DAMPING = 0.8; 
const REPEL_STRENGTH = 38; 

const CANVAS_WIDTH = 800; 
const CANVAS_HEIGHT = 800; 
const SPHERE_RADIUS = 350; 
const REPEL_RADIUS = 120; 

let angle = 0; 
let points = []; 

// ==========================================
// [p5.js 라이프사이클]
// ==========================================

function setup() {
  createCanvas(CANVAS_WIDTH, CANVAS_HEIGHT);
  pixelDensity(1);

  stroke(255);
  strokeWeight(2);

  // 파티클 초기화
  initializeParticles();

  // 초기 위치 설정
  angle = 0;
  updateParticleTargets();

  // 속도 초기화
  for (let p of points) {
    p.vel.set(0, 0);
  }
}

function draw() {
  background(0);
  translate(width / 2, height / 2);

  // 마우스 위치 (캔버스 중심 기준)
  const mousePos = createVector(mouseX - width / 2, mouseY - height / 2);

  // 모든 파티클 업데이트 및 렌더링
  updateAndRenderParticles(mousePos);

  // 구체 회전
  angle += 0.01;
}

// ==========================================
// [초기화 함수]
// ==========================================

function initializeParticles() {
  points = [];
  for (let i = 0; i < PARTICLE_COUNT; i++) {
    points.push({
      index: i,
      pos: createVector(0, 0),
      vel: createVector(0, 0),
    });
  }
}

// 파티클의 초기 "홈" 위치 설정
function updateParticleTargets() {
  for (let p of points) {
    const i = p.index;
    const x = sin(i + angle) * sin(i * i) * SPHERE_RADIUS;
    const y = cos(i * i) * SPHERE_RADIUS;
    p.pos.set(x, y);
  }
}

// ==========================================
// [파티클 물리 시뮬레이션]
// ==========================================

function updateAndRenderParticles(mousePos) {
  for (let p of points) {
    const i = p.index;

    // 1. 회전하는 "홈" 위치 계산
    const homeX = sin(i + angle) * sin(i * i) * SPHERE_RADIUS;
    const homeY = cos(i * i) * SPHERE_RADIUS;
    const home = createVector(homeX, homeY);

    // 2. 스프링 힘 (홈으로 끌어당김)
    const toHome = p5.Vector.sub(home, p.pos);
    const springForce = toHome.mult(ATTRACTION);
    p.vel.add(springForce);

    // 3. 마우스 반발력
    applyMouseRepulsion(p, mousePos);

    // 4. 감속 및 위치 업데이트
    p.vel.mult(DAMPING);
    p.pos.add(p.vel);

    // 5. 파티클 렌더링
    point(p.pos.x, p.pos.y);
  }
}

function applyMouseRepulsion(particle, mousePos) {
  const awayFromMouse = p5.Vector.sub(particle.pos, mousePos);
  const distSq = awayFromMouse.magSq();

  // 마우스가 충분히 가까울 때만 반발력 적용
  if (distSq > 0.1 && distSq < REPEL_RADIUS * REPEL_RADIUS) {
    const distance = sqrt(distSq);
    awayFromMouse.normalize();

    // 거리에 따른 자연스러운 감쇠
    const repelForce = REPEL_STRENGTH * (1 - distance / REPEL_RADIUS);
    awayFromMouse.mult(repelForce);

    particle.vel.add(awayFromMouse);
  }
}

```

***

## 30. script

**파일 경로:** `251226_2d_3d_allinone\script.js`

**설명:** script.js - p5.js 스케치

```javascript
/* -------------------- 2D Artwork -------------------- */
const sketch2D = (p) => {
  p.setup = () => {
    let canvas = p.createCanvas(400, 400);
    canvas.parent("container-2d");
    p.colorMode(p.HSL, 360, 100, 100);
    p.noStroke();
    p.frameRate(60); 
  };

  p.draw = () => {
    p.background(5, 5, 5); 

    const time = p.millis() * 0.0003;
    const w = p.width;
    const h = p.height;

    for (let i = 0; i < 1600; i++) {
      const t = i / 80;
      const x = (t * w) % w;

      const n = p.noise(t + time * 0.3);
      const y = h / 2 + n * p.sin(t + time) * 120;
      const r = 1 + n * 4;

      p.fill(210 + n * 50, 80, 60);
      p.circle(x, y, r * 2);
    }
  };
};

/* -------------------- 3D Artwork (WebGL 2) -------------------- */
const sketch3D = (p) => {
  const GRID = 16; 
  const WAVE_FREQ = 2.5;
  const WAVE_AMP = 0.35;
  const WAVE_SPEED = 1.0;
  const SCALE = 160;

  p.setup = () => {
    let canvas = p.createCanvas(400, 400, p.WEBGL);
    canvas.parent("container-3d");

    p.setAttributes("antialias", true);
  };

  p.draw = () => {
    p.background(5, 5, 5);
    p.orbitControl(2, 2, 1);

    p.stroke(100, 255, 218); 
    p.strokeWeight(1.2);
    p.noFill();

    const time = p.millis() * 0.001 * WAVE_SPEED;

    p.rotateY(p.frameCount * 0.005);
    p.rotateX(p.PI / 6); 

    for (let i = 0; i < GRID - 1; i++) {
      p.beginShape(p.TRIANGLE_STRIP);
      for (let j = 0; j < GRID; j++) {
        drawVertex(i, j, time);
        drawVertex(i + 1, j, time);
      }
      p.endShape();
    }
  };

  function drawVertex(i, j, time) {
    const xNorm = (i / (GRID - 1)) * 2 - 1;
    const zNorm = (j / (GRID - 1)) * 2 - 1;

    const dist = p.sqrt(xNorm * xNorm + zNorm * zNorm);
    const damp = p.constrain(1.5 - dist, 0, 1);

    const y =
      p.sin(xNorm * WAVE_FREQ + time) *
      p.cos(zNorm * WAVE_FREQ + time) *
      WAVE_AMP *
      damp;

    p.vertex(xNorm * SCALE, -y * SCALE, zNorm * SCALE);
  }
};

new p5(sketch2D);
new p5(sketch3D);

```

***

## 31. script

**파일 경로:** `260102_vanishing_picture_particle\script.js`

**설명:** script.js - p5.js 스케치

```javascript
let img;
const detail = 6;
let particles = [];
let grid = [];
let particleImage;
let ctx;

function preload() {
  img = loadImage(
    "./image_01.jpg"
  );
}

class Particle {
  constructor(x, y) {
    this.x = x || random(width);
    this.y = y || random(height);
    this.prevX = this.x;
    this.speed = 0;
    this.v = random(0, 0.7);
  }

  update() {
    if (grid.length) {
      this.speed = grid[floor(this.y / detail)][floor(this.x / detail)] * 0.97;
    }
    this.x += (1 - this.speed) * 3 + this.v;

    if (this.x > width) {
      this.x = 0;
    }
  }

  draw() {
    image(particleImage, this.x, this.y);
  }
}

function setup() {
  const canvas = createCanvas(100, 100);
  ctx = canvas.drawingContext;
  pixelDensity(1);

  // 파티클 이미지 생성
  particleImage = createGraphics(6, 6);
  particleImage.fill(255, 200, 60);
  particleImage.noStroke();
  particleImage.circle(4, 4, 4);

  windowResized();
}

function windowResized() {
  const imgRatio = img.width / img.height;
  if (windowWidth / windowHeight > imgRatio) {
    resizeCanvas(floor(windowHeight * imgRatio), floor(windowHeight));
  } else {
    resizeCanvas(floor(windowWidth), floor(windowWidth / imgRatio));
  }

  initializeEffect();
}

function initializeEffect() {
  clear();
  ctx.globalAlpha = 1;

  // 이미지를 로드하고 픽셀 데이터 추출
  image(img, 0, 0, width, height);
  loadPixels();
  clear();
  noStroke();

  // 밝기 그리드 생성
  grid = [];
  for (let y = 0; y < height; y += detail) {
    let row = [];
    for (let x = 0; x < width; x += detail) {
      const r = pixels[(y * width + x) * 4];
      const g = pixels[(y * width + x) * 4 + 1];
      const b = pixels[(y * width + x) * 4 + 2];
      const _color = color(r, g, b);
      const _brightness = brightness(_color) / 100;
      row.push(_brightness);
    }
    grid.push(row);
  }

  // 파티클 생성 (개수가 변해도 균등 분포))
  const particleCount = 8000;
  particles = [];
  for (let i = 0; i < particleCount; i++) {
    particles.push(new Particle(null, (i / particleCount) * height));
  }
}

// 파티클 알갱이로 보이게 하기
function draw() {
  // 매 프레임마다 완전히 클리어
  clear();

  // 파티클 업데이트 및 그리기
  // ctx.globalAlpha =1;
  particles.forEach((p) => {
    p.update();
    ctx.globalAlpha = p.speed * 0.8;
    p.draw();
  });
}

// 파티클에 trail effect
// function draw() {
//   // 페이드 효과를 위한 반투명 검은색 레이어
//   ctx.globalAlpha = 0.05;
//   fill(0);
//   rect(0, 0, width, height);

//   // 파티클 업데이트 및 그리기
//   ctx.globalAlpha = 0.2;
//   particles.forEach((p) => {
//     p.update();
//     ctx.globalAlpha = p.speed * 0.3;
//     p.draw();
//   });
// }

```

***

## 32. script

**파일 경로:** `260102_variable_rectangles\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// 캔버스 크기 설정 (16:9 가로형)
let canvasW = 1280;
let canvasH = 720;

// 사용할 색상 팔레트
// 대비가 느껴지는 색 위주로 구성
let colors = [
  "#F94144", // 강한 붉은색
  "#F3722C", // 주황
  "#F9C74F", // 노랑
  "#90BE6D", // 연두
  "#577590", // 푸른 회색
];

function setup() {
  // 캔버스 생성
  createCanvas(canvasW, canvasH);

  // 배경을 어두운 톤으로 설정
  background(20);

  // 외곽선 제거
  noStroke();

  // 세로 방향으로 여러 줄을 만들어
  // 각 줄마다 감정의 흐름을 표현
  let rows = 14;

  // 한 줄의 높이 계산
  let rowHeight = height / rows;

  // 각 줄을 반복
  for (let r = 0; r < rows; r++) {
    // 현재 줄의 y 위치
    let y = r * rowHeight + rowHeight / 2;

    // 가로 방향으로 도형을 배치
    for (let x = 0; x < width; x += random(30, 80)) {
      // 도형의 너비와 높이를 랜덤으로 설정
      let w = random(20, 100);
      let h = random(6, rowHeight * 0.6);

      // 색상 배열에서 랜덤 선택
      let c = random(colors);

      // 투명도를 주어 겹침이 자연스럽게 보이도록 설정
      fill(red(c), green(c), blue(c), 180);

      // 약간의 세로 흔들림을 추가하여
      // 기계적인 정렬을 피함
      let offsetY = random(-10, 10);

      // 얇은 사각형을 사용해
      // 감정의 흐름을 선처럼 표현
      rect(x, y + offsetY, w, h);
    }
  }

  // 정적인 이미지이므로 반복 중단
  noLoop();
}

function draw() {
  // 정적 작업이므로 draw에서는 아무 작업도 하지 않음
}

```

***

## 33. script

**파일 경로:** `260111_gravity_comparison\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// p5.js 코드
// 프로젝트 폴더에 CSV 파일들을 업로드해야 합니다.

let tables = {};
let planets = [
  { name: "Sun", file: "Sun_data.csv", color: "#FFD700" }, // 노랑
  { name: "Jupiter", file: "Jupiter_data.csv", color: "#D2691E" }, // 갈색
  { name: "Neptune", file: "Neptune_data.csv", color: "#4169E1" }, // 파랑
  { name: "Earth", file: "Earth_data.csv", color: "#32CD32" }, // 초록
  { name: "Mars", file: "Mars_data.csv", color: "#FF4500" }, // 주황
  { name: "Moon", file: "Moon_data.csv", color: "#C0C0C0" }, // 회색
];

let maxTime = 0;
let maxPos = 0;
let dataPoints = []; // 파싱된 데이터를 저장할 배열
let currentIndex = 0; // 애니메이션 프레임 인덱스

function preload() {
  // 모든 CSV 파일 로드
  for (let p of planets) {
    tables[p.name] = loadTable(p.file, "csv", "header");
  }
}

function setup() {
  createCanvas(1000, 600);
  frameRate(60);

  // 데이터 파싱 및 최대값 찾기 (스케일링용)
  for (let p of planets) {
    let table = tables[p.name];
    let rows = table.getRows();
    let pData = [];

    for (let r of rows) {
      let t = r.getNum("Time (s)");
      let y = r.getNum("Position (m)");

      pData.push({ t: t, y: y });

      if (t > maxTime) maxTime = t;
      if (y > maxPos) maxPos = y;
    }
    dataPoints.push({ meta: p, data: pData });
  }

  // 여백을 위해 최대값 약간 증가
  maxPos *= 1.1;

  textSize(14);
}

function draw() {
  background(30);

  // 축 그리기
  stroke(255);
  line(50, height - 50, width - 50, height - 50); // X축
  line(50, height - 50, 50, 50); // Y축

  // 범례 및 텍스트 표시
  noStroke();
  fill(255);
  text(`Max Height: ${maxPos.toFixed(1)}m`, 60, 40);
  text(`Max Duration: ${maxTime.toFixed(1)}s`, width - 150, height - 30);

  // 각 행성별 궤적 그리기
  for (let i = 0; i < dataPoints.length; i++) {
    let planet = dataPoints[i];
    let path = planet.data;

    stroke(planet.meta.color);
    strokeWeight(2);
    noFill();

    // 현재 프레임까지만 경로 그리기 (애니메이션 효과)
    beginShape();
    let drawLimit = min(currentIndex, path.length - 1);

    for (let j = 0; j <= drawLimit; j++) {
      // 화면 좌표로 매핑
      // x: 시간 (0 ~ maxTime) -> 화면 너비 (50 ~ width-50)
      // y: 높이 (0 ~ maxPos) -> 화면 높이 (height-50 ~ 50)
      let sx = map(path[j].t, 0, maxTime, 50, width - 50);
      let sy = map(path[j].y, 0, maxPos, height - 50, 50);
      vertex(sx, sy);

      // 현재 위치에 원 그리기 (헤드)
      if (j === drawLimit) {
        push();
        fill(planet.meta.color);
        noStroke();
        ellipse(sx, sy, 8, 8);

        // 행성 이름 라벨
        text(planet.meta.name, sx + 10, sy);
        pop();
      }
    }
    endShape();
  }

  // 애니메이션 진행 (속도 조절 가능)
  // 데이터가 많으므로 한 번에 여러 스텝씩 점프하여 속도감 있게 재생
  currentIndex += 5;

  // 루프 종료 조건 (가장 긴 데이터 기준)
  let longestData = 0;
  for (let p of dataPoints) longestData = max(longestData, p.data.length);

  if (currentIndex > longestData + 100) {
    noLoop(); // 애니메이션 끝
  }
}

```

***

## 34. script

**파일 경로:** `260114_slime_mold_patt_vira\script.js`

**설명:** script.js - p5.js 스케치

```javascript
// Patt Vira's Slime Mold Simulation
// https://www.youtube.com/watch?v=VyXxSNcgDtg

let molds = [];             // Mold 객체를 저장할 배열
let num = 8000;             // 생성할 Mold 객체의 개수
let d;                      // pixelDensity() 값을 저장할 변수

function setup() {
  createCanvas(1000, 1000);   // 400x400 픽셀 캔버스 생성
  angleMode(DEGREES);       // 각도를 도(degree)로 설정
  d = pixelDensity();       // 디스플레이의 픽셀 밀도를 저장

  // num 개수만큼 Mold 객체 생성
  for (let i = 0; i < num; i++) {
    molds[i] = new Mold();
  }
}

function draw() {
  background(0, 5);          // 반투명 검은색 배경 (알파값 5)
  loadPixels();              // 픽셀 배열을 메모리에 로드 (pixel[] 배열 사용 가능)

  // 모든 Mold 객체 업데이트 및 표시
  for (let i = 0; i < num; i++) {
    if (key == "s") {
      molds[i].stop = true;  // "s" 키를 누르면 Mold 객체가 멈춤
      updatePixels();        // 픽셀 배열을 화면에 업데이트
      noLoop();              // draw() 루프 중지
    } else {
      molds[i].stop = false; // "s" 키를 떼면 다시 움직임
    }

    molds[i].update();       // Mold 객체 상태 업데이트
    molds[i].display();      // Mold 객체 화면에 표시
  }
}

/*
1. Mold 객체
- 각 Mold는 캔버스 내 랜덤한 위치에서 생성됩니다.
- heading은 이동 방향을 나타내며, vx와 vy는 해당 방향의 x, y 성분입니다.
- stop 변수는 "s" 키를 누르면 true가 되어 이동을 멈춥니다.

2. 센서 시스템
- Mold는 앞쪽, 왼쪽, 오른쪽 센서를 가지고 있으며, 센서는 현재 방향을 기준으로 ±45도 위치에 있습니다.
- 센서는 주변 픽셀의 색상 값을 읽어, 주변 환경에 따라 이동 방향을 결정합니다.

3. 방향 전환 로직
- 앞쪽 센서 값이 가장 크면 직진합니다.
- 앞쪽 센서 값이 가장 작으면 랜덤하게 왼쪽 또는 오른쪽으로 회전합니다.
- 왼쪽 센서 값이 더 크면 오른쪽으로, 오른쪽 센서 값이 더 크면 왼쪽으로 회전합니다.

4. 캔버스 경계 처리
- Mold가 캔버스 경계를 넘어가면 반대편에서 등장하도록 % 연산자를 사용합니다.
*/
```

***

## 35. script

**파일 경로:** `260115_slime_mold_2\script.js`

**설명:** script.js - p5.js 스케치

```javascript
let agents = [];
let trailMap;
let newTrail;

const w = 600;
const h = 600;
const renderScale = 1; // 렌더링 해상도 축소 비율
const diffuseInterval = 10; // 확산 수행 프레임 간격

// 설정
const config = {
  numAgents: 1000,
  sensorAngle: 0.5,
  sensorDistance: 10,
  turnSpeed: 0.5,
  moveSpeed: 2,
  trailWeight: 10,
  decayRate: 0.95,
};

function setup() {
  createCanvas(w, h);
  pixelDensity(1);

  // trail map 초기화
  trailMap = new Float32Array(w * h);
  newTrail = new Float32Array(w * h);

  // 에이전트 초기화 (중앙 원형 배치)
  for (let i = 0; i < config.numAgents; i++) {
    let a = random(TWO_PI);
    let r = 70;
    agents.push({
      x: w / 2 + cos(a) * r,
      y: h / 2 + sin(a) * r,
      angle: random(TWO_PI),
    });
  }

  background(0);
}

function draw() {
  // 에이전트 업데이트
  for (let agent of agents) {
    updateAgent(agent);
  }

  // 확산 (프레임 간격 적용)
  if (frameCount % diffuseInterval === 0) {
    diffuseTrail();
  }

  // 렌더링
  renderTrail();
}

// ──────────────────────────────
// Agent 로직
// ──────────────────────────────

function updateAgent(agent) {
  let forward = sense(agent, 0);
  let left = sense(agent, config.sensorAngle);
  let right = sense(agent, -config.sensorAngle);

  let steer = (random() - 0.5) * 2 * config.turnSpeed;

  if (forward > left && forward > right) {
    // 유지
  } else if (left > right) {
    agent.angle += steer;
  } else if (right > left) {
    agent.angle -= steer;
  } else {
    agent.angle += random() < 0.5 ? steer : -steer;
  }

  agent.x += cos(agent.angle) * config.moveSpeed;
  agent.y += sin(agent.angle) * config.moveSpeed;

  // 경계 반사
  if (agent.x < 0 || agent.x >= w) {
    agent.x = constrain(agent.x, 0, w - 1);
    agent.angle = PI - agent.angle;
  }
  if (agent.y < 0 || agent.y >= h) {
    agent.y = constrain(agent.y, 0, h - 1);
    agent.angle = -agent.angle;
  }

  // trail 기록
  let x = agent.x | 0;
  let y = agent.y | 0;
  trailMap[x + y * w] += config.trailWeight;
}

function sense(agent, offset) {
  let a = agent.angle + offset;
  let x = (agent.x + cos(a) * config.sensorDistance) | 0;
  let y = (agent.y + sin(a) * config.sensorDistance) | 0;

  if (x < 0 || x >= w || y < 0 || y >= h) return 0;
  return trailMap[x + y * w];
}

// ──────────────────────────────
// Diffusion (5-point kernel)
// ──────────────────────────────

function diffuseTrail() {
  for (let y = 1; y < h - 1; y++) {
    let yw = y * w;
    for (let x = 1; x < w - 1; x++) {
      let i = x + yw;
      newTrail[i] =
        (trailMap[i] +
          trailMap[i - 1] +
          trailMap[i + 1] +
          trailMap[i - w] +
          trailMap[i + w]) *
        0.2;
    }
  }

  for (let i = 0; i < trailMap.length; i++) {
    trailMap[i] = newTrail[i] * config.decayRate;
  }
}

// ──────────────────────────────
// Rendering (downsample)
// ──────────────────────────────

function renderTrail() {
  loadPixels();

  for (let y = 0; y < h; y += renderScale) {
    for (let x = 0; x < w; x += renderScale) {
      let i = x + y * w;
      let v = min(255, trailMap[i] * 12);

      for (let dy = 0; dy < renderScale; dy++) {
        for (let dx = 0; dx < renderScale; dx++) {
          let px = x + dx;
          let py = y + dy;
          if (px >= w || py >= h) continue;

          let idx = (px + py * w) * 4;
          pixels[idx + 0] = v;     // R
          pixels[idx + 1] = v;     // G
          pixels[idx + 2] = v;     // B
          pixels[idx + 3] = 255;   // A
        }
      }
    }
  }

  updatePixels();
}

```

***

## 36. script

**파일 경로:** `260406_simple_dot_torus\script.js`

**설명:** script.js - p5.js 스케치

```javascript
let ringRadius = 140;
let minorRadius = 75;

let ringStep = 3;
let tubeStep = 3;

let rotY = 0;
let rotSpeed = 0.05;

function setup() {
  createCanvas(800, 600, WEBGL);
  angleMode(DEGREES);
  colorMode(HSB, 360, 100, 100);
  strokeWeight(1.5);
  noFill();
  smooth();
}

function draw() {
  background(0);
  orbitControl(1.5, 1.5);

  rotY = (rotY + rotSpeed) % 360;
  rotateY(rotY);

  let h = rotY;

  beginShape(POINTS);
  for (let ringAngle = 0; ringAngle < 360; ringAngle += ringStep) {
    for (let tubeAngle = 0; tubeAngle < 360; tubeAngle += tubeStep) {
      let x = (ringRadius + minorRadius * cos(tubeAngle)) * cos(ringAngle);
      let y = minorRadius * sin(tubeAngle);
      let z = (ringRadius + minorRadius * cos(tubeAngle)) * sin(ringAngle);

      stroke(h, 100, 100);
      vertex(x, y, z);
    }
  }
  endShape();
}

```

***

## 37. script\_1

**파일 경로:** `251222_tile_grid_animation\script_1.js`

**설명:** script\_1.js - p5.js 스케치

```javascript
let tiles = [];
let cols = 10;
let rows = 10;

function setup() {
  createCanvas(1000, 1000);
  background(10);
  frameRate(4);

  const tileWidth = width / cols;
  const tileHeight = height / cols;

  for (let y = 0; y < rows; y++) {
    for (let x = 0; x < cols; x++) {
      const pos = {
        x: x * tileWidth,
        y: y * tileHeight,
        w: tileWidth,
        h: tileHeight,
      };
      tiles.push(new Tile(pos));
    }
  }
}

function draw() {
  background(10);

  for (const tile of tiles) {
    tile.display();
  }
}

class Tile {
  constructor(position) {
    this.position = position;
  }

  display() {
    const { x, y, w, h } = this.position; // 구조분해할당

    // let r = int(random(50, 70));
    // let g = int(random(200, 230));
    // let b = int(random(200, 250));
    strokeWeight(1);
    stroke(10);
    fill(150,150,150,255);
    rect(x, y, w, h);
  }
}

```

***

## 38. script\_1

**파일 경로:** `251226_spirograph_cookies\script_1.js`

**설명:** script\_1.js - p5.js 스케치

```javascript
class Spirograph {
  constructor(R, r, d, color, speed) {
    this.R = R;
    this.r = r;
    this.d = d;
    this.color = color;
    this.speed = speed;
    this.theta = 0;
    this.points = [];
    this.maxPoints = 5000;
  }

  update() {
    this.theta += this.speed;

    const k = (this.R - this.r) / this.r;
    const x =
      (this.R - this.r) * cos(this.theta) + this.d * cos(k * this.theta);
    const y =
      (this.R - this.r) * sin(this.theta) - this.d * sin(k * this.theta);

    this.points.push({ x, y });

    if (this.points.length > this.maxPoints) {
      this.points.shift();
    }
  }

  display() {
    const len = this.points.length;

    for (let i = 1; i < len; i++) {
      const ratio = i / len;
      const alpha = ratio * 255;
      const weight = map(ratio, 0, 1, 0.2, 1.5);

      stroke(red(this.color), green(this.color), blue(this.color), alpha);
      strokeWeight(weight);

      line(
        this.points[i - 1].x,
        this.points[i - 1].y,
        this.points[i].x,
        this.points[i].y
      );
    }
  }
}

let spiro;
let rotation = 0;
let scaleOsc = 0;

function setup() {
  createCanvas(800, 800);
  background(210, 200, 190);

  const colors = [color(50, 60, 100), color(40, 50, 80), color(70, 80, 120)];

  spiro = new Spirograph(
    random(180, 280),
    random(40, 90),
    random(50, 120),
    random(colors),
    random(0.008, 0.02)
  );
}

function draw() {
  background(210, 200, 190, 4);

  translate(width / 2, height / 2);

  rotation += 0.006;
  rotate(rotation);

  scaleOsc += 0.012;
  const s = 0.95 + 0.12 * sin(scaleOsc);
  scale(s);

  spiro.update();
  spiro.display();
}

```

***

## 39. script\_2

**파일 경로:** `251222_tile_grid_animation\script_2.js`

**설명:** script\_2.js - p5.js 스케치

```javascript
let tiles = [];
let cols = 25;
let rows = 25;
let t = 0; // 시간 변수

function setup() {
  createCanvas(1000, 1000);
  frameRate(60);

  const tileWidth = width / cols;
  const tileHeight = height / rows;

  for (let y = 0; y < rows; y++) {
    for (let x = 0; x < cols; x++) {
      const pos = {
        x: x * tileWidth,
        y: y * tileHeight,
        w: tileWidth,
        h: tileHeight,
      };
      tiles.push(new Tile(pos));
    }
  }
}

function draw() {
  background(10);

  for (const tile of tiles) {
    tile.update(t);
    tile.display();
  }

  t += 0.01; // 주기 속도
}

function keyPressed() {
  if (key === " ") {
    for (const tile of tiles) {
      tile.randomizeColor();
    }
  }
}

class Tile {
  constructor(position) {
    this.position = position;

    // 타일별 랜덤 위상 오프셋
    this.phase = random(TWO_PI);

    // 초기 색상 고정
    this.randomizeColor();
  }

  randomizeColor() {
    this.color = {
      r: int(random(20, 100)),
      g: int(random(100, 200)),
      b: int(random(170, 220)),
    };
  }

  /* 오른쪽에서 왼쪽으로 파장 전달 */
  // 최소 크기가 필요하면 숫자를 더하고, 작은 수를 곱함
  // 큰 숫자로 겹치면 더 재미있는 패턴 생성됨
  // update(time) {
  //   const { x, w } = this.position;
  //   const nx = x / width; // 0~1 정규화

  //   this.scale = 0.4 + (sin(time + nx * TWO_PI) + 1) * 0.3;
  // }

  /* 중앙에서 바깥으로 파장 전달 */
  // time 뒤 부호를 바꾸면 방향 반전
  // nd 끝의 숫자를 키우면 파장 간격이 커짐

  // update(time) {
  //   const { x, y, w, h } = this.position;

  //   const cx = x + w * 0.5;
  //   const cy = y + h * 0.5;

  //   const d = dist(cx, cy, width * 0.5, height * 0.5);
  //   const nd = d / (sqrt(width * width + height * height) * 0.7);

  //   this.scale = (sin(time - nd * TWO_PI) + 0.5) * 0.65;
  // }

  /* 랜덤한 파동 */
  update(time) {
    // 사인 곡선 기반 크기 비율 (0~1)
    // 곱하는 숫자를 키우면 사이즈 증가
    this.scale = (sin(time + this.phase) + 1) * 0.5;
    // 최소 크기 보장
    //  this.scale = 0.42 + 0.7 * (sin(time + this.phase) + 1) * 0.4;
  }


  display() {
    const { x, y, w, h } = this.position;
    const { r, g, b } = this.color;

    // 중앙 기준 크기 변화
    const sw = w * this.scale;
    const sh = h * this.scale;

    const cx = x + w * 0.5;
    const cy = y + h * 0.5;

    stroke(10);
    strokeWeight(1);
    fill(r, g, b, 255);
    rectMode(CENTER);
    rect(cx, cy, sw, sh);
    rectMode(CORNER);
  }
}

```

***

## 40. script\_2

**파일 경로:** `251223_arc_animation\script_2.js`

**설명:** script\_2.js - p5.js 스케치

```javascript
const num = 50;
let step, theta;

function setup() {
  createCanvas(600, 600);
  strokeWeight(5);
  step = 12;
  theta = 0;
}

function draw() {
  background(20, 50);
  translate(width / 2, height);

  for (let i = 0; i < num; i++) {
    stroke(i * 4, 60, 130);
    noFill();
    const size = i * step;
    const offSet = (TWO_PI / num) * i;
    // sin 값을 0.001~1 범위로 매핑하여 arcEnd가 항상 PI보다 크도록 설정
    const arcEnd = map(sin(theta + offSet), -1, 1, PI + 0.001, TWO_PI * 1.0);
    arc(0, 0, size, size, PI, arcEnd);
  }
  resetMatrix();
  theta += 0.004;
}

```

***

## 41. script\_2

**파일 경로:** `251226_spirograph_cookies\script_2.js`

**설명:** script\_2.js - p5.js 스케치

```javascript
class Spirograph {
  constructor(R, r, d, hue, saturation, brightness, speed, offset) {
    this.R = R;
    this.r = r;
    this.d = d;
    this.hue = hue;
    this.saturation = saturation;
    this.brightness = brightness;
    this.speed = speed;
    this.theta = offset;
    this.points = [];
    this.maxPoints = 3000;
  }

  update() {
    this.theta += this.speed;

    const k = (this.R - this.r) / this.r;
    const x =
      (this.R - this.r) * cos(this.theta) + this.d * cos(k * this.theta);
    const y =
      (this.R - this.r) * sin(this.theta) - this.d * sin(k * this.theta);

    this.points.push({ x, y });

    if (this.points.length > this.maxPoints) {
      this.points.shift();
    }
  }

  display() {
    const len = this.points.length;

    noFill();
    beginShape();
    for (let i = 0; i < len; i++) {
      const ratio = i / len;
      const alpha = map(ratio, 0, 1, 0, 80);
      const weight = map(ratio, 0, 1, 0.3, 0.8);

      stroke(this.hue, this.saturation, this.brightness, alpha);
      strokeWeight(weight);

      vertex(this.points[i].x, this.points[i].y);
    }
    endShape();
  }
}

let spiros = [];
let rotation = 0;
let scaleOsc = 0;

function setup() {
  createCanvas(800, 800);
  colorMode(HSB, 360, 100, 100, 100);
  background(20, 15, 95);

  // 여러 개의 스파이로그래프를 다른 파라미터로 생성
  const numSpiros = 7;

  for (let i = 0; i < numSpiros; i++) {
    const angle = (TWO_PI * i) / numSpiros;

    spiros.push(
      new Spirograph(
        random(120, 200),
        random(30, 70),
        random(40, 100),
        random(0, 20), // 주황-빨강 계열
        random(70, 90), // 채도
        random(85, 95), // 밝기
        random(0.01, 0.025),
        angle
      )
    );
  }
}

function draw() {
  background(20, 15, 95, 3);

  translate(width / 2, height / 2);

  rotation += 0.002;
  rotate(rotation);

  scaleOsc += 0.008;
  const s = 0.9 + 0.15 * sin(scaleOsc);
  scale(s);

  // 모든 스파이로그래프 업데이트 및 표시
  for (let spiro of spiros) {
    spiro.update();
    spiro.display();
  }
}

```

***

## 42. script\_3

**파일 경로:** `251226_spirograph_cookies\script_3.js`

**설명:** script\_3.js - p5.js 스케치

```javascript
class RotatingCurve {
  constructor(angle, baseRadius, curveSize, hue, sat, bri, speed, frequency) {
    this.angle = angle;
    this.baseRadius = baseRadius;
    this.curveSize = curveSize;
    this.hue = hue;
    this.sat = sat;
    this.bri = bri;
    this.speed = speed;
    this.frequency = frequency;
    this.phase = 0;
    this.points = [];
    this.maxPoints = 2000;
  }

  update() {
    this.phase += this.speed;

    // 원점에서 일정 거리에 위치한 곡선의 중심점
    const centerX = this.baseRadius * cos(this.angle);
    const centerY = this.baseRadius * sin(this.angle);

    // 곡선을 따라 움직이는 점 계산
    const t = this.phase;
    const r = this.curveSize * (1 + 0.5 * sin(this.frequency * t));
    const spiralAngle = t * 3;

    const x = centerX + r * cos(spiralAngle);
    const y = centerY + r * sin(spiralAngle);

    this.points.push({ x, y });

    if (this.points.length > this.maxPoints) {
      this.points.shift();
    }
  }

  display() {
    const len = this.points.length;

    for (let i = 1; i < len; i++) {
      const ratio = i / len;
      const alpha = map(ratio, 0, 1, 0, 60);
      const weight = map(ratio, 0, 1, 0.2, 1.0);

      stroke(this.hue, this.sat, this.bri, alpha);
      strokeWeight(weight);

      line(
        this.points[i - 1].x,
        this.points[i - 1].y,
        this.points[i].x,
        this.points[i].y
      );
    }
  }
}

let curves = [];
let globalRotation = 0;
let scaleOsc = 0;

function setup() {
  createCanvas(800, 800);
  colorMode(HSB, 360, 100, 100, 100);
  background(20, 15, 95);

  const numCurves = 7;

  for (let i = 0; i < numCurves; i++) {
    const angle = (TWO_PI * i) / numCurves;

    curves.push(
      new RotatingCurve(
        angle,
        random(150, 160), // 원점으로부터의 거리
        random(30, 40), // 곡선 크기
        random(0, 20), // 주황-빨강 색상
        random(70, 90),
        random(85, 95),
        random(0.03, 0.04), // 회전 속도
        random(7, 9) // 진동 주파수
      )
    );
  }
}

function draw() {
  background(20, 15, 95, 50);

  translate(width / 2, height / 2);

  globalRotation += 0.0005;
  rotate(globalRotation);

  scaleOsc += 0.01;
  const s = 1.5 + 0.1 * sin(scaleOsc);
  scale(s);

  for (let curve of curves) {
    curve.update();
    curve.display();
  }
}

```

***

## 43. script\_blue\_noise

**파일 경로:** `251214_exploding_blackhole\script_blue_noise.js`

**설명:** script\_blue\_noise.js - p5.js 스케치

```javascript
// script_blue_noise.js

let points = [];
const POINT_COUNT = 4000;

let baseColor;
let whiteColor;
let state = "normal";
let resetFrame = 0;

function setup() {
  createCanvas(windowWidth, windowHeight);
  colorMode(RGB, 255);
  background(20);
  strokeWeight(2);

  baseColor = color(80, 140, 255); // 파랑
  whiteColor = color(255);

  let maxR = min(width, height) * 0.5;

  for (let i = 0; i < POINT_COUNT; i++) {
    let r = random(100, maxR);
    let angle = random(TWO_PI);

    points.push({
      r: r,
      r0: r,
      angle: angle,
      angularSpeed: random(0.005, 0.008),
      drift: 0,
      noiseOffset: random(1000),
      burstSpeed: 0,
    });
  }
}

function draw() {
  translate(width / 2, height / 2);
  background(20, 20);

  for (let p of points) {
    if (state === "explode") {
      // 중심에서 방사형 폭발
      p.burstSpeed *= 0.9;
      p.r += p.burstSpeed;

      if (frameCount - resetFrame > 40) {
        state = "normal";
      }
    } else {
      // 정상 붕괴 상태
      p.angle += p.angularSpeed;
      p.drift += 0.002;
    }

    // 노이즈 흔들림 유지
    let n = noise(p.noiseOffset + frameCount * 0.01);
    let distortion = map(n, 0, 1, -1, 1) * p.drift * 220;

    let x = cos(p.angle) * (p.r + distortion);
    let y = sin(p.angle) * (p.r + distortion);

    // 노이즈 강도 → 색상
    let noiseEnergy = constrain(p.drift * 0.6, 0, 1);
    let c = lerpColor(baseColor, whiteColor, noiseEnergy);

    stroke(c);
    point(x, y);
  }
}

function keyPressed() {
  if (key === " ") {
    state = "explode";
    resetFrame = frameCount;

    for (let p of points) {
      // ❗ 누적 폭발
      p.burstSpeed += random(8, 18);

      // 노이즈는 리셋
      p.drift = 0;
    }
  }
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}

```

***

## 44. script\_class

**파일 경로:** `251215_geometric_tiles\script_class.js`

**설명:** script\_class.js - p5.js 스케치

```javascript
// ======================================================
// 1. 전역 설정값 (Configuration Constants)
// ======================================================

const CANVAS_SIZE = 800;        // 캔버스의 가로·세로 크기
const TILE_SIZE = 50;           // 하나의 타일이 차지하는 정사각형 크기
const GRID_NOISE_SCALE = 0.01;  // 공간 노이즈의 세밀함
const TIME_NOISE_SCALE = 0.01;  // 시간에 따른 노이즈 변화 속도
const TIME_STEP = 0.2;          // 매 프레임마다 time 변수에 더해지는 값 (속도 조절)

// ======================================================
// 2. 전역 상태 변수 (Global State)
// ======================================================
// p5.js의 draw 루프 전체에서 공유되는 상태값

let tiles = []; // 모든 타일 객체(Tile 인스턴스)를 담는 배열
let time = 0;   // 애니메이션의 시간 축 역할을 하는 변수. 점점 증가하며 변화의 기준이 됨

// ======================================================
// 3. p5.js 라이프사이클 함수
// ======================================================

// p5.js 초기 설정 함수 (프로그램 시작 시 한 번만 실행)
function setup() {
  // 캔버스 생성
  createCanvas(CANVAS_SIZE, CANVAS_SIZE);

  // 색상 모드를 HSB로 설정
  // hue: 0~360, saturation/brightness/alpha: 0~100
  colorMode(HSB, 360, 100, 100, 100);

  // 각도 단위를 라디안이 아닌 도(degree)로 설정
  angleMode(DEGREES);

  // 화면 전체를 TILE_SIZE 간격으로 나누어 타일 객체들을 생성하고 배열에 저장
  tiles = createTileGrid();
}

// p5.js 무한 반복 함수 (매 프레임마다 실행됨)
function draw() {
  // 설정한 배경 색와 알파값을 매 프레임마다 덮어씌움
  background(5, 5, 5, 100);

  // 시간 값 증가. 이값은 노이즈, 색상 변화, 회전에 사용됨.
  time += TIME_STEP;

  // 모든 타일에 대해
  // 1) 현재 시간 값을 전달하며 타일의 노이즈와 회전 상태 갱신
  // 2) 갱신된 상태를 바탕으로 타일을 화면에 그림
  for (const tile of tiles) {
    tile.update(time);
    tile.render();
  }
}

// ======================================================
// 4. 타일 그리드 생성 함수
// ======================================================

// 화면을 TILE_SIZE 단위의 격자로 나누어 각 위치에 Tile 객체를 생성하는 함수
function createTileGrid() {
  // 생성된 타일들을 저장할 배열
  const result = [];

  // 가로 세로 방향으로 각각 TILE_SIZE 간격만큼 이동하며 반복
  for (let x = 0; x < width; x += TILE_SIZE) {
    for (let y = 0; y < height; y += TILE_SIZE) {
      // 현재 격자 위치(x, y)에 새로운 Tile 객체를 생성하여 배열에 추가
      result.push(new Tile(x, y, TILE_SIZE));
    }
  }

  // 완성된 타일 배열 반환
  return result;
}

// ======================================================
// 5. Tile 클래스 정의
// ======================================================
// Tile 클래스는 "타일 하나"를 표현하는 단위 객체
// 위치, 색, 회전, 형태, 애니메이션 로직을 정의함

class Tile {
  // --------------------------------------------------
  // 5-1. 생성자 (constructor)
  // --------------------------------------------------
  // new Tile(x, y, size) 형태로 호출됨
  // 타일이 처음 만들어질 때 한 번만 실행됨

  constructor(x, y, size) {
    // 타일의 좌상단 위치
    // p5.Vector를 사용하면 이후 벡터 연산 확장이 용이함
    this.position = createVector(x, y);

    // 타일의 기본 크기 저장
    this.size = size;

    // 초기 회전 각도를 랜덤하게 설정 (0~270도 사이)
    this.rotation = random(270);

    // 회전 속도를 랜덤하게 설정 (-4 ~ +4도/프레임)
    this.rotationSpeed = random(-4, 4);

    // 타일의 기본 색조(hue)를 랜덤하게 설정 (0~30으로 유사색 분포)
    this.hueBase = random(30);

    // 어떤 형태를 그릴지 결정하는 인덱스
    // 0: 사각형, 1: 원, 2: 삼각형, 3: 십자
    this.shapeType = floor(random(4));

    // 각 타일마다 다른 노이즈 패턴을 만들기 위한 랜덤 오프셋
    this.noiseOffset = random(1000);

    // 현재 프레임에서 계산된 노이즈 값 저장용 변수 (초기값 0)
    this.noiseValue = 0;
  }

  // --------------------------------------------------
  // 5-2. update 메서드
  // --------------------------------------------------
  // 시간에 따른 타일의 "상태 변화"를 담당
  // 위치는 고정되어 있고, 회전과 노이즈 값만 변함

  update(t) {
    // 타일 위치의 x, y 좌표를 구조 분해 할당으로 추출
    const { x, y } = this.position;

    // 3차원 Perlin 노이즈 값을 계산 (공간 x, 공간 y, 시간 t + 오프셋)
    this.noiseValue = noise(
      x * GRID_NOISE_SCALE,
      y * GRID_NOISE_SCALE,
      t * TIME_NOISE_SCALE + this.noiseOffset
    );

    // 노이즈 값에 비례하여 회전 속도 적용 (노이즈가 클수록 더 빠르게 회전)
    this.rotation += this.rotationSpeed * this.noiseValue;
  }

  // --------------------------------------------------
  // 5-3. render 메서드
  // --------------------------------------------------
  // 타일을 실제로 화면에 그리는 역할을 한다.
  // 계산된 상태값을 시각적 요소로 변환한다.

  render() {
    // 타일 위치의 x, y 좌표 추출
    const { x, y } = this.position;

    // 타일의 중심 좌표 계산
    const centerX = x + this.size * 0.5;
    const centerY = y + this.size * 0.5;

    // 현재 계산된 노이즈 값을 n으로 사용
    const n = this.noiseValue;

    // 색조(hue): 기본 hue + 시간에 따른 변화 + 노이즈 영향
    const hue = (this.hueBase + time * 0.5 + n * 100) % 360;

    // 채도(saturation): 시간과 x 위치에 따른 사인파로 부드럽게 진동
    const saturation = 40 + sin(time * 6 + x * 0.01) * 20;

    // 명도(brightness): 시간과 y 위치에 따른 코사인파로 부드럽게 진동
    const brightness = 50 + cos(time * 3 + y * 0.1) * 50;

    // 도형의 실제 그려질 크기: 노이즈에 따라 0.8~1.2배 사이에서 변동
    const drawSize = this.size * 0.5 * (0.8 + n * 0.4);

    push(); // 현재 좌표계와 스타일 저장

    // 타일 중심으로 좌표계를 이동
    translate(centerX, centerY);

    // 계산된 회전 각도 적용 (기본 회전 + 노이즈에 따른 추가 회전)
    rotate(this.rotation + n * 20);

    // 테두리 없음
    noStroke();

    // 계산된 HSB 색상과 불투명도 100으로 채우기
    fill(hue, saturation, brightness, 100);

    // shapeType에 따라 도형 그리기
    this.drawShape(drawSize);

    // 도형 중앙에 작은 흰 점 추가
    this.drawInnerDot(drawSize);

    pop(); // 좌표계와 스타일 복원
  }

  // --------------------------------------------------
  // 5-4. 도형 선택 메서드
  // --------------------------------------------------
  // shapeType 값을 기반으로
  // 미리 정의된 함수 배열에서 도형을 호출

  drawShape(size) {
    SHAPE_RENDERERS[this.shapeType](size);
  }

  // --------------------------------------------------
  // 5-5. 내부 점 장식
  // --------------------------------------------------
  // 타일 중앙에 작은 흰색 원 그림

  drawInnerDot(size) {
    fill(0, 0, 100, 100); // HSB Mode로 채우기
    ellipse(10, 0, size * 0.2); // 중심에서 약간 오른쪽(10,0)
  }

  // --------------------------------------------------
  // 5-6. 외형 랜덤화 메서드
  // --------------------------------------------------
  // 외부에서 호출 시 타일의 회전 속도, 색상, 도형을 다시 랜덤화

  randomizeAppearance() {
    this.rotationSpeed = random(-4, 4);
    this.hueBase = random(30);
    this.shapeType = floor(random(4));
  }
}

// ======================================================
// 6. 도형 렌더링 함수들
// ======================================================
// 각 함수는 "현재 좌표계 기준"으로 도형을 그림
// Tile 클래스에서는 좌표 변환만 담당하고
// 실제 도형 정의는 이 영역에 위임함

// 각 도형을 그리는 함수들을 배열로 묶음 (0,1,2,3 인덱스로 선택)
const SHAPE_RENDERERS = [drawSquare, drawCircle, drawTriangle, drawCross];

// 사각형 그리기 (중심 기준)
function drawSquare(size) {
  rect(-size / 2, -size / 2, size, size);
}

// 원 그리기 (중심 기준)
function drawCircle(size) {
  ellipse(0, 0, size, size);
}

// 정삼각형 그리기 (중심 기준, 위쪽이 뾰족)
function drawTriangle(size) {
  beginShape();
  for (let i = 0; i < 3; i++) {
    const angle = 120 * i - 90; // 120도 간격, -90도로 회전하여 위쪽 정점
    vertex(
      cos(angle) * size * 0.5, 
      sin(angle) * size * 0.5
    );
  }
  endShape(CLOSE);
}

// 십자 모양 그리기 (가로 막대 + 세로 막대)
function drawCross(size) {
  rect(-size / 2, -size / 6, size, size / 3);  // 가로 막대
  rect(-size / 6, -size / 2, size / 3, size);  // 세로 막대
}

// ======================================================
// 7. 입력 처리
// ======================================================
// 스페이스바를 누르면 모든 타일의 외형이 새로 랜덤화

function keyPressed() {
  if (key === " ") {
    for (const tile of tiles) {
      tile.randomizeAppearance();
    }
  }
}

```

***

## 45. script\_comment

**파일 경로:** `251222_pointing_triangle\script_comment.js`

**설명:** script\_comment.js - p5.js 스케치

```javascript
// ======================================================
// 1. 전역 설정값 (Configuration Constants)
// ======================================================

// 삼각형 그리드 기본 설정
const TRI_SIZE = 20; // 삼각형 크기
const SPACING = 30; // 삼각형 간격
const BRIGHT_RANGE = 500; // 밝아지는 효과 범위 (픽셀)
const ROTATION_EASING = 0.1; // 회전 easing 값 (0~1, 작을수록 부드러움)

// HSB 색상 설정
const HUE_MIN = 180; // Hue 최소값 (0~360, 청록색)
const HUE_MAX = 240; // Hue 최대값 (0~360, 파란색)
const BRIGHTNESS_NEAR = 80; // 가까운 곳의 밝기 (0~100)
const BRIGHTNESS_FAR = 20; // 먼 곳의 밝기 (0~100)

// ======================================================
// 2. 전역 상태 변수 (Global State)
// ======================================================

let grid; // 삼각형 그리드 객체 (TriangleGrid 인스턴스)

// ======================================================
// 3. p5.js 라이프사이클 함수
// ======================================================

// p5.js 초기 설정 함수 (프로그램 시작 시 한 번만 실행)
function setup() {
  // 캔버스 생성 (전체 윈도우 크기)
  createCanvas(windowWidth, windowHeight);

  // 색상 모드를 HSB로 설정
  // hue: 0~360, saturation/brightness: 0~100
  colorMode(HSB, 360, 100, 100);

  // 삼각형 그리드 초기화
  grid = new TriangleGrid();
}

// p5.js 메인 루프 함수 (매 프레임마다 실행)
function draw() {
  // 배경을 검은색으로 초기화
  background(0);

  // 모든 삼각형의 상태 업데이트 (각도 계산)
  grid.update();

  // 모든 삼각형 화면에 그리기
  grid.display();
}

// 윈도우 크기 변경 시 호출되는 함수
function windowResized() {
  // 캔버스 크기 재조정
  resizeCanvas(windowWidth, windowHeight);

  // 그리드 재생성
  grid = new TriangleGrid();
}

// ======================================================
// 4. 클래스 정의 (Class Definitions)
// ======================================================

// 삼각형 그리드를 관리하는 클래스
class TriangleGrid {
  constructor() {
    // 화면에 필요한 열(column)과 행(row) 개수 계산
    this.cols = ceil(width / SPACING);
    this.rows = ceil(height / SPACING);

    // 모든 삼각형 객체를 담을 배열
    this.triangles = [];

    // 삼각형 객체들 생성 및 초기화
    this.initializeTriangles();
  }

  // 그리드 상의 모든 위치에 삼각형 객체 생성
  initializeTriangles() {
    for (let i = 0; i < this.cols; i++) {
      for (let j = 0; j < this.rows; j++) {
        // 각 삼각형의 화면 상 좌표 계산
        let x = SPACING / 2 + i * SPACING;
        let y = SPACING / 2 + j * SPACING;

        // Triangle 인스턴스 생성 후 배열에 추가
        this.triangles.push(new Triangle(x, y));
      }
    }
  }

  // 모든 삼각형의 상태 업데이트 (각도 계산)
  update() {
    for (let tri of this.triangles) {
      tri.update();
    }
  }

  // 모든 삼각형을 화면에 그리기
  display() {
    for (let tri of this.triangles) {
      tri.display();
    }
  }
}

// 개별 삼각형을 나타내는 클래스
class Triangle {
  constructor(x, y) {
    this.x = x; // 삼각형의 X 좌표
    this.y = y; // 삼각형의 Y 좌표
    this.currentAngle = 0; // 현재 회전 각도 (라디안)
  }

  // 삼각형의 회전 각도를 마우스 방향으로 업데이트
  update() {
    // 마우스를 향하는 목표 각도 계산
    let targetAngle = atan2(mouseY - this.y, mouseX - this.x);

    // 현재 각도와 목표 각도의 차이 계산
    let diff = targetAngle - this.currentAngle;

    // 각도 차이를 -PI ~ PI 범위로 정규화
    // (최단 경로로 회전하도록 보정)
    if (diff > PI) diff -= TWO_PI;
    if (diff < -PI) diff += TWO_PI;

    // easing을 적용하여 부드럽게 회전
    this.currentAngle += diff * ROTATION_EASING;
  }

  // 삼각형을 화면에 그리기
  display() {
    // 마우스와 삼각형 사이의 거리 계산
    let distance = dist(mouseX, mouseY, this.x, this.y);

    // 거리에 따른 Hue 값 계산
    // 가까우면 HUE_MIN, 멀면 HUE_MAX
    let hue = map(distance, 0, BRIGHT_RANGE, HUE_MIN, HUE_MAX, true);

    // 거리에 따른 밝기 계산 (ease-out cubic 적용)
    let brightness;
    if (distance < BRIGHT_RANGE) {
      // 0~1 사이로 정규화
      let t = distance / BRIGHT_RANGE;

      // ease-out cubic 함수: 1 - (1-t)³
      // 가까울 때는 천천히 변화, 멀 때는 급격히 변화
      let easedT = 1 - pow(1 - t, 3);

      // 정규화된 값을 실제 밝기 값으로 변환
      brightness = lerp(BRIGHTNESS_NEAR, BRIGHTNESS_FAR, easedT);
    } else {
      // 범위를 벗어난 경우 먼 곳의 밝기 적용
      brightness = BRIGHTNESS_FAR;
    }

    // 외곽선 없이 색상만 채우기
    noStroke();
    fill(hue, 100, brightness);

    // 삼각형 그리기
    push();

    translate(this.x, this.y); // 삼각형 위치로 이동
    rotate(this.currentAngle); // 계산된 각도로 회전
    beginShape();
    vertex(TRI_SIZE / 2, 0); // 오른쪽 끝 (앞쪽)
    vertex(-TRI_SIZE / 2, -TRI_SIZE / 4); // 왼쪽 아래
    vertex(-TRI_SIZE / 2, TRI_SIZE / 4); // 왼쪽 위
    endShape(CLOSE);

    pop();
  }
}

```

***

## 46. script\_COMMENT

**파일 경로:** `251223_fake_sphere_particle\script_COMMENT.js`

**설명:** script\_COMMENT.js - p5.js 스케치

```javascript
// ==========================================
// [설정 영역] 전역 상수 및 변수
// ==========================================

const PARTICLE_COUNT = 8000; // 파티클 개수 (많을수록 밀도 높은 구체)
const ATTRACTION = 0.01;     // 원래 위치로 끌어당기는 힘 (0~1, 클수록 빠르게 복귀)
const DAMPING = 0.9;         // 마찰력/감속 계수 (0~1, 1에 가까울수록 부드럽게 미끄러짐)
const REPEL_STRENGTH = 28;   // 마우스 반발력 강도 (픽셀 단위 가속도)

const CANVAS_WIDTH = 1200;   // 캔버스 너비
const CANVAS_HEIGHT = 900;   // 캔버스 높이
const SPHERE_RADIUS = 350;   // 가상 구체의 반지름 (실제로는 2D 타원)
const REPEL_RADIUS = 120;    // 마우스 주변 이 거리 안에 있는 파티클만 반발

let angle = 0;               // 시간에 따라 증가하는 회전 각도 (라디안)
let points = [];             // 파티클 객체 배열 {index, pos, vel}

// ==========================================
// [p5.js 라이프사이클]
// ==========================================

function setup() {
  // 캔버스 생성
  createCanvas(CANVAS_WIDTH, CANVAS_HEIGHT);

  // pixelDensity(1): 고해상도 디스플레이에서도 1:1 픽셀 매핑 (성능 최적화)
  pixelDensity(1);

  // 파티클 렌더링 스타일 설정
  stroke(255);      // 흰색 점
  strokeWeight(2);  // 점 크기 2px

  // 파티클 배열 초기화 (빈 객체들 생성)
  initializeParticles();

  // 초기 회전 각도를 0으로 설정
  angle = 0;

  // 각 파티클의 초기 "홈" 위치를 구체 표면에 배치
  updateParticleTargets();

  // 모든 파티클의 속도를 0으로 초기화 (정지 상태에서 시작)
  for (let p of points) {
    p.vel.set(0, 0);
  }
}

function draw() {
  // 매 프레임마다 검은 배경으로 화면 지우기
  background(0);

  // 좌표계 원점을 캔버스 중앙으로 이동
  // 이제 (0, 0)이 화면 정중앙이 됨
  translate(width / 2, height / 2);

  // 마우스 위치를 캔버스 중심 기준 좌표로 변환
  // 예: 마우스가 캔버스 왼쪽 상단에 있으면 (-600, -450)
  const mousePos = createVector(mouseX - width / 2, mouseY - height / 2);

  // 모든 파티클에 대해 물리 계산 및 렌더링 수행
  updateAndRenderParticles(mousePos);

  // 시간 경과에 따라 각도 증가 (초당 약 0.6 라디안 = 34도)
  // 이 각도가 증가하면서 구체가 회전하는 효과 발생
  angle += 0.01;
}

// ==========================================
// [초기화 함수]
// ==========================================

function initializeParticles() {
  // 파티클 배열 초기화
  points = [];

  // PARTICLE_COUNT개의 파티클 생성
  for (let i = 0; i < PARTICLE_COUNT; i++) {
    points.push({
      index: i,                // 파티클 고유 번호 (0 ~ 7999)
      pos: createVector(0, 0), // 현재 위치 (x, y)
      vel: createVector(0, 0), // 현재 속도 (vx, vy)
    });
  }
}

// 파티클의 초기 "홈" 위치를 설정하는 함수
function updateParticleTargets() {
  for (let p of points) {
    const i = p.index;

    // 2D 평면에서 3D 구체를 흉내내는 수식
    // sin(i + angle): i에 따라 X축 방향 회전
    // sin(i * i): i의 제곱에 sin을 적용하여 불규칙한 분포 생성
    // 두 sin을 곱하면 구체 표면처럼 보이는 패턴 생성
    const x = sin(i + angle) * sin(i * i) * SPHERE_RADIUS;

    // cos(i * i): Y축 위치 (위아래 분포)
    // i * i를 사용하여 파티클마다 다른 높이 부여
    const y = cos(i * i) * SPHERE_RADIUS;

    // 계산된 위치를 파티클의 pos에 설정
    p.pos.set(x, y);
  }
}

// ==========================================
// [파티클 물리 시뮬레이션]
// ==========================================

function updateAndRenderParticles(mousePos) {
  // 모든 파티클에 대해 반복
  for (let p of points) {
    const i = p.index;

    // --------------------------------------------------
    // 1. 회전하는 "홈" 위치 계산
    // --------------------------------------------------
    // angle이 계속 증가하므로 홈 위치가 시간에 따라 회전함
    // sin(i + angle): angle이 변하면서 X 좌표가 좌우로 이동
    const homeX = sin(i + angle) * sin(i * i) * SPHERE_RADIUS;

    // Y 좌표는 angle에 독립적이므로 상하 왕복만 함
    const homeY = cos(i * i) * SPHERE_RADIUS;

    // 홈 위치를 벡터로 생성
    const home = createVector(homeX, homeY);

    // --------------------------------------------------
    // 2. 스프링 힘 (홈으로 끌어당김)
    // --------------------------------------------------
    // 현재 위치에서 홈 위치로 가는 벡터 계산
    // 예: 파티클이 홈에서 오른쪽으로 10px 떨어져 있으면 toHome = (-10, 0)
    const toHome = p5.Vector.sub(home, p.pos);

    // 스프링 힘 = 거리 × ATTRACTION
    // 멀리 떨어질수록 강한 힘이 작용 (후크의 법칙)
    const springForce = toHome.mult(ATTRACTION);

    // 속도에 스프링 힘 추가 (가속도 적용)
    p.vel.add(springForce);

    // --------------------------------------------------
    // 3. 마우스 반발력
    // --------------------------------------------------
    applyMouseRepulsion(p, mousePos);

    // --------------------------------------------------
    // 4. 감속 및 위치 업데이트
    // --------------------------------------------------
    // 속도에 DAMPING(0.9)를 곱해 매 프레임마다 10%씩 감속
    // 이것이 없으면 파티클이 끝없이 가속됨
    p.vel.mult(DAMPING);

    // 뉴턴의 운동 법칙: 위치 += 속도
    p.pos.add(p.vel);

    // --------------------------------------------------
    // 5. 파티클 렌더링
    // --------------------------------------------------
    // 현재 위치에 점 하나 그리기
    point(p.pos.x, p.pos.y);
  }
}

function applyMouseRepulsion(particle, mousePos) {
  // 파티클에서 마우스로부터 멀어지는 방향 벡터 계산
  // 예: 파티클(100, 100), 마우스(90, 100) → awayFromMouse = (10, 0)
  const awayFromMouse = p5.Vector.sub(particle.pos, mousePos);

  // 거리의 제곱 계산 (sqrt 연산 생략으로 성능 최적화)
  // 예: (10, 0)의 magSq = 10² + 0² = 100
  const distSq = awayFromMouse.magSq();

  // --------------------------------------------------
  // 반발력 적용 조건 체크
  // --------------------------------------------------
  // 조건 1: distSq > 0.1 → 파티클과 마우스가 너무 가깝지 않음 (0으로 나누기 방지)
  // 조건 2: distSq < REPEL_RADIUS² → 파티클이 마우스 영향권 안에 있음
  if (distSq > 0.1 && distSq < REPEL_RADIUS * REPEL_RADIUS) {
    // 실제 거리 계산 (피타고라스 정리)
    const distance = sqrt(distSq);

    // 벡터 정규화: 크기를 1로 만들어 방향만 남김
    // 예: (10, 0) → (1, 0)
    awayFromMouse.normalize();

    // --------------------------------------------------
    // 거리에 따른 자연스러운 감쇠 계산
    // --------------------------------------------------
    // (1 - distance / REPEL_RADIUS): 가까울수록 1에 가까움, 멀수록 0에 가까움
    // 예: distance=60, REPEL_RADIUS=120 → (1 - 60/120) = 0.5
    // 최종 힘 = 28 × 0.5 = 14
    const repelForce = REPEL_STRENGTH * (1 - distance / REPEL_RADIUS);

    // 정규화된 방향 벡터에 힘의 크기를 곱함
    // 예: (1, 0) × 14 = (14, 0)
    awayFromMouse.mult(repelForce);

    // 파티클의 속도에 반발력 추가
    particle.vel.add(awayFromMouse);
  }
}

```

***

## 47. script\_develop

**파일 경로:** `251126_6lines_art\script_develop.js`

**설명:** script\_develop.js - p5.js 스케치

```javascript
// setup=_=>createCanvas(w=800,w)
// draw=_=>{
// x=random(-w/8,w+w/8);y=random(-w/8,w+w/8)
// stroke(random(255))
// for(i=0;i<w*2;i++)point(x+=cos(5*y/w*10)+cos(3*y/w*10),y+=sin(3*x/w*10)-cos(5*x/w*10))}

let w = 800;

function setup() {
  createCanvas(w, w);
  colorMode(HSB);
  frameRate(3); 
}

function draw() {
  background(5, 100);
  let x = random(-w / 8, w + w / 8);
  let y = random(-w / 8, w + w / 8);

  stroke(frameCount % 30 + 180, 80, 80);
  strokeWeight(0.5);

  let px = x,
    py = y;
  for (let i = 0; i < w * 2; i++) {
    x += cos(((5 * y) / w) * 10) + cos(((3 * y) / w) * 10);
    y += sin(((3 * x) / w) * 10) - cos(((5 * x) / w) * 10);
    line(px, py, x, y);
    px = x;
    py = y;
  }
}

```

***

## 48. script\_exploding\_blackhole

**파일 경로:** `251214_exploding_blackhole\script_exploding_blackhole.js`

**설명:** script\_exploding\_blackhole.js - p5.js 스케치

```javascript
// ==========================================
// [설정 영역] 비주얼 및 물리 관련 주요 전역 변수
// ==========================================

// 1. 기본 설정
const POINT_COUNT = 6000;       // 파티클 개수 (성능에 따라 조절: 4000 ~ 10000)
const BG_ALPHA = 30;            // 배경의 잔상 농도 (낮을수록 긴 잔상, 0~255)

// 2. 크기 및 노이즈 설정
let BASE_RADIUS_RATIO = 0.4;    // 화면 대비 파티클 분포 반지름 비율 (0.1 ~ 0.5)
let NOISE_SCALE = 0.01;         // 노이즈의 텍스처 크기 (작을수록 부드러움)
let NOISE_STRENGTH = 300;       // 노이즈가 위치에 미치는 영향력 (픽셀 단위)
let TIME_SPEED = 0.003;         // 노이즈 변화 속도

// 3. 움직임 설정
let ROTATION_SPEED_MIN = 0.006; // 최소 회전 속도
let ROTATION_SPEED_MAX = 0.012; // 최대 회전 속도
let EXPLOSION_FORCE_MIN = 20;   // 폭발 시 최소 힘
let EXPLOSION_FORCE_MAX = 35;   // 폭발 시 최대 힘
let DRAG = 0.91;                // 폭발 후 감속 비율 (1에 가까울수록 미끄러짐)

// 4. 색상 설정 (RGB)
// 중심부/저속일 때 색상 (Deep Blue-Purple)
const COLOR_CORE = [60, 20, 220];
// 외곽/고속일 때 색상 (Bright Cyan-White)
const COLOR_OUTER = [100, 255, 200];

// ==========================================

let points = [];
let state = "normal"; // 'normal' | 'explode'
let resetFrame = 0;
let cCore, cOuter;

function setup() {
  createCanvas(windowWidth, windowHeight);
  colorMode(RGB, 255);
  background(10);
  strokeWeight(2);

  // 색상 객체 생성
  cCore = color(...COLOR_CORE);
  cOuter = color(...COLOR_OUTER);

  initParticles();
}

function draw() {
  // 배경 잔상 효과
  background(10, 10, 15, BG_ALPHA);

  translate(width / 2, height / 2);

  // 폭발 상태가 오래 지속되면 자동으로 복귀
  if (state === "explode" && frameCount - resetFrame > 40) {
    state = "normal";
  }

  // 파티클 업데이트 및 그리기
  for (let i = 0; i < points.length; i++) {
    points[i].update();
    points[i].display();
  }
}

// 파티클 초기화 함수
function initParticles() {
  points = [];
  let maxR = min(width, height) * BASE_RADIUS_RATIO;

  for (let i = 0; i < POINT_COUNT; i++) {
    // 1. 초기 위치 설정 (도넛 모양 분포를 위해 랜덤 조정)
    let r = random(maxR * 0.3, maxR);
    let angle = random(TWO_PI);

    // 2. 파티클 객체 생성
    points.push(new Particle(r, angle));
  }
}

// ==========================================
// [클래스] Particle
// ==========================================
class Particle {
  constructor(r, angle) {
    this.baseR = r;     // 원래 궤도 반지름
    this.r = r;         // 현재 반지름
    this.angle = angle; // 현재 각도

    // 개별 속성 랜덤화
    this.angSpeed = random(ROTATION_SPEED_MIN, ROTATION_SPEED_MAX);
    this.noiseOffset = random(1000);

    // 물리 변수
    this.burstSpeed = 0; // 폭발 속도
    this.drift = 0;      // 노이즈에 의한 흔들림 값
  }

  update() {
    // 1. 상태별 물리 연산
    if (state === "explode") {
      // 폭발: 밖으로 튕겨나감 + 마찰력(감속)
      this.burstSpeed *= DRAG;
      this.r += this.burstSpeed;
    } else {
      // 정상: 천천히 원래 궤도로 복귀 (탄성)
      this.r = lerp(this.r, this.baseR, 0.009);

      // 지속적인 회전
      this.angle += this.angSpeed;
    }

    // 2. 노이즈 계산 (유기적인 움직임)
    // 시간과 고유 오프셋을 이용해 부드러운 난수 생성
    let n = noise(this.noiseOffset + frameCount * TIME_SPEED);

    // 노이즈 값을 -1 ~ 1 사이로 매핑하여 흔들림(drift) 생성
    this.drift = map(n, 0, 1, -1, 1) * NOISE_STRENGTH;
  }

  display() {
    // 3. 최종 위치 계산 (극좌표계 -> 직교좌표계)
    // 반지름 = 현재 반지름 + 노이즈 흔들림
    let finalR = this.r + this.drift;

    let x = cos(this.angle) * finalR;
    let y = sin(this.angle) * finalR;

    // 4. 색상 계산
    // 폭발 속도나 노이즈 강도에 따라 색상을 섞음
    let energy = constrain(
      abs(this.burstSpeed) * 0.1 +
        map(abs(this.drift), 0, NOISE_STRENGTH, 0, 1),
      0,
      1
    );
    let c = lerpColor(cCore, cOuter, energy);

    stroke(c);
    point(x, y);
  }

  // 폭발 힘 적용
  explode() {
    this.burstSpeed += random(EXPLOSION_FORCE_MIN, EXPLOSION_FORCE_MAX);
  }
}

// ==========================================
// [이벤트] 사용자 입력 처리
// ==========================================

function keyPressed() {
  if (key === " ") {
    state = "explode";
    resetFrame = frameCount;

    // 모든 파티클에 폭발 힘 전달
    for (let p of points) {
      p.explode();
    }
  }
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
  // 화면 크기가 바뀌면 파티클 위치 재계산
  initParticles();
}

```

***

## 49. script\_exploding\_blackhole\_instance

**파일 경로:** `251214_exploding_blackhole\script_exploding_blackhole_instance.js`

**설명:** script\_exploding\_blackhole\_instance.js - p5.js 스케치

```javascript
// ==========================================
// p5.js 인스턴스 모드
// ==========================================

const sketch = (p) => {
  // ==========================================
  // [설정 영역] 비주얼 및 물리 관련 주요 변수
  // ==========================================

  // 1. 기본 설정
  const POINT_COUNT = 6000; // 파티클 개수 (성능에 따라 조절: 4000 ~ 10000)
  const BG_ALPHA = 30; // 배경의 잔상 농도 (낮을수록 긴 잔상, 0~255)

  // 2. 크기 및 노이즈 설정
  let BASE_RADIUS_RATIO = 0.4; // 화면 대비 파티클 분포 반지름 비율 (0.1 ~ 0.5)
  let NOISE_SCALE = 0.01; // 노이즈의 텍스처 크기 (작을수록 부드러움)
  let NOISE_STRENGTH = 300; // 노이즈가 위치에 미치는 영향력 (픽셀 단위)
  let TIME_SPEED = 0.003; // 노이즈 변화 속도

  // 3. 움직임 설정
  let ROTATION_SPEED_MIN = 0.006; // 최소 회전 속도
  let ROTATION_SPEED_MAX = 0.012; // 최대 회전 속도
  let EXPLOSION_FORCE_MIN = 20; // 폭발 시 최소 힘
  let EXPLOSION_FORCE_MAX = 35; // 폭발 시 최대 힘
  let DRAG = 0.91; // 폭발 후 감속 비율 (1에 가까울수록 미끄러짐)

  // 4. 색상 설정 (RGB)
  // 중심부/저속일 때 색상 (Deep Blue-Purple)
  const COLOR_CORE = [60, 20, 220];
  // 외곽/고속일 때 색상 (Bright Cyan-White)
  const COLOR_OUTER = [100, 255, 200];

  // ==========================================

  let points = [];
  let state = "normal"; // 'normal' | 'explode'
  let resetFrame = 0;
  let cCore, cOuter;

  p.setup = () => {
    p.createCanvas(p.windowWidth, p.windowHeight);
    p.colorMode(p.RGB, 255);
    p.background(10);
    p.strokeWeight(2);

    // 색상 객체 생성
    cCore = p.color(...COLOR_CORE);
    cOuter = p.color(...COLOR_OUTER);

    initParticles();
  };

  p.draw = () => {
    // 배경 잔상 효과
    p.background(10, 10, 15, BG_ALPHA);

    p.translate(p.width / 2, p.height / 2);

    // 폭발 상태가 오래 지속되면 자동으로 복귀
    if (state === "explode" && p.frameCount - resetFrame > 40) {
      state = "normal";
    }

    // 파티클 업데이트 및 그리기
    for (let i = 0; i < points.length; i++) {
      points[i].update();
      points[i].display();
    }
  };

  // 파티클 초기화 함수
  const initParticles = () => {
    points = [];
    let maxR = p.min(p.width, p.height) * BASE_RADIUS_RATIO;

    for (let i = 0; i < POINT_COUNT; i++) {
      // 1. 초기 위치 설정 (도넛 모양 분포를 위해 랜덤 조정)
      let r = p.random(maxR * 0.3, maxR);
      let angle = p.random(p.TWO_PI);

      // 2. 파티클 객체 생성
      points.push(new Particle(r, angle));
    }
  };

  // ==========================================
  // [클래스] Particle
  // ==========================================
  class Particle {
    constructor(r, angle) {
      this.baseR = r; // 원래 궤도 반지름
      this.r = r; // 현재 반지름
      this.angle = angle; // 현재 각도

      // 개별 속성 랜덤화
      this.angSpeed = p.random(ROTATION_SPEED_MIN, ROTATION_SPEED_MAX);
      this.noiseOffset = p.random(1000);

      // 물리 변수
      this.burstSpeed = 0; // 폭발 속도
      this.drift = 0; // 노이즈에 의한 흔들림 값
    }

    update() {
      // 1. 상태별 물리 연산
      if (state === "explode") {
        // 폭발: 밖으로 튕겨나감 + 마찰력(감속)
        this.burstSpeed *= DRAG;
        this.r += this.burstSpeed;
      } else {
        // 정상: 천천히 원래 궤도로 복귀 (탄성)
        this.r = p.lerp(this.r, this.baseR, 0.009);

        // 지속적인 회전
        this.angle += this.angSpeed;
      }

      // 2. 노이즈 계산 (유기적인 움직임)
      // 시간과 고유 오프셋을 이용해 부드러운 난수 생성
      let n = p.noise(this.noiseOffset + p.frameCount * TIME_SPEED);

      // 노이즈 값을 -1 ~ 1 사이로 매핑하여 흔들림(drift) 생성
      this.drift = p.map(n, 0, 1, -1, 1) * NOISE_STRENGTH;
    }

    display() {
      // 3. 최종 위치 계산 (극좌표계 -> 직교좌표계)
      // 반지름 = 현재 반지름 + 노이즈 흔들림
      let finalR = this.r + this.drift;

      let x = p.cos(this.angle) * finalR;
      let y = p.sin(this.angle) * finalR;

      // 4. 색상 계산
      // 폭발 속도나 노이즈 강도에 따라 색상을 섞음
      let energy = p.constrain(
        p.abs(this.burstSpeed) * 0.1 +
          p.map(p.abs(this.drift), 0, NOISE_STRENGTH, 0, 1),
        0,
        1
      );
      let c = p.lerpColor(cCore, cOuter, energy);

      p.stroke(c);
      p.point(x, y);
    }

    // 폭발 힘 적용
    explode() {
      this.burstSpeed += p.random(EXPLOSION_FORCE_MIN, EXPLOSION_FORCE_MAX);
    }
  }

  // ==========================================
  // [이벤트] 사용자 입력 처리
  // ==========================================

  p.keyPressed = () => {
    if (p.key === " ") {
      state = "explode";
      resetFrame = p.frameCount;

      // 모든 파티클에 폭발 힘 전달
      for (let particle of points) {
        particle.explode();
      }
    }
  };

  p.windowResized = () => {
    p.resizeCanvas(p.windowWidth, p.windowHeight);
    // 화면 크기가 바뀌면 파티클 위치 재계산
    initParticles();
  };
};

// p5 인스턴스 생성
new p5(sketch);

```

***

## 50. script\_old

**파일 경로:** `251215_geometric_tiles\script_old.js`

**설명:** script\_old.js - p5.js 스케치

```javascript
let tiles = [];
let tileSize = 50;
let time = 0;

function setup() {
  const canvas = createCanvas(800, 800);
  colorMode(HSB, 360, 100, 100, 100);
  angleMode(DEGREES);

  // 타일 그리드 생성
  for (let x = 0; x < width; x += tileSize) {
    for (let y = 0; y < height; y += tileSize) {
      tiles.push({
        x: x,
        y: y,
        rotation: random(270),
        rotationSpeed: random(-4, 4),
        hue: random(20),
        shapeType: floor(random(4)), // 0: 사각형, 1: 원, 2: 삼각형, 3: 십자선
        noiseOffset: random(1000),
      });
    }
  }
}

function draw() {
  background(5, 5, 5, 100); // 어두운 배경

  time += 0.2;

  // 모든 타일 그리기
  for (let tile of tiles) {
    drawTile(tile);
  }
}

function drawTile(tile) {
  let centerX = tile.x + tileSize / 2;
  let centerY = tile.y + tileSize / 2;

  // 노이즈 기반 동적 변화
  let noiseVal = noise(
    tile.x * 0.01,
    tile.y * 0.01,
    time * 0.01 + tile.noiseOffset
  );

  // 회전 업데이트
  tile.rotation += tile.rotationSpeed * noiseVal;

  // 색상 변화
  let currentHue = (tile.hue + time * 0.5 + noiseVal * 100) % 360;
  let saturation = 40 + sin(time * 6 + tile.x * 0.01) * 20;
  let brightness = 50 + cos(time * 3 + tile.y * 0.1) * 50;

  // 타일 본체 그리기
  push();
  translate(centerX, centerY);
  rotate(tile.rotation + noiseVal * 20);

  noStroke();
  fill(currentHue, saturation, brightness, 100);

  // 모양 타입에 따라 다르게 그리기
  let size = tileSize * 0.5 * (0.8 + noiseVal * 0.4);

  switch (tile.shapeType) {
    case 0: // 사각형
      rect(-size / 2, -size / 2, size, size);
      break;
    case 1: // 원
      ellipse(0, 0, size, size);
      break;
    case 2: // 삼각형
      drawTriangle(size);
      break;
    case 3: // 십자선
      drawCross(size);
      break;
  }

  // 작은 하얀 원
  fill(0, 0, 100, 100);
  let innerSize = size * 0.2;
  ellipse(10, 0, innerSize, innerSize); // 위치를 움직이면 재미있는 애니메이션

  pop();
}

function drawTriangle(size) {
  beginShape();
  for (let i = 0; i < 3; i++) {
    let angle = 120 * i - 90;
    let x = (cos(angle) * size) / 2;
    let y = (sin(angle) * size) / 2;
    vertex(x, y);
  }
  endShape(CLOSE);
}

function drawCross(size) {
  rect(-size / 2, -size / 6, size, size / 3);
  rect(-size / 6, -size / 2, size / 3, size);
}

function keyPressed() {
  if (key === " ") {
    // 스페이스바로 타일 재배치
    for (let tile of tiles) {
      tile.rotationSpeed = random(-3, 3);
      tile.hue = random(360);
      tile.shapeType = floor(random(4));
    }
  }
}

```

***

## 51. script\_red\_rotating

**파일 경로:** `251214_exploding_blackhole\script_red_rotating.js`

**설명:** script\_red\_rotating.js - p5.js 스케치

```javascript
// script_red_rotating.js

let points = [];
const POINT_COUNT = 8000;

let baseColor, fastColor;
let state = "collapse";
let resetFrame = 0;

function setup() {
  createCanvas(windowWidth, windowHeight);
  colorMode(RGB, 255);
  background(10);
  strokeWeight(2);

  baseColor = color(255, 80, 40);
  fastColor = color(245, 200, 100);

  for (let i = 0; i < POINT_COUNT; i++) {
    let r0 = random(80, width * 0.45);

    points.push({
      r0: r0,
      r: r0,
      angle: random(TWO_PI),
      angularSpeed: random(0.005, 0.01),
      collapse: random(0.0005, 0.0015),
      // 폭발용
      burstSpeed: 0,
      noiseOffset: random(1000),
    });
  }
}

function draw() {
  translate(width / 2, height / 2);
  background(10, 25);

  for (let p of points) {
    if (state === "explode") {
      // 중심에서 바깥으로 튕김
      p.burstSpeed *= 0.92; // 감쇠
      p.r += p.burstSpeed;

      // 폭발 종료 조건
      if (frameCount - resetFrame > 40) {
        state = "collapse";
      }
    } else {
      // 붕괴 상태
      p.angularSpeed *= 1.0015;
      p.r *= 1 - p.collapse;
    }

    // 회전은 항상 유지
    p.angle += p.angularSpeed;

    // 붕괴 노이즈
    let n = noise(p.noiseOffset + frameCount * 0.01);
    let distortion = map(n, 0, 1, -1, 1) * p.angularSpeed * 800;

    let x = cos(p.angle) * (p.r + distortion);
    let y = sin(p.angle) * (p.r + distortion);

    // 속도 기반 색상
    let speedEnergy = abs(p.burstSpeed) + p.angularSpeed * 20;
    let speedNorm = constrain(map(speedEnergy, 0, 2, 0, 1), 0, 1);

    let c = lerpColor(baseColor, fastColor, speedNorm);
    stroke(c);
    point(x, y);
  }
}

function keyPressed() {
  if (key === " ") {
    state = "explode";
    resetFrame = frameCount;

    for (let p of points) {
      // 중심에서 바깥 방향으로 폭발
      p.burstSpeed = random(10, 30);
      p.angularSpeed = random(0.002, 0.01);
    }
  }
}

```

***

## 52. script\_sound\_vis\_1

**파일 경로:** `251223_keyboard_sound_visualizer\script_sound_vis_1.js`

**설명:** script\_sound\_vis\_1.js - p5.js 스케치

```javascript
let audioCtx;
let activeNotes = new Map(); // 눌린 키 관리
let visualEvents = [];

const keyMap = {
  a: 261.63, // C4
  s: 293.66, // D4
  d: 329.63, // E4
  f: 349.23, // F4
  g: 392.0, // G4
  h: 440.0, // A4
  j: 493.88, // B4
};

function setup() {
  createCanvas(800, 800);
  background(20);
  textAlign(CENTER, CENTER);
  textSize(14);
}

function draw() {
  background(20, 40);

  // 시각 이벤트 렌더링
  for (let i = visualEvents.length - 1; i >= 0; i--) {
    let v = visualEvents[i];
    v.radius += 2;
    v.alpha -= 4;

    noFill();
    stroke(255, v.alpha);
    circle(width / 2, height / 2, v.radius);

    if (v.alpha <= 0) {
      visualEvents.splice(i, 1);
    }
  }

  fill(200);
  noStroke();
  text("A S D F G H J 키를 눌러 연주합니다", width / 2, height - 40);
}

function keyPressed() {
  let k = key.toLowerCase();
  if (!(k in keyMap)) return;

  // 오디오 컨텍스트 초기화 (사용자 입력 후)
  if (!audioCtx) {
    audioCtx = new AudioContext();
  }

  if (activeNotes.has(k)) return;

  let freq = keyMap[k];
  let osc = audioCtx.createOscillator();
  let gain = audioCtx.createGain();

  osc.type = "sine";
  osc.frequency.value = freq;

  // 엔벨로프
  let now = audioCtx.currentTime;
  gain.gain.setValueAtTime(0, now);
  gain.gain.linearRampToValueAtTime(0.5, now + 0.05);

  osc.connect(gain);
  gain.connect(audioCtx.destination);

  osc.start();

  activeNotes.set(k, { osc, gain });

  // 시각 이벤트 추가
  visualEvents.push({
    radius: 10,
    alpha: 255,
  });
}

function keyReleased() {
  let k = key.toLowerCase();
  if (!activeNotes.has(k)) return;

  let { osc, gain } = activeNotes.get(k);
  let now = audioCtx.currentTime;

  gain.gain.cancelScheduledValues(now);
  gain.gain.setValueAtTime(gain.gain.value, now);
  gain.gain.linearRampToValueAtTime(0, now + 0.2);

  osc.stop(now + 0.25);
  activeNotes.delete(k);
}

```

***

## 53. script\_sound\_vis\_2

**파일 경로:** `251223_keyboard_sound_visualizer\script_sound_vis_2.js`

**설명:** script\_sound\_vis\_2.js - p5.js 스케치

```javascript
let audioCtx;
let masterGain;
let activeNotes = new Map();
let noteData = {};

const keyMap = {
  a: 196.0, // G3 (솔)
  s: 220.0, // A3 (라)
  d: 246.94, // B3 (시)
  f: 261.63, // C4 (도)
  g: 293.66, // D4 (레)
  h: 329.63, // E4 (미)
  j: 349.23, // F4 (파)
  k: 392.0, // G4 (솔)
  l: 440.0, // A4 (라)
  ";": 493.88, // B4 (시)
  "'": 523.25, // C5 (도)
};

const keyOrder = ["a", "s", "d", "f", "g", "h", "j", "k", "l", ";", "'"];
const maxRadius = 300;

function setup() {
  createCanvas(1920, 540);
  colorMode(HSB, 360, 100, 100, 255);
  background(0);

  const step = width / (keyOrder.length + 1);

  keyOrder.forEach((k, i) => {
    noteData[k] = {
      freq: keyMap[k],
      x: step * (i + 1),
      y: height / 2,
      hue: map(i, 0, keyOrder.length - 1, 0, 360),
      radius: 0,
      active: false,
      startTime: 0,
    };
  });
}

function draw() {
  background(0, 20);

  for (let k in noteData) {
    let n = noteData[k];

    if (n.active) {
      let held = millis() - n.startTime;
      n.radius = constrain(held * 0.15, 0, maxRadius);
    } else {
      n.radius *= 0.9;
    }

    if (n.radius > 1) {
      noFill();
      stroke(n.hue, 80, 100, 200);
      strokeWeight(2);
      circle(n.x, n.y, n.radius * 2);
    }
  }
}

function keyPressed() {
  let k = key.toLowerCase();
  if (!(k in noteData)) return;

  if (!audioCtx) {
    audioCtx = new AudioContext();

    // Master Gain 생성
    masterGain = audioCtx.createGain();
    masterGain.gain.value = 0.9;
    masterGain.connect(audioCtx.destination);
  }

  if (activeNotes.has(k)) return;

  let osc = audioCtx.createOscillator();
  let gain = audioCtx.createGain();
  let now = audioCtx.currentTime;

  osc.type = "sine";
  osc.frequency.value = noteData[k].freq;

  gain.gain.setValueAtTime(0, now);
  gain.gain.linearRampToValueAtTime(0.5, now + 0.08);

  osc.connect(gain);
  gain.connect(masterGain);
  osc.start();

  activeNotes.set(k, { osc, gain });

  noteData[k].active = true;
  noteData[k].startTime = millis();

  updateMasterGain();
}

function keyReleased() {
  let k = key.toLowerCase();
  if (!activeNotes.has(k)) return;

  let { osc, gain } = activeNotes.get(k);
  let now = audioCtx.currentTime;

  gain.gain.cancelScheduledValues(now);
  gain.gain.setValueAtTime(gain.gain.value, now);
  gain.gain.linearRampToValueAtTime(0, now + 0.25);

  osc.stop(now + 0.3);

  activeNotes.delete(k);
  noteData[k].active = false;

  updateMasterGain();
}

function updateMasterGain() {
  if (!masterGain) return;

  let noteCount = activeNotes.size;
  if (noteCount === 0) {
    masterGain.gain.setTargetAtTime(0.9, audioCtx.currentTime, 0.05);
    return;
  }

  let target = 0.9 / Math.sqrt(noteCount);
  masterGain.gain.setTargetAtTime(target, audioCtx.currentTime, 0.05);
}

```
---