# React Authentication

## 1. Authentication(인증)

### 사용자 인증

웹 서비스를 운영하다보면 반드시 **인증**이 필요한 리소스가 있다.

또한 사용자 별로 가지고 있는 정보가 다르기 때문에 사용자를 구분할 수 있어야 한다.

일반적으로 인증은 2가지 절차를 거친다.

1. 사용자가 접근 허가를 받는다.

    사용자가 계정을 생성하고 로그인 시에 자격 증명을 제공하면 데이터가 백엔드 서버로 전송된다.

    서버에서는 데이터베이스를 살펴 사용자의 데이터(아이디, 비밀번호 등)를 확인한다.

    데이터의 유효성이 검증되면 서버는 접근을 허가한다.

2. 보호된 서비스를 요청한다.

    허가 정보를 첨부해 API 엔드포인트의 다른 보호된 리소스에 후속 요청을 보낸다.

### Authentication 동작 방식

그렇다면 클라이언트와 서버는 어떤 방식으로 인증 단계를 걸칠까?

우선 클라이언트는 데이터를 요청할 때마다 Header에 계정 정보를 담지 않는다. (보안에 매우 취약함)

또한 서버는 악성 유저로부터 위조되기 쉬운 'Yes' / 'No'와 같은 단순한 방식으로는 접근 허가를 알릴 수 없다.

따라서 클라이언트와 서버는 더 정교한 방식으로 자격 증명을 주고 받는다.

- **Server Side Session**

    사용자 접근을 허가한 백엔드 서버는 특정 사용자의 고유한 ID를 생성하고 세션 저장소에 저장한다.

    그 후 이와 연결되는 세션ID를 발행해 클라이언트에 전송하고 클라이언트는 이를 쿠키에 저장한다.

    이후 후속 요청을 보낼 때마다 클라이언트는 세션ID를 첨부하고, 서버는 세션ID를 이용해 인증된 사용자인지 확인한 뒤 대응되는 정보를 응답한다.

    하지만 이러한 **stateful** 형태는 서버의 증설과 같은 경우 한계를 가지는데, 이는 **stateless** 형태의 **Authentication Token**으로 해결할 수 있다.

    > 참고: **세션과 쿠키**
    >
    > 세션: 서버에서 가지고 있는 정보이다.
    >
    > 쿠키: 사용자에게 발급된 세션을 열기 위한 열쇠(session ID)를 담고 있다.
    >
    > 쿠키만으로 인증을 한다는 것은 서버의 자원을 사용하지 않는다는 것이며, 이는 클라이언트가 인증 정보를 책임진다는 뜻이다.
    >
    > 이는 HTTP 요청을 탈취당할 위험이 있기 때문에, 결과적으로 보안이 더 강력한 서버 측에 인증 책임을 지게 하기 위해 세션을 사용한다.

    > 참고: **stateful vs stateless**
    >
    > stateful: server side에 상태 정보를 저장하는 형태이다. 세션 상태에 기반하여 서버의 응답이 달라진다.
    >
    > stateless: server side에 상태 정보를 저장하지 않는 형태이다. 서버가 클라이언트 정보를 저장하지 않으며, 서버의 응답이 클라이언트와의 세션 상태와 독립적이다.
    >
    > 만약 서버에 장애가 생기면 다른 서버를 사용해야 하는데, stateful의 경우 기존 서버의 유저 정보가 함께 소실되기 때문에 클라이언트는 유저 정보를 받을 수 없다.
    >
    > 하지만 stateless는 서버에 상태를 보관하지 않기 때문에 다른 서버를 사용해도 유저 정보는 유지된다.
    >
    > 즉 stateless는 스케일 아웃에는 유리하지만, 클라이언트의 요청에 상대적으로 더 많은 데이터가 소모된다는 단점이 있다.

- **Authentication Token(JWT: Json Web Token)**
    
    사용자 자격 정보로 데이터베이스의 정보과 비교해 유효성 검사를 하는 것은 세션 기반 방식과 동일하다.

    하지만 세션ID를 저장 및 발급하지 않고 허가 토큰을 발행하는데, 토큰에는 사용자의 데이터가 **서버만의 key로  인코딩**된 긴 문자열이 담겨져 있다.

    토큰은 서버의 특정 알고리즘에 따라 서버만 아는 키로 해싱되므로 클라이언트는 토큰 정보를 읽을 수 없다.

    단지 후속 요청에 해당 토큰을 담기만 하면, 서버는 자신의 key를 이용해 직접 생성한 토큰인지 확인할 수 있다.

    <img src="https://user-images.githubusercontent.com/109272360/207913581-344dacbb-6ca2-4a77-8837-3936a7207869.png" width="900px">
  

