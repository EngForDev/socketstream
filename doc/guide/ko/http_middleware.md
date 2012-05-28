# HTTP Middleware

#### Warning: Incomplete. More to follow
#### 경고: 미완료. 더 수행해야 함.

SocketStream provides a stack of Connect HTTP middleware which is used internally to serve single-page clients, asset files and static files (e.g. images) in `client/static`.  
SocketStream 은 싱글-페이지 클라이언트에게 `client/static` 안에 asset 파일들 과 static 파일 (예 이미지들) 서비스하기 위한 내부적으로 사용되는 Connect HTTP  미들웨어 스택을 제공합니다.

The Connect `app` instance is accessible directly via the `ss.http.middleware` variable within `app.js`.  
그 Connect `app` 인스턴스는 `app.js`안에 `ss.http.middleware`변수를 통해 직접적으로 접근하는 것이 가능합니다.

SocketStream allows you 
to prepend new middleware to the top of the stack (to be processed BEFORE SocketStream middleware) using:
SocketStream는 새로운 middleware가 스택의 꼭대기에 첨부 될수 있는 (SocketStream middleware 전에 처리하는) 사용을 허용한다: 

    ss.http.middleware.prepend()

For example you could add:
예를 들어 추가 할 수 있음:

    ss.http.middleware.prepend( ss.http.connect.bodyParser() );

Because SocketStream adds `connect.cookieParser()` and `connect.session()` to the stack, if the middleware you're wanting to use requires sessions support (i.e. access to `req.session`) it will need to be appended to the bottom of the stack AFTER SocketStream middleware has been loaded as so:  
만약 당신이 사용하고자 하는 미들웨어가 세션들의 지원을 요청하는 경우(즉, `req.session`로 접근), 왜냐하면 SocketStream은 `connect.cookieParser()` 과 `connect.session()`을 스택에 추가하기 때문에 로드된 SocketStream 미들웨어 후에 스택의 바닥에 삽입해야 할 것이다.  

    ss.http.middleware.append( everyauth.middleware() );

Apart from determining where the middleware should be added, the `prepend()` and `append()` functions work in exactly the same way as `connect.use()`.  
미들웨어와 추가해야 하는 곳의 결정과는 별개로, `prepend()`과  `append()`함수 들은 정확히 `connect.use()` 와 같은 방법으로 동작합니다.
