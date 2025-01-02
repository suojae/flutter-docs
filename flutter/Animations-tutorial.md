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




