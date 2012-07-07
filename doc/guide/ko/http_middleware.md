<!--# HTTP Middleware-->
# HTTP 미들웨어

<!--#### Warning: Incomplete. More to follow-->
#### 주의: 미완성입니다. 계속 적을꺼에요.

<!--SocketStream provides a stack of Connect HTTP middleware which is used internally to serve single-page clients, asset files and static files (e.g. images) in `client/static`.-->
소켓스트림은 커넥트 HTTP 미들웨어 스택을 재공합니다. 보통 한 페이지로 구성된 클라이언트의 asset 파일과 `client/static`안의 정적 파일(예를 들면 이미지)을 내부적으로 서빙하기 위해 사용합니다.

<!--The Connect `app` instance is accessible directly via the `ss.http.middleware` variable within `app.js`.-->
커낵트 `app` 인스턴스는 `app.js`파일안의 `ss.http.middleware`변수를 통해 직접 억세스가 가능합니다.

<!--SocketStream allows you to prepend new middleware to the top of the stack (to be processed BEFORE SocketStream middleware) using:-->
소켓스트림은 새로운 미들웨어를 스택의 가장 앞(소켓스트림의 미들워어보다 **먼저** 수행되도록)에 추가할수 있도록 합니다.

    ss.http.middleware.prepend()

<!--For example you could add:-->
예를 들자면 이렇게 할수 있습니다:

    ss.http.middleware.prepend( ss.http.connect.bodyParser() );

<!--Because SocketStream adds `connect.cookieParser()` and `connect.session()` to the stack, if the middleware you're wanting to use requires sessions support (i.e. access to `req.session`) it will need to be appended to the bottom of the stack AFTER SocketStream middleware has been loaded as so:-->
소켓스트림은 `connect.cookieParser()`와 `connect.session()`를 스택에 추가해두었기 때문에, 미들웨어에서 세션 지원을 원한다면(예를 들면 `req.session`에 접근 해야한다던가) 소켓스트림의 미들웨어보다 **나중에** 수행되도록 스택의 밑에 추가할 필요가 있습니다:

    ss.http.middleware.append( everyauth.middleware() );

<!--Apart from determining where the middleware should be added, the `prepend()` and `append()` functions work in exactly the same way as `connect.use()`.-->
이외에도 미들웨어가 추가되어야한다면, `prepend()` 랑 `append()` 함수들은 `connect.use()`와 같은 방식으로 동작하게 됩니다.
