[[Generative Art Concepts]]
# <span style="color: #BF2F9B">**Concepts for Generative Art**</span>

✳️✴️❇️☑️ 제너레이티브 아트를 위한 개념

## <span style="color: #ed9149">**Preface**</span>
### ✴️ About this book (이 책에 관하여)
### ✴️ How to use this book (이 책 활용 방법)
### ✴️ Recommended toolchains (권장 도구 모음)
- p5.js
- Processing
- openFrameworks
- Nannou
- Blender (bpy)

---
---

# <span style="color: #BF2F9B">**Part I. Foundations**</span>
> 기초: 
수학, 공간, 색, 시간: 이미지를 생성하고 움직임을 표현하는 데 필수적인 수학적 토대(좌표, 벡터, 행렬), 시각적 요소(색상, 조명), 그리고 시간 제어(프레임, 델타 타임)의 기본 개념을 다룹니다.

## Chapter 1. <span style="color: #ed9149">**Coordinates**</span> (좌표)
### ✴️ Cartesian coordinate systems 
(데카르트 좌표계)
### ✴️ Pixel space 
(픽셀 공간)
### ✴️ Normalized Device Coordinates 
(NDC, 정규화된 디바이스 좌표)
### ✴️ UV coordinates 
(UV 좌표)
### ✴️ World, Local, View, Projection spaces 
(월드, 로컬, 뷰, 투영 공간)

---

## Chapter 2. <span style="color: #ed9149">**Geometry**</span> (기하학)

### ✴️ Point, Vertex, Edge, Face, Mesh 
(점, 정점, 모서리, 면, 메시)
### ✴️ 2D and 3D primitives 
(2D/3D 기본 도형)
### ✴️ Curves 
- Bézier 
- NURBS 
- Spline (Catmull-Rom, B-Spline)
- Parametric 

(베지어, NURBS, 스플라인 (캣멀-롬, B-스플라인), 매개 변수)
### ✴️ Polygon and polyline 
(다각형 및 폴리라인)
### ✴️ Vector and Vector operations 
(벡터 및 벡터 연산)
### ✴️ Degree and Radian
(각도와 라디안)
### ✴️ Euler Angles and Quaternion
(오일러각과 쿼터니언)

### ✴️ Normals and tangents 
(법선 및 탄젠트)

### ✴️ Sampling and interpolation 
(샘플링 및 보간)

### ✴️ Instancing, Duplication, Point-cloud 
(인스턴싱, 복제, 포인트 클라우드)

---

## Chapter 3. <span style="color: #ed9149">**Color and Perception**</span> (색상과 인지)
#### ✴️ Color Model
- RGB
- HSB
- HSL
### ✴️ Alpha blending 
(알파 블렌딩)

### ✴️ Color palettes 
(컬러 팔레트)

### ✴️ Color Gradient 
(컬러 그라디언트)

### ✴️ Color mapping and Range Mapping 
(색상 매핑 및 범위 매핑)

### ✴️ Basic lighting: ambient, diffuse, specular 
(기본 조명: 앰비언트, 디퓨즈, 스페큘러)

---

## Chapter 4. <span style="color: #ed9149">**Time and Motion**</span> (시간과 움직임)
### ✴️ Frame-based animation 
(프레임 기반 애니메이션)
### ✴️ FPS, V-sync 
(FPS, 수직 동기화)
### ✴️ Delta-time 
(델타 시간)
### ✴️ Fixed vs variable timestep 
(고정 대 가변 시간 간격)
### ✴️ Easing functions 
(이징 함수)
### ✴️ Temporal interpolation 
(시간 보간)
### ✴️ Determinism and reproducibility 
(결정론 및 재현성)
### ✴️ Loop 
(루프)
### ✴️ Time Remapping 
(타임 리매핑)

---
## Chapter 5. <span style="color: #ed9149">**Trigonometric**</span> (삼각함수)
### ✴️ Oscillation and periodicity 
(오실레이션 및 주기성)
### ✴️ Wave Pattern / Waveform 
(파형 패턴 / 파형)
### ✴️ Spiral Pattern 
(나선 패턴, 리사주 패턴)

---
---

# <span style="color: #BF2F9B">**Part II. Randomness and Noise**</span>
> 랜덤성과 노이즈: 
자연스러우면서도 예측 불가능한 복잡성을 창출하는 핵심 원리입니다. 난수 생성 및 분포를 시작으로, 자연의 흐름이나 유기적인 패턴을 모방하는 노이즈(Perlin, Simplex)와 프랙탈 구조를 탐구합니다.

