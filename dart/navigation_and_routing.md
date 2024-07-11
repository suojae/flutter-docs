공식문서 링크<br/>
[https://docs.flutter.dev/ui/navigation](https://docs.flutter.dev/ui/navigation)

<br/>


## Navigation and routing


<br/>

- 간단한 화면 전환은 `Navigator`, 딥 링크기능을 쓰거나 복잡한 화면전환이 있다면 `Router`를 쓰는 것이 좋다.

<br/>

### Using the Navigator

<br/>

```dart
onPressed: () {
  Navigator.of(context).push(
    MaterialPageRoute(
      builder: (context) => const SongScreen(song: song),
    ),
  );
},
child: Text(song.name),
```

- `navigator` 위젯은 `context`를 받아 스택자료구조로 화면이동을 한다.
- 이때 스택을 `route`객체로 쌓기 때문에 `push()` 메서드는 위 코드처럼 `route` 객체를 받게된다.
- ` MaterialPageRoute` 객체는 `Route` 서브클래싱된 객체로 화면이동시 안드로이드처럼 아래로 내려가는 화면이동 애니메이션이 구현되어있다.

<br/>

### Using named routes

```dart
@override
Widget build(BuildContext context) {
  return MaterialApp(
    routes: {
      '/': (context) => HomeScreen(),
      '/details': (context) => DetailScreen(),
    },
  );
}
```
- named routes를 쓰면 위와같이 하위 위젯에서 스트링값을 통해 화면 이동을 할 수 있다.
- 이를 통해 각 화면 담당 객체는 서로를 바라보지않고도 스트링값으로 화면이동을 가능하게끔 구현할 수 있다.
- 하지만 네임드 라우터가 딥링크등에서 문제를 보이고 있기 때문에 공식문서에서는 비추천하고 있다.

<br/>

### Using the Router
```dart
void main() {
  final GoRouter router = GoRouter(
    routes: <GoRoute>[
      GoRoute(
        path: '/',
        builder: (BuildContext context, GoRouterState state) {
          return HomeScreen();
        },
      ),
      GoRoute(
        path: '/details',
        builder: (BuildContext context, GoRouterState state) {
          return DetailsScreen();
        },
      ),
    ],
  );

  runApp(MyApp(router: router));
}
```
- 웹에서 사용자가 특정 화면으로 직접 링크를 통해 접근할 수 있어야 하거나, 하나의 앱 내에서 여러 Navigator 위젯을 사용하여 복잡한 네비게이션 구조를 구성해야 하는 경우 공식문서에서는 `go_router` 패키지를 이용할 것을 추천하고 있다.

<br/>

### Using Router and Navigator together

- `Router`와 `Navigator`는 함께 사용할 수 있다.
- 페이지 기반 라우트(`router`)는 딥 링크가 가능하다. 즉, 사용자가 특정 URL을 통해 직접 해당 페이지로 이동할 수 있다. 
- 페이지 없는 라우트(`Navigator`)는 딥 링크가 불가능하다.

```
페이지 추가:

    페이지 A (페이지 기반 라우트)
    페이지 B (페이지 없는 라우트) - Navigator.push로 추가됨
    페이지 C (페이지 없는 라우트) - Navigator.push로 추가됨
    페이지 D (페이지 기반 라우트)

페이지 A 제거:

    페이지 A가 제거되면, 그 뒤에 있는 페이지 B와 페이지 C도 함께 제거됩니다. 페이지 D는 남아있습니다.
```

- `Router`로 페이지를 이동하고 이전 페이지를 제거하면 여전히 현재 페이지는 남아있는 반면 `Navigator`로 페이지를 이동뒤 이전 페이지를 제거하면 현재페이지도 제거된다는 특징이 있다.