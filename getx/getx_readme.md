공식문서 링크 <br/>
[https://github.com/jonataslaw/getx/blob/master/README.md](https://github.com/jonataslaw/getx/blob/master/README.md)

<br/>
<br/>

## The Three pillars

### State management

- `Get`은 두개의 상태관리 매니저를 가지고 있다 -> `GetBuilder`, `Obx`

<br/>

```dart
var name = 'Jonatas Borges';
var name = 'Jonatas Borges'.obs;
Obx(() => Text("${controller.name}"));
```

- Getx를 사용하면 반응형 프로그래밍을 위와 같이 손쉽게 구현할 수 있다.
- 반응형으로 다룰 변수에다 `.obs`를 통해 옵저버블을 안들어주고 구독한다.


<br/>

### Route management

```dart
GetMaterialApp( // Before: MaterialApp(
  home: MyHome(),
)

Get.to(NextScreen());
Get.toNamed('/details');
```
- `GetMaterialApp`으로 선언하게 되면 위와같이 화면이동을 할 수 있다.
- 화면이동에는 두가지 버전이 있는데 직접적인 호출과 키패스가 있다. (키패스가 더 느슨한 연결에 적합하다고 생각한다)

<br/>

```dart
Get.back();
Get.off(NextScreen());
Get.offAll(NextScreen());
```
- 뒤로가기 종류에는 크게 3가지가 있다.
- 첫번재는 이전화면으로 돌아가기 옵션이 없을경우에 사용한다. (로그인, 스플래쉬 뷰 etc)
- 두번째는 작업했던 이전 뷰로 가기 (쇼핑 카트, 투표, 테스트 etc)
- 첫번째와 두번째의 가장 큰 차이는 이전뷰로 갈때 데이터를 리셋할거냐, 아님 유지할거냐이다.
- 세번째는 네비게이션에 있는 스택을 모두 제거하는 메서드이다. 예를들어 로그아웃을 누르면 전부날리고 첫화면으로 돌아가는 경우처럼.

<br/>

### Dependency management

```dart
Controller controller = Get.put(Controller()); // Rather Controller controller = Controller();
final Controller controller = Get.find();
```
- Get 패키지는 의존성 관리도 해준다..! (종합 선물 상자 ㄷ)
- `Get.put()`을 통해 객체를 등록하고 `Get.find<Controller>()` 를 통해 등록한 객체를 가져온다. 일종의 Service Locator 라고 볼 수 있다.
- 이때 다른 상태관리 패키지를 사용하고 있더라도 GetX의 의존성 관리 툴이 적용가능하다고 한다.



<br/>
<br/>

## Utils

#### Internalization

```dart
import 'package:get/get.dart';

class Messages extends Translations {
  @override
  Map<String, Map<String, String>> get keys => {
        'en_US': {
          'hello': 'Hello World',
        },
        'de_DE': {
          'hello': 'Hallo Welt',
        }
      };
}
```
- 이럴수가 getx는 지역화 기능까지 제공해주고 있다.
- 

<br/>

#### Change Theme

```dart
Get.changeTheme(ThemeData.light());
Get.changeTheme(Get.isDarkMode? ThemeData.light(): ThemeData.dark());
```
- `GetMaterialApp` 보다 상위에 위젯을 두고 사용하지 말아야한다. 키가 중복될 수 있다.
- 첫번째처럼 전역적인 테마 변경, 두번째처럼 특정 위젯의 테마 변경 모두 구현 가능하다.

<br/>

#### GetPage Middleware 
```dart
final middlewares = [
  GetMiddleware(priority: 2),
  GetMiddleware(priority: 5),
  GetMiddleware(priority: 4),
  GetMiddleware(priority: -8),
];
```
- Get에서는 미들웨어 메서드 기능도 가지고 있다.
- 미들웨어는 일종의 검문소역할을 하는, 소프트웨어 전반적으로 쓰이는 용어이다.

<br/>

```dart
RouteSettings redirect(String route) {
  final authService = Get.find<AuthService>();
  return authService.authed.value ? null : RouteSettings(name: '/login')
}
```
- redirect 함수는 GetMiddleware에서 특정 조건(예: 인증 상태)에 따라 페이지 접근을 제어하고 필요한 경우 리다이렉트할 수 있는 도구이다.

<br/>

```dart
GetPage onPageCalled(GetPage page) {
  final authService = Get.find<AuthService>();
  return page.copyWith(title: 'Welcome ${authService.UserName}');
}
```
- `onPageCalled`는 GetX 패키지에서 페이지가 호출될 때 특정 작업을 수행하거나 페이지의 속성을 변경할 수 있도록 하는 기능이다.


<br/>

```dart
List<Bindings> onBindingsStart(List<Bindings> bindings) {
  final authService = Get.find<AuthService>();
  if (authService.isAdmin) {
    bindings.add(AdminBinding());
  }
  return bindings;
}
```
- `onBindingsStart` 함수는 페이지가 호출되기 전에 바인딩을 초기화하기 직전에 실행된다. 
- 앞선 메서드와 차이점은 `onBindingsStart`는 페이지가 호출되기 전에 필요한 의존성을 동적으로 주입하는 단계이며, `onPageCalled`는 페이지가 호출될 때 페이지의 속성을 동적으로 변경하는 단계이다. (큰걸 주입할지, 작은걸 주입할지)
<br/>

```dart
GetPageBuilder onPageBuildStart(GetPageBuilder page) {
  print('bindings are ready');
  return page;
}
```
- `onPageBuildStart` 함수는 바인딩이 초기화된 직후, 페이지 위젯이 생성되기 전에 호출된다.

<br/>

getx 미들웨어 기능 실행순서 정리
```
+---------------------------------------+
|                Start                  |
+---------------------------------------+
                   |
                   v
+---------------------------------------+
|             redirect                  |
|   (라우트를 찾을 때 실행, 리다이렉트 결정)     |
+---------------------------------------+
                   |
                   v
+---------------------------------------+
|            onPageCalled               |
|  (페이지가 호출될 때 실행, 페이지 속성 변경)    |
+---------------------------------------+
                   |
                   v
+---------------------------------------+
|           onBindingsStart             |
| (바인딩 초기화 직전에 실행, 바인딩 변경)       |
+---------------------------------------+
                   |
                   v
+---------------------------------------+
|           onPageBuildStart            |
| (바인딩 초기화 후, 페이지 생성 전 실행)       |
+---------------------------------------+
                   |
                   v
+---------------------------------------+
|            onPageBuilt                |
| (페이지 생성 후, 페이지 위젯 반환 시 실행)     |
+---------------------------------------+
                   |
                   v
+---------------------------------------+
|            onPageDispose              |
|   (페이지와 관련된 객체 해제 시 실행)         |
+---------------------------------------+
```


<br/>
<br/>

## Why Getx?