## Chapter 6. <span style="color: #ed9149">**Probability and Randomness**</span> (확률 및 랜덤성)
### ✴️ Pseudo-random number generators 
(의사 난수 생성기)
### ✴️ Seeding 
(시드)
### ✴️ Uniform distribution 
(균등 분포)
### ✴️ Gaussian distribution 
(가우시안 분포)
### ✴️ Poisson distribution 
(포아송 분포)
### ✴️ Beta and specialized distributions 
(베타 및 특수 분포)
### ✴️ Weighted randomness 
(가중 랜덤)

---

## Chapter 7. <span style="color: #ed9149">**Noise and Fractals**</span> (노이즈 및 프랙탈)
### ✴️ Perlin noise 
(퍼린 노이즈)
### ✴️ Simplex noise 
(심플렉스 노이즈)
### ✴️ Worley noise (Cellular Noise) 
(워리 노이즈(셀룰러 노이즈))
### ✴️ Fractal Brownian Motion (fBm) 
(프랙탈 브라운 운동)
### ✴️ Ridged noise 
(리지드 노이즈)
### ✴️ Curl noise 
(컬 노이즈)
### ✴️ Noise dimensions and domains 
(노이즈 차원 및 도메인)
### ✴️ Noise tiling and periodization 
(노이즈 타일링 및 주기화)
### ✴️ Procedural textures 
(절차적 텍스처)
### ✴️ Recursion 
(재귀)
### ✴️ Self-similarity 
(자기 유사성)
### ✴️ Fractals 
- Mandelbrot
- Julia
- IFS
### ✴️ Recursive Tree 
(재귀적 트리)

---

# <span style="color: #BF2F9B">**Part III. Discrete and Grammar Systems**</span>
이산 및 문법 시스템: 간단한 규칙(문법)을 이산적인(단계적인) 반복을 통해 적용하여 나무, 미생물, 추상 패턴 등 복잡한 전역 구조를 생성하는 알고리즘(L-System, 셀룰러 오토마타 등)을 학습합니다.

## Chapter 8. <span style="color: #ed9149">**Iteration and Recursion**</span> (반복 및 재귀)
### ✴️ Iterative structures 
(반복 구조)
### ✴️ Recursive patterns 
(재귀 패턴)
### ✴️ Recursive trees 
(재귀적 트리)
### ✴️ 1D, 2D, 3D Arrays 
(1, 2, 3차원 배열)

---

## Chapter 9. <span style="color: #ed9149">**Grammar Systems**</span> (문법 시스템)
### ✴️ L-systems 
(L-시스템)
### ✴️ String rewriting 
(문자열 재작성)
### ✴️ Stochastic grammars 
(확률적 문법)
### ✴️ Branching 
(분지)

---

## Chapter 10. <span style="color: #ed9149">**Cellular and Reaction Systems**</span> (셀룰러 및 반응 시스템)
### ✴️ Cellular Automata 
(셀룰러 오토마타)
### ✴️ Custom Rules 
(커스텀 규칙)
### ✴️ Game of Life 
(라이프 게임)
### ✴️ Reaction-Diffusion systems 
(반응-확산 시스템)
### ✴️ Diffusion-Limited Aggregation (DLA) 
(확산 제한 응집)
### ✴️ Laplacian growth 
(라플라스 성장)

---

# <span style="color: #BF2F9B">**Part IV. Spatial Structures and Geometry Pattern**</span>
> 공간 구조 및 기하학 처리: 
생성된 결과물을 공간적으로 조직하고 구조화하는 기법을 다룹니다. 격자, 타일링, 공간 분할(Voronoi, Delaunay) 등 효율적인 공간 관리 및 기하학적 형태 처리 방법을 익힙니다.

## Chapter 11. <span style="color: #ed9149">**Grids and Tiling**</span> (격자 및 타일링)
### ✴️ 1D, 2D, 3D grids 
(1, 2, 3차원 격자)

### ✴️ Square tiling 
(정사각 타일링)

### ✴️ Hexagonal tiling 
(육각 타일링)

### ✴️ Triangular tiling 
(삼각 타일링)

### ✴️ Offset and staggered grids 
(오프셋 및 지그재그 격자)

### ✴️ Pattern repetition 
(패턴 반복)

### ✴️ Tessellation 
(테셀레이션)

### ✴️ Symmetry: radial, rotational, mirror 
(대칭: 방사형, 회전형, 거울)


---

## Chapter 12. <span style="color: #ed9149">**Spatial Data Structures**</span> (공간 데이터 구조)
### ✴️ Quadtree 
(쿼드트리)

