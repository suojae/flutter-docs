공식문서 링크 <br/>
[https://docs.flutter.dev/ui/animations/tutorial](https://docs.flutter.dev/ui/animations/tutorial

<br/>

## Animations tutorial

<br/>

### Essential animation concepts and classes

- `Animation` 은 플러터 라이브러리에 정의된 클래스 중 하나이다.
- `Animation` 객체는 현재 애니메이션 상태를 알 수 있다. 하지만 스크린에 무엇이 나타나는지는 알지 못한다.
- `Animation Controller`가 플러터 애니메이션을 관리한다.
- `CurvedAnimatinon`이 Non-Linear Curve (비선형 곡선) 애니메이션을 관리한다.
- Tween은 Flutter 애니메이션에서 값을 범위(begin, end) 사이로 변환(보간)하는 데 사용하는 클래스다. 애니메이션이 진행되면서, Tween이 값을 "중간 단계"로 계산해서 애니메이션 효과를 만들어줌

<br/>

### Animation<double>

```dart
class MyAnimationWidget extends StatefulWidget {
  @override
  _MyAnimationWidgetState createState() => _MyAnimationWidgetState();
}

class _MyAnimationWidgetState extends State<MyAnimationWidget>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller; // 애니메이션의 실행과 속도를 제어하는 컨트롤러
  late Animation<double> _animation;    // double 값을 생성하고 관리하는 애니메이션 객체

  @override
  void initState() {
    super.initState();

    // AnimationController 생성
    _controller = AnimationController(
      duration: const Duration(seconds: 2), // 애니메이션의 총 실행 시간: 2초
      vsync: this, // vsync를 사용해 오프스크린 애니메이션의 리소스 낭비 방지
    );

    // Animation<double> 생성
    _animation = Tween<double>(begin: 0.0, end: 300.0) // 0.0에서 300.0까지 값을 보간
        .animate(_controller) // AnimationController와 결합해 애니메이션 정의
      ..addListener(() { // 애니메이션 값이 변경될 때마다 호출
        setState(() {
          // setState()를 호출해 위젯을 다시 빌드하고, 변경된 애니메이션 값 적용
        });
      });

    _controller.forward(); // 애니메이션 시작: 시작 값에서 끝 값으로 진행
  }

  @override
  void dispose() {
    _controller.dispose(); // AnimationController 리소스 해제
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Container(
        width: _animation.value,  // 애니메이션의 현재 값을 width에 적용
        height: _animation.value, // 애니메이션의 현재 값을 height에 적용
        color: Colors.blue,       // 고정된 파란색 박스
      ),
    );
  }
}

/*
  추가 설명:
  - Tween<double>(begin: 0.0, end: 300.0)
    - `begin`: 애니메이션의 시작 값 (여기서는 0.0)
    - `end`: 애니메이션의 종료 값 (여기서는 300.0)
    - 이 값들은 애니메이션 진행 중에 위젯의 속성(크기, 위치 등)에 적용됨.

  - 이 예제에서는 width와 height에 사용됨:
    1. 처음: width와 height는 0.0으로 시작.
    2. 진행 중: 0.0 → 300.0 사이 값을 생성하며, 시간이 지남에 따라 크기가 커짐.
    3. 끝: 애니메이션이 종료되면 width와 height는 300.0에 도달.

  - AnimationController의 duration(2초)과 결합:
    - 2초 동안 Tween의 값을 단계적으로 계산해 애니메이션 효과를 만듦.
*/

```

- `Animation<double>`은 항상 현재 값을 `.value` 속성으로 제공한다.
- 이 값은 `Tween`과 `AnimationController`에 의해 계산된다.
- `Animation<double>`의 주요 속성으로는 3가지가 있다.

1. `.value`: 현재 애니메이션 값. 예: 0.0에서 1.0 또는 0.0에서 300.0까지 변화.
2. `.status`: 현재 애니메이션의 상태. 예: AnimationStatus.forward, AnimationStatus.completed.
3. `.isCompleted` (추론 속성): 애니메이션이 완료되었는지 확인.

<br/>

- Animation<double>의 이벤트 리스너는 2가지가 있다.
```dart
_animation.addStatusListener((status) {
  if (status == AnimationStatus.completed) {
    print('애니메이션 완료');
  }
});
```
- `addListener()`: 애니메이션 값이 변경될 때 호출. 예: UI를 다시 그리기 위해 setState() 호출.
- `addStatusListener()`:애니메이션 상태 변화(시작, 완료, 역방향 등)를 감지.

<br/>


### Curved Animation 

```dart
animation = CurvedAnimation(parent: controller, curve: Curves.easeIn);
```

- Flutter에서 애니메이션 디폴트값은 선형적으로 진행된다,
- `Curved Animation`은 애니메이션의 진행상태를 **비선형 곡선**으로 정의하는데 사용된다.
- `parent`: 애니메이션의 실제 진행상태를 관리하는 `Animation Controller`
- `curve`: 진행률에 적용할 곡선, Flutter의 `Curves`클래스에서 제공

<br/>

- `Curves` 클래스란 Flutter에서 애니메이션 진행률(progress)을 조작할 수 있는 추상클래스이다.
- `Curves` 클래스를 사용하면 진행률을 비선형적으로 바꿀 수 있다.
- `Curves.linear`: 선형으로 진행
- `Curves.easeIn`: 점점 빨라지는 곡선
- `Curves.easeOut`: 점점 느려지는 곡선
- `Curves.elasticIn`: 탄성 효과로 시작
- `Curves.bounceOut`: 튕기는 효과로 종료

<br/>

```dart
class ShakeCurve extends Curve {
  @override
  double transform(double t) => sin(t * pi * 2);
}
```
- `입력값 t`: 애니메이션의 진행률. 범위는 0.0부터 1.0까지
- `sin(...)`: 사인 함수는 -1에서 1 사이의 값을 반환하며 주기적인 진동을 생성
- 입력 값이 0.0에서 1.0까지 변할 때, 사인 함수는 아래와 같은 패턴으로 움직인다: 0.0 → 0 (시작점), 0.25 → 1 (최고점), 0.5 → 0 (다시 원점), 0.75 → -1 (최저점), 1.0 → 0 (다시 원점)

<br/>


### AnimationController

- `Animation Controller`는 Flutter에서 애니메이션의 진행 상태를 제어하는 핵심 클래스이다.
- `Animation Controller`는 애니메이션의 시작, 중지, 방향 전환등을 관리하며 화면의 새 프레임이 준비될 때마다 새로운 값을 생성한다.
- 상태 제어는 아래 4가지 메서드로 관리한다.
- `forward()`: 애니메이션을 시작
- `reverse()`: 애니메이션을 역방향으로 실행
- `stop()`: 애니메이션 중지
- `repreat()`: 애니메이션을 반복

<br/>

```dart
class _LogoAppState extends State<LogoApp> {
class _LogoAppState extends State<LogoApp> with SingleTickerProviderStateMixin {
  late Animation<double> animation;
  late AnimationController controller;

  @override
  void initState() {
    super.initState();
    controller =
        AnimationController(duration: const Duration(seconds: 2), vsync: this);
    animation = Tween<double>(begin: 0, end: 300).animate(controller)
      ..addListener(() {
        setState(() {
          // The state that has changed here is the animation object's value.
        });
      });
    controller.forward();
  }

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Container(
        margin: const EdgeInsets.symmetric(vertical: 10),
        height: 300,
        width: 300,
        height: animation.value,
        width: animation.value,
        child: const FlutterLogo(),
      ),
    );
  }

  @override
  void dispose() {
    controller.dispose();
    super.dispose();
  }
}
```
- 리스터는 아래 두가지 메서드로 한다.
- `addListener()`: 애니메이션 값변경시 호출
- `addStatusListener()`: 애니메이션의 상태 변경(시작, 완료, 역방향 등) 시 호출
- 값이 변경될 때(`addListener()`)마다 `setState()`를 호출시켜 현재프레임을 dirty로 만든뒤 `build()`메서드를 실행시켜 ui를 업데이트함

<br/>

```dart
import 'package:flutter/material.dart';

class MyAnimatedWidget extends StatefulWidget {
  const MyAnimatedWidget({super.key});

  @override
  State<MyAnimatedWidget> createState() => _MyAnimatedWidgetState();
}

class _MyAnimatedWidgetState extends State<MyAnimatedWidget>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;

  @override
  void initState() {
    super.initState();
    // AnimationController 생성 시 vsync 설정
    _controller = AnimationController(
      duration: const Duration(seconds: 2),
      vsync: this, // vsync로 현재 객체 제공
    )..repeat(reverse: true);
  }

  @override
  void dispose() {
    _controller.dispose(); // 리소스 정리
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedBuilder(
      animation: _controller,
      builder: (context, child) {
        return Transform.scale(
          scale: _controller.value,
          child: child,
        );
      },
      child: Container(
        width: 100,
        height: 100,
        color: Colors.blue,
      ),
    );
  }
}

void main() => runApp(const MaterialApp(home: Scaffold(body: MyAnimatedWidget())));
```

- `vsync`는 필수적으로 들어간다. vsync는 애니메이션 프레임 생성이 디스플레이의 화면 새로 고침 빈도와 동기화되도록한다. 만약 화면이 초당 60프레임을 지원하면, 애니메이션은 1초에 최대 60개의 프레임만 생성한다.
- `vsyncs`는 화면에 보이지 않는(off-screen) 애니메이션이 실행 중일 때, 애니메이션이 불필요한 프레임을 생성하지 않도록 방지하기도 한다.
- `vsync`를 지정하지 않으면 Flutter는 모든 프레임을 계속 생성하므로 리소스 낭비가 심해지며 경고가 뜨기도 한다.

<br/>

### Refactoring with Animated Builder

```dart

//  "어떻게" 애니메이션을 구현할지는 AnimationController가 처리
_controller = AnimationController(
  duration: const Duration(seconds: 2), // 애니메이션 시간 정의
  vsync: this,
)..repeat(reverse: true); // 애니메이션 반복


// AnimationController에서 애니메이션 설계를 했다면 AnimatedBuilder는 구체적인 구현을 맡아서 진행한다
return AnimatedBuilder(
  animation: _controller,
  builder: (context, child) {
    // 애니메이션 값(_controller.value)에 따라 빌드 로직 결정
    return Transform.scale(
      scale: _controller.value, // 크기 변경
      child: child,
    );
  },
  child: Container(
    width: 100,
    height: 100,
    color: Colors.blue, // 고정된 UI
  ),
);

```
- `AnimateBuilder`는 Flutter에서 애니메이션을 활용하여 효율적으로 UI를 업데이트하기 위한 위젯이다.
- 애니메이션이 변경될 때마다 "특정위젯"만 다시 빌드하고, `setState()`를 반복적으로 호출할 필요없이 애니메이션과 빌드로직을 분리한다.
- `AnimateBuilder`는 어떻게 위젯이 렌더링되는지 알지못하고 `Animation`객체를 관리하지 않는다. 오직 빌드로직을 맡는다.즉 어떻게 구현할까와 빌드로직을 분리해서 관리하는 것이다.

<br/>

### Simultaneous animations 

```dart
controller = AnimationController(duration: const Duration(seconds: 2), vsync: this);
sizeAnimation = Tween<double>(begin: 0, end: 300).animate(controller);
opacityAnimation = Tween<double>(begin: 0.1, end: 1).animate(controller);

class AnimatedLogo extends AnimatedWidget {
  const AnimatedLogo({super.key, required Animation<double> animation})
      : super(listenable: animation);

  // Make the Tweens static because they don't change.
  static final _opacityTween = Tween<double>(begin: 0.1, end: 1);
  static final _sizeTween = Tween<double>(begin: 0, end: 300);

  @override
  Widget build(BuildContext context) {
    final animation = listenable as Animation<double>;
    return Center(
      child: Opacity(
        opacity: _opacityTween.evaluate(animation),
        child: Container(
          margin: const EdgeInsets.symmetric(vertical: 10),
          height: _sizeTween.evaluate(animation),
          width: _sizeTween.evaluate(animation),
          child: const FlutterLogo(),
        ),
      ),
    );
  }
}
```
- Flutter에서 동시 애니메이션을 구현하는 방법은 다음과 같다:

1. 하나의 `AnimationController`로 여러 애니메이션 관리:
- 여러 Tween을 사용하여 동일한 AnimationController로 값을 계산.
- 예: sizeAnimation, opacityAnimation을 같은 controller에서 생성.

2. 다중 `AnimationController` 사용:
- 서로 다른 AnimationController를 사용해 독립적인 애니메이션 진행.
- 예: 위치와 회전 애니메이션을 각각 다른 속도로 실행.
