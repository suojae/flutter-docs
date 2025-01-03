## Fetch data from the internet

<br/>

### Add the HTTP Package


- `http` 패키지를 사용하면 인터넷으로부터 심플하게 데이터를 가져올 수 있다.
1. HTTP 패키지를 추가한다.

```xml
<!-- Required to fetch data from the internet. -->
<uses-permission android:name="android.permission.INTERNET" />
```
2. 안드로이드는 `AndroidManifest.xml` 파일에 internet perssion을 추가한다.
```xml
<!-- Required to fetch data from the internet. -->
<key>com.apple.security.network.client</key>
<true/>
``` 
4. macOS의 경우 info.plist 파일을 수정한다.

<br/>

### Make a network request

```
Future<http.Response> fetchAlbum() {
  return http.get(Uri.parse('https://jsonplaceholder.typicode.com/albums/1'));
}
```

- `http.get()`: http 메서드를 통해 GET 요청을 보낸다. 요청은 비동기로 처리되고 응답은 Future<http.Response> 로 온다.

<br/>

```dart
import 'package:http/http.dart' as http;

Future<void> fetchData() async {
  final url = Uri.parse('https://jsonplaceholder.typicode.com/albums/1');
  
  // HTTP GET 요청
  final response = await http.get(url);

  // 응답 처리
  print("Status Code: ${response.statusCode}");
  print("Reason Phrase: ${response.reasonPhrase}");
  print("Headers: ${response.headers}");
  print("Body: ${response.body}");
}
```

- `http.response`는 크게 4가지 속성으로 이루어진다
- `statusCode`: http응답코드로 타입은 **`int`**, 200,400,500같은 응답코드가 날라온다
- `headers`: 응답헤더를 포함하는 키-값 쌍으로 타입은 **`Map<String, String>`**으로 된다.
- `reasonPhrase`: 타입은 **`String?`** 이고 상태 코드와 함께 제공되는 이유 구문이 들어간다 ("OK", "Not Found" 같은)
- `isRedirect`: 타입은 **`bool`** 이고 요청이 리다이렉션되었는지 여부를 알려준다.
- `persistentConnection`: 타입은 **`bool`** 이고 서버와 연결이 지속적인지 여부를 나타낸다.
 
<br/>

```json
{
  "userId": 1,
  "id": 1,
  "title": "quidem molestiae enim"
}
```

```dart
final responseBody = response.body;
print(responseBody);

// 출력값: {"userId":1,"id":1,"title":"quidem molestiae enim"}
```

- `body`: HTTP 응답 본문을 나타낸다. 타입은 **String**이다. 타입은 문자열이더라도 JSON형식이기 때문에 파싱이 필요하다. 파싱을 하게되면 `Map<String, dynamic>`으로 된다.

<br/>

```dart
import 'package:http/http.dart' as http;

void main() async {
  final url = Uri.parse('https://jsonplaceholder.typicode.com/albums/1');
  final response = await http.get(url);

  // 전체 헤더 출력
  print("All Headers:");
  response.headers.forEach((key, value) {
    print("$key: $value");
  });

  // 특정 헤더 출력
  print("\nContent-Type: ${response.headers['content-type']}");
  print("Server: ${response.headers['server']}");
  print("Date: ${response.headers['date']}");
}

/*
All Headers:
content-type: application/json; charset=utf-8
date: Fri, 03 Jan 2025 12:00:00 GMT
server: cloudflare
content-length: 83
cache-control: max-age=14400

Content-Type: application/json; charset=utf-8
Server: cloudflare
Date: Fri, 03 Jan 2025 12:00:00 GMT
*/
```

- `Header`에 대해 좀더 자세하게 알아보자
- `content-type`: 응답 본문의 데이터 유형을 나타낸다 (application/json, text/html, text/plain, image/png, etc)
- `content-length`: 응답 본문 바이트 길이를 나타낸다.
- `server`: 응답을 생성한 서버의 소프트웨어 정보를 나타낸다.
- `cache-control`: 캐싱정책을 정의한다. (no-cache, max-age=3600, etc)
- `date`: 응답이 생성된 날짜 및 시간을 나타낸다.

<br/>

### Convert the response into a custom Dart object

```dart
class Album {
  final int userId;
  final int id;
  final String title;

  const Album({
    required this.userId,
    required this.id,
    required this.title,
  });

  factory Album.fromJson(Map<String, dynamic> json) {
    return switch (json) {
      {
        'userId': int userId,
        'id': int id,
        'title': String title,
      } =>
        Album(
          userId: userId,
          id: id,
          title: title,
        ),
      _ => throw const FormatException('Failed to load album.'),
    };
  }
}

// 패턴매칭을 안쓰면 아래와 같이 맵핑해야한다..
factory Album.fromJson(Map<String, dynamic> json) {
  if (json.containsKey('userId') &&
      json.containsKey('id') &&
      json.containsKey('title')) {
    return Album(
      userId: json['userId'] as int,
      id: json['id'] as int,
      title: json['title'] as String,
    );
  } else {
    throw const FormatException('Failed to load album.');
  }
}
```

- 패턴 매칭(switch)을 사용해 JSON 데이터를 검증하고 리턴한다
- 패턴 매칭의 장점은 JSON의 구조를 더 안전하게 검증할 수 있다는 것!
- 이때 `fromJson()` 은 팩토리 메서드이며 `dart:convert` 패키지를 사용하게 된다.

<br/>

### Fetch the data

- 데이터를 가져올 때는 `initState()` 나 `didChangeDependencies()` 메서드를 호출될 때 불러온다.
- `initState()`: 위젯 생성시 한 번만 호출되어 초기 데이터 가져오는데 적합 
- `didChangeDependencies()`: 상위 위젯(inheritedWidget)이 변경될 때 호출된다.
- 특히 `build()` 메서드 호출 시점에 데이터 가져오는 것만은 하면 안된다. `build()`는 UI를 다시 그릴 때마다 호출되기 때문에 API를 반복 호출할 수도 있기 때문이다.


| **상황**                         | **`initState`**                              | **`didChangeDependencies`**                   |
|-----------------------------------|---------------------------------------------|-----------------------------------------------|
| **고정된 초기 데이터 로드**        | ✅ 초기 상태 설정                             | ❌ 불필요                                      |
| **상위 데이터 변경 감지**          | ❌                                            | ✅ 상위 `InheritedWidget` 변경 대응            |
| **다국어(언어) 변경**             | ❌ 초기 상태만 설정                          | ✅ 언어 변경 시 번역 데이터 다시 로드           |
| **테마 변경**                     | ❌ 앱 시작 시 초기 설정                      | ✅ 다크 모드/라이트 모드 변경 대응              |
| **네트워크 상태 변경**             | ❌ 초기 연결 상태 확인                       | ✅ 네트워크 연결 상태 변화에 대응               |
| **앱 방향 변경**                  | ❌ 무반응                                    | ✅ 가로/세로 전환 시 데이터 새로 로드           |
| **사용자 고유 데이터 로드**        | ✅ 사용자 정보 초기 로드                     | ❌ 불필요                                      |
| **컨텍스트 의존 데이터**           | ❌                                            | ✅ 컨텍스트(예: `Locale`, `MediaQuery`) 변경 대응 |