### ✴️ Octree 
(옥트리)

### ✴️ KD-tree 
(KD-트리)

### ✴️ BSP trees (Binary Space Partitioning Trees) 
(BSP 이진 공간 분할 트리)

---

## Chapter 13. <span style="color: #ed9149">**Geometric Algorithms**</span> (기하학 알고리즘)
### ✴️ Delaunay triangulation 
(들로니 삼각 분할)

### ✴️ Voronoi diagram 
(보로노이 다이어그램)

### ✴️ Boolean polygon operations 
(불리언 다각형 연산)

### ✴️ Mesh subdivision 
(메시 세분화)

### ✴️ Signed Distance Fields (SDF) 
(부호 있는 거리 함수)

### ✴️ Surface/Volume: Implicit vs Explicit Form 
(표면/볼륨: 암시적 대 명시적 형태)


---

# <span style="color: #BF2F9B">**Part V. Dynamics and Physics**</span>
> 역학과 물리: 
정적인 형태에 시간성을 부여하고 실제 물리 법칙에 기반한 움직임과 상호작용을 구현합니다. 입자 시스템, 외부 힘(중력, 스프링), 충돌 감지 및 시뮬레이션 기술을 포함합니다.

## Chapter 14. <span style="color: #ed9149">**Particle Systems**</span> (입자 시스템)
### ✴️ Emitters 
(이미터)

### ✴️ Particle attributes 
(입자 속성)

### ✴️ Lifespan 
(수명)

### ✴️ Integrators: Euler, Verlet, RK 
(적분기: 오일러, 벌렛, 룬게-쿠타)

### ✴️ GPU particle approaches 
(GPU 입자 접근 방식)

### ✴️ GPU Instancing 
(GPU 인스턴싱)

---

## Chapter 15. <span style="color: #ed9149">**Forces and Constraints**</span> (힘과 제약)
### ✴️ Gravity 
(중력)

### ✴️ Wind, Turbulence 
(바람, 터뷸런스)

### ✴️ Global Field 
(전역장)

### ✴️ Springs, Distance Constraints 
(스프링, 거리 제약)

### ✴️ Velocity, Acceleration, Damping 
(속도, 가속, 감쇠)

### ✴️ XPBD (Extended Position Based Dynamics) 
(XPBD)

---

## Chapter 16. <span style="color: #ed9149">**Collision and Simulation**</span> (충돌 및 시뮬레이션)
### ✴️ Collision detection: AABB, sphere, OBB 
(충돌 감지: AABB, 구, OBB)

### ✴️ Collision response 
(충돌 반응)

### ✴️ Rigid body basics 
(강체 기본)

### ✴️ Fluid simulation concepts 
(유체 시뮬레이션 개념)

### ✴️ Stable constraint systems 
(안정적인 제약 시스템)

### ✴️ Boundary, Reflection 
(경계, 반사)

### ✴️ Attraction, Repulsion

---

# <span style="color: #BF2F9B">**Part VI. Collective Behavior and Algorithms**</span>
> 군집 행동 및 알고리즘: 
개별 요소들이 모여 전체적인 복잡한 행동을 만들어내는 시스템(군집 지능, Boids)과, 최적의 경로 찾기 및 패턴을 생성하는 고급 알고리즘(유전자 알고리즘, 마르코프 체인)을 탐구합니다.

## Chapter 17. <span style="color: #ed9149">Flocking and Agent-Based Systems</span> (군집 및 에이전트 기반 시스템)

자율적으로 움직이는 개별 주체(Agent)를 정의하고, 이들이 환경 및 다른 에이전트와 상호작용하며 복잡한 군집 행동을 만들어내는 원리를 학습합니다.

### ✴️ Agent-Based Systems 
(에이전트 기반 시스템)

### ✴️ Steering Behaviors 
(조종 행동)
  - Seeking (추적/끌림)
  - Avoiding (회피/밀쳐냄)
  - Attraction / Repulsion (인력 / 척력)
### ✴️ Flocking and Swarm Intelligence 
(군집 및 스웜 인텔리전스)
  - Boids (보이드)
  - Separation (분리)
  - Alignment (정렬)
  - Cohesion (응집)
- Field-driven movement (필드 기반 움직임)

---

## Chapter 18. <span style="color: #ed9149">**Search and Optimization**</span> (탐색 및 최적화)
### ✴️ A* Search Algorithm 
(A 탐색 알고리즘)

### ✴️ Dijkstra's Algorithm 
(다익스트라 알고리즘)

