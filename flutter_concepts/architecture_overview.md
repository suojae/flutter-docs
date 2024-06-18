공식문서 링크<br/> 
[https://docs.flutter.dev/resources/architectural-overview](https://docs.flutter.dev/resources/architectural-overview)

<br/>

## 플러터 아키텍쳐

<br/>

### Architectural Layers

<img width="400" alt="image" src="https://github.com/suojae3/flutter_docs_study/assets/126137760/77ce1d39-45c7-4d93-9303-34ce202c4aaa">

<br/>
<br/>

#### Embbeder
🔹 Embedder가 수행하는 역할은 크게 앱의 진입점, 플랫폼별 운영체제와 상호작용, 플러터 애플리케이션 이벤트 루프 처리 3가지가 있다.<br/>
🔹 진입점 역할: 각 플랫폼별 언어로 작성되어 특정 플랫폼에서 실행될 수 있도록 한다.

<br/>

#### Engine
🔹 엔진은 프레임을 **rasterize**하는 역할을 수행한다. 여기서 rasterize란 벡터 형식의 그래픽 데이터를 픽셀 단위로 변환하여 디스플레이 장치에 표시할 수 있도록 만드는 과정이다.<br/>
🔹 엔진은 `dart:ui`(C++코드를 Dart로 포장) 를 통해 플러터 프레임워크에 노출되어 있다. 따라서 엔진을 이용한 세부작업이 필요하면 `dart:ui`를 이용하자<br/>

<br/>

#### Framework
🔹 Foundation: Flutter 프레임워크의 다른 모든 계층에서 사용되는 가장 기본적인 유틸리티 클래스와 함수들로 구성<br/>
🔹 Animation/Painting/Gestures: <br/>
🔹 Rendering: <br/>
🔹 Widgets: <br/>
🔹 Material/Cupertino: 


<br/>

#

### Anatomy of an App

<img width="357" alt="image" src="https://github.com/suojae3/flutter_docs_study/assets/126137760/bdd7caca-3799-47f5-9066-1a35fe303078"><br/>