### ✴️ Graph representations 
(그래프 표현)

### ✴️ Pathfinding 
(경로 찾기)

### ✴️ Monte Carlo methods 
(몬테카를로 방법)

### ✴️ Markov chains 
(마르코프 체인)

### ✴️ Genetic algorithms 
(유전자 알고리즘)

### ✴️ Evolutionary art processes 
(진화 예술 프로세스)

---

# <span style="color: #BF2F9B">**Part VII. Shaders and GPU Techniques**</span>
> 셰이더 및 GPU 기술: 
중앙처리장치(CPU)의 한계를 넘어 GPU의 병렬 처리 능력을 활용하는 방법을 다룹니다. GLSL 언어를 사용한 Vertex, Fragment, Compute Shader의 기본 원리와 레이 마칭 등 GPU 기반 렌더링 기술을 심도 있게 학습합니다.

## Chapter 19. <span style="color: #ed9149">**Shader Fundamentals**</span> (셰이더 기본)
### ✴️ GLSL basics 
(GLSL 기본)

### ✴️ Vertex shader 
(버텍스 셰이더)

### ✴️ Fragment shader 
(프래그먼트 셰이더)

### ✴️ Compute shader 
(컴퓨트 셰이더)

### ✴️ Attributes, Varyings, Uniforms 
(속성, 베어링, 유니폼)


---

## Chapter 20. <span style="color: #ed9149">**GPU Rendering Techniques**</span> (GPU 렌더링 기술)
### ✴️ Textures and samplers 
(텍스처 및 샘플러)

### ✴️ Framebuffer objects (FBO) 
(프레임 버퍼 객체)

### ✴️ Render-to-texture 
(텍스처로 렌더링)

### ✴️ Ray marching 
(레이 마칭)

### ✴️ Ray tracing 
(레이 트레이싱)

### ✴️ SDF rendering patterns 
(SDF 렌더링 패턴)

### ✴️ Post-processing 
(후처리)

### ✴️ Anti-aliasing techniques 
(안티 에일리어싱 기법)


---

# <span style="color: #BF2F9B">**Part VIII. Rendering and Visual Expression**</span>
> 렌더링 및 시각적 표현: 
생성된 결과물을 최종적으로 시각화하여 품질을 높이는 렌더링 파이프라인과 후처리 기술을 다룹니다. 시각적 인지 원리와 디자인 요소를 통합하여 작품의 완성도를 높이는 방법을 모색합니다.

## Chapter 21. <span style="color: #ed9149">**Rendering Pipeline**</span> (렌더링 파이프라인)
### ✴️ Rasterization 
(래스터화)

### ✴️ Depth testing 
(깊이 테스트)

### ✴️ Tone mapping 
(톤 매핑)

### ✴️ Gamma correction 
(감마 보정)

### ✴️ Depth of field (DOF) 
(피사계 심도)

### ✴️ Motion blur 
(모션 블러)

### ✴️ Pixelization 
(픽셀화)

### ✴️ Antialiasing 
(안티 에일리어싱)

### ✴️ Vector Graphics 
(벡터 그래픽스)

### ✴️ PBR (Physically Based Rendering) 
(물리 기반 렌더링)

---

## Chapter 22. <span style="color: #ed9149">**Visual Design Principles**</span> (시각 디자인 원칙)
### ✴️ Visual hierarchy 
(시각적 계층 구조)

### ✴️ Balance and symmetry 
(균형 및 대칭)

### ✴️ Contrast 
(대비)

### ✴️ Rhythm 
(리듬)

### ✴️ Shape language 
(형태 언어)

### ✴️ Perceptual considerations 
(지각적 고려 사항)

### ✴️ Typographic elements 
(타이포그래피 요소)

---

# <span style="color: #BF2F9B">**Part IX. Data, Interaction, and Machine Learning**</span>
> 데이터, 상호작용 및 머신 러닝: 
외부 데이터(CSV, MIDI, 센서)를 작품의 입력이나 동력으로 활용하는 방법,사용자 입력에 실시간으로 반응하는 인터랙션 기술, 그리고 최신 AI 기술(GAN, VAE)을 활용하는 방법을 탐구합니다.

## Chapter 23. <span style="color: #ed9149">**Data-Driven Generation**</span> (데이터 기반 생성)
### ✴️ CSV, JSON
### ✴️ MIDI
### ✴️ Real-time sensors
(실시간 센서)

### ✴️ OSC, UDP Networking
### ✴️ Signal processing
(신호 처리)
### ✴️ FFT (Fast Fourier Transform)
(고속 푸리에 변환)

---

## Chapter 24. <span style="color: #ed9149">User Interaction and Sensing</span> (사용자 인터랙션 및 감지)
### ✴️ Input Devices (입력 장치)
  - Mouse, Keyboard (마우스, 키보드)
  - Game Controllers (게임 컨트롤러)
  - Multi-touch screen (멀티터치 스크린)

### ✴️ Physical Sensing (물리적 감지)
  - Camera and Vision Processing (카메라 및 비전 처리): Webcam Input, 
  - Computer Vision Basics (웹캠 입력, 컴퓨터 비전 기본)
  - Depth Sensing (깊이 감지): Kinect, Lidar, ToF (키넥트, 라이다, ToF)
  - Microphone and Audio Input (마이크 및 오디오 입력)
  - Custom Sensor Integration (커스텀 센서 통합): Arduino/Raspberry Pi (아두이노/라즈베리 파이)

### ✴️ Output Feedback (출력 피드백)
  - Haptic Feedback (촉각 피드백)

---

## Chapter 25. <span style="color: #ed9149">**Machine Learning for Generative Art**</span> (제너레이티브 아트를 위한 머신 러닝)
### ✴️ Neural networks 
(신경망)
### ✴️ GAN (Generative Adversarial Networks) 
(생성적 적대 신경망)
### ✴️ VAE (Variational Autoencoder) 
(변이형 오토인코더)
### ✴️ Latent space 
(잠재 공간)
### ✴️ Reinforcement learning 
(강화 학습)

---

# <span style="color: #BF2F9B">**Part X. Tools, Frameworks, and Workflow**</span>
> 도구, 프레임워크 및 워크플로우: 
제너레이티브 아트를 위한 다양한 프로그래밍 언어, 크리에이티브 코딩 프레임워크(p5.js, openFrameworks), 3D/웹 환경, 그리고 효율적인 코드 관리 및 배포(Bundler, WebGPU)를 위한 실용적인 워크플로우를 다룹니다.

## Chapter 26. <span style="color: #ed9149">**Creative Coding Environments**</span> (크리에이티브 코딩 환경)
- JavaScript / C++ / Python / Rust / GLSL runtime (자바스크립트/C++/파이썬/러스트/GLSL 런타임)
- p5.js, Processing, openFrameworks, Nannou, openRNDR (p5.js, 프로세싱, 오픈프레임웍스, 나노우, 오픈RNDR)
- Blender bpy (블렌더 bpy)

---

## Chapter 27. <span style="color: #ed9149">**3D and Web Frameworks**</span> (3D 및 웹 프레임워크)
- Three.js, Babylon.js (Three.js, Babylon.js)
- React Three Fiber (React Three Fiber)
- WebGL, WebGPU (WebGL, WebGPU)
- TouchDesigner (터치디자이너)
- Cables.gl

---

## Chapter 28. <span style="color: #ed9149">**Shader Tools**</span> (셰이더 도구)
- ShaderToy, ShaderPark (ShaderToy, ShaderPark)
- Offline rendering tools (오프라인 렌더링 도구)
- WebGPU IDE (WebGPU IDE)

---

## Chapter 29. <span style="color: #ed9149">**Workflow and Deployment**</span> (워크플로우 및 배포)
- ES Modules (ES 모듈)
- Bundlers: Vite, Parcel (번들러: Vite, Parcel)
- Web deployment (웹 배포)
- Exhibition setup (전시 설정)
- GPU / CPU / Texture IO / Buffer (GPU / CPU / 텍스처 I/O / 버퍼)

---

# <span style="color: #BF2F9B">**Part XI. Projects and Exercises**</span>
> 프로젝트 및 실습: 
앞선 파트에서 학습한 개념들을 실제 작업으로 통합하는 실습 위주의 장입니다. 노이즈 기반 풍경, 파티클 시스템, L-시스템 식물 등 구체적인 프로젝트를 통해 기술 숙련도를 높입니다.

## Chapter 30. <span style="color: #ed9149">**Creative Coding Exercises**</span> (크리에이티브 코딩 실습)
- Minimal reproducible sketch (최소 재현 가능 스케치)
- Noise-based landscape (노이즈 기반 풍경)
- Particle fireworks (파티클 불꽃놀이)
- Reaction-diffusion textures (반응-확산 텍스처)
- L-system plant (L-시스템 식물)
- Data-driven visualization (데이터 기반 시각화)
- Interactive installation (인터랙티브 설치)
- GAN-assisted morphing (GAN 지원 모핑)