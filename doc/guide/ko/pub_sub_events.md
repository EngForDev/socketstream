# Pub/Sub Events

SocketStream includes an powerful pub/sub system allowing you to easily send messages to connected browsers over the websocket.
SocketStream 은 연결된 웹브라우저에게 웹소켓으로 쉽게 메세지를 전송 할 수 있는 강력한 pub/sub시스템을 가지고 있습니다. 

### 1. Sending to Everyone
### 1. 모두에게 보내기

To send an event to every connected client (for example to let them know the server's going down for maintenance), use the `ss.publish.all` method:  
연결된 모든 브라우저에게 이벤트를 보내기 위해(예를 들어 서버가 유지보수를 위해 다운시켜야 함을 알리기 위해), `ss.publish.all`를 사용 함:

``` javascript
// in a /server/rpc file
ss.publish.all('flash', 'Notice: This service is going down in 10 minutes');
```
<!-- ss.publish.all('flash', '알림 : 10분 내로 이 서비스를 중지합니다.'); -->

Receive the event in the browser with:  
브라우저에서 이벤트 받기:

``` javascript
// in a /client/code file
ss.event.on('flash', function(message){
  alert(message);
});
```

Note: The first argument specifies the event name. All subsequent arguments will be passed through to the event handler.  
노트: 첫번째 인자는 이벤트 이름으로 지정한다. 뒤이은 모든 인자들은 이벤트 핸들러에게 전달 되어질 것이다.


### 2. Sending to Private Channels
### 2. 프라이빗 채널로 전송하기
    
Sometimes you'll want to send events to a subset of connected clients. For example, you may have a chat app with multiple rooms.  
가끔 접속된 일부 클라이언트에게만 이벤트를 전송하고 싶을 것입니다. 예를 들어, 여러개의 방에서 채팅하는 앱

Each client session can be subscribed to an unlimited number of private channels using the following commands:
다음에 명령을 사용하여 각각의 클라이언트 세션은 무제한으로 프라이빗 채널이 구독될 수 있습니다.(열려 질수 있습니다. 참조될 수 있습니다.)

``` javascript
// in a /server/rpc file after calling req.use('session') middleware

req.session.channel.subscribe('disney')   // note: multiple channel names can be passed as an array 
    
req.session.channel.unsubscribe('kids')   // note: multiple channel names can be passed as an array

req.session.channel.reset()               // unsubscribes the session from every channel
    
req.session.channel.list()                // shows which channels the session is currently subscribed to
```
<!-- // /server/rpc 파일에서 req.use('session') 미들웨어를 호출 후에 -->


Sending a message to a private channel is similar to broadcasting to all; however, note the first argument now specifies the channel name (or channels if you pass an array):  
프라이빗 채널로 메세지를 전송 하는 것은 모두에게 브로드캐스팅(전송)하는 것과 비슷함; 하지만, 첫번째 인자에 채널(또는 채널들 채널 배열로 기록)이름을 기록 함:  

``` javascript
// in a /server/rpc file
ss.publish.channel('disney', 'chatMessage', {from: 'jerry', message: 'Has anyone seen Tom?'});
```

Receive these events in the browser in the usual way. Note: If the event was sent to a channel, the channel name is passed as the final argument to the event handler:  
이일반 적인 방법으로 브라우저에서 이러한 이벤트를 받음. 노트: 채널로 이벤트가 보내진다면, 채널이름은 마지막 인자로 이벤트 핸들러에 전달 됨 :  


``` javascript
// in a /client/code file
ss.event.on('chatMessage', function(msg, channelName){
  console.log('The following message was sent to the ' + channelName + ' channel:', msg);
});
```
<!-- console.log('다음은 받은 메세지 ' + channelName + ' channel:', msg); -->

### 3. Sending to Users
### 3. 사용자들에게 보내기

Once a user has been [authenticated](https://github.com/socketstream/socketstream/blob/master/doc/guide/en/authentication.md) (which basically means their session now includes a value for `req.session.userId`), you can message the user directly by passing the `userId` (or an array of IDs) to the first argument of `ss.publish.user` as so:
한번이라도 인증이 되어지면(기본적으로 사용자의 현재 세션은 `req.session.userId`의 값을 포함 하는 것을 의미 함), `ss.publish.user`의 첫번째 인자에 `userId`(또는 ID들의 배열)을 (삽입하여) 사용자에게 직접 메세지를 보낼 수 있습니다.   


``` javascript
// in a /server/rpc file
ss.publish.user('fred', 'specialOffer', 'Here is a special offer just for you!');
```
<!-- ss.publish.user('가카', '빅엿', '여기 당신을 위한 빅엿이 있습니다.'); -->
<!-- 취향에 맞게 바꾸셈.. -->

### 4. Sending to Individual Clients (browser tabs)
### 4. 개체 각각의 클라이언트에게 전송하기 (브라우저 탭들)

If a user opens multiple tabs in the browser, each will share the same `req.session.id` and private channel subscriptions (as channels are attached to sessions).  
만약 사용자가 브라우저로 복수의 탭을 열은 경우, 각각은 `req.session.id` 과 프라이빗 채널 구독(참조) 를 공유합니다.(채널들이 세션에 첨부된 되어 있으므로)


Normally this is the desired behavior, but in rare cases you'll want to message a particular client (i.e. a individual browser tab):  
일반적으로 이것은 올바른 동작이지만, 드문 경우지만 개개의 클라이언트에게 메세지를 보내길 원할 수 있습니다. (예 각각의 브라우저 탭):  
<!-- 의미상 Individual 을 각각의 로 표현  == particular : 개개의 -->

``` javascript
// in a /server/rpc file
ss.publish.socketId('254987654324567', 'justForMe', 'Just for one tab');
```
<!-- // /server/rpc 파일 에서 -->

You can find the socketId by calling `req.socketId` in your server-side code. Note, this attribute may not always be present (e.g. if you invoke the RPC method via `ss-console`), so plan accordingly.  
당신은 서버측 코드에서 `req.socketId` 호출 하여 socketId를 찾을 수 있습니다. 노트: 이 속성은 항상 존재하지 않을 수 있으므로 적절히 계획하시오.(예: `ss-console`으로  RPC메소드 호출)

Please be aware that `req.socketId` will change if you refresh the page, so it's much better to assign clients to private channels and use those wherever possible.  
페이지가 새로고침 되면 `req.socketId`는 변경될 걸 이라는 걸 인식했으면 하며, 그래서 가능한 모든때에 클라이언트들에게 프라이빗 채널들을 할당하고 그것울 이 용하는 것이 훨씬 낫다.


### Publishing Events via app.js
### app.js를 통해 이벤트 발행하기

Sometimes you'll want to publish events from outside your `/server/rpc` files, such as your `app.js` file. The entire `ss` API available to actions in `/server/rpc` is also available in `app.js` via the `ss.api` object. Hence to publish an event to `all` you would call:
떄떄로 `app.js`처럼 /server/rpc`파일 외부에서 이벤트들을 발생하길 원할 수 있을 것이다. /server/rpc`에서 action들에  사용할 수 있는 전체의 `ss` API는 `ss.api`의 객체를 통해 `app.js`에서 또한 이용 할 수 있다. 따라서 모두에게 이벤트를 게시하기 위해 당신은 호출 해야 함:  

``` javascript
// in /app.js
ss.api.publish.all()
```


### Event Transports
### 이벤트 수송


By default SocketStream sends all published events over an internal event emitter. This works fine during development and under moderate load, however; once your app outgrows a single process, you'll need to switch to an external event transport to allow you to run multiple SocketStream processes/servers at once.  
모든 게시된 이벤트들은 내부의 이벤트 emitter(게시자)를 통해 기본적인 SocketStream으로 보내집니다. 이것은 개발단계 와 약간의 부하에서도 잘 동작하지만; 일단 당신의 앱이 싱글 프로세스를 벗어나게 되면, 당신은 한번에 여러 프로세스들/서버들이 한번에 실행할 수 있도록 외부의 이벤트를 수송하도록 전환할 필요가 있을 것입니다. 

Event transports are implemented in a modular way, so it's possible to write a simple driver to support any Message Queue.  
이벤트 수송은 모듈방식으로 구현될 수 있어서 어떤 메세지 큐를든지 지원 할 수 있는 간단한 드라이버 형태로 작성될 수 있습니다.

To use the in-built Redis transport (recommended), add the following line to your `app.js` file:  
(추천하는)내장된 Redis 수송을 사용하기 위해, 다음 라인을 당신의 `app.js`추가:  

``` javascript
// in app.js
ss.publish.transport.use('redis');  // any config can be passed to the second argument
```
<!-- ss.publish.transport.use('redis');  // 모든 설정은 두번쨰 인자로 전달됩니다. -->

Using an external event transport has an important added benefit: Now you can easily push events to connected browser from external (non Node.js) systems. Simply connect the external system (e.g. an Erlang service) to the same instance of Redis and publish messages using the same JSON message format.  
외부 이벤트 수송을 이용하면 중요한 잇점이 있습니다: 지금 외부 시스템(Node.js는 아니고)으로부터 연결된 브라우저에게 쉽게 이벤트를 보낼수 있습니다.  JSON 메세지 포멧을 이용하여 외부 시스템과 같은 인스턴스, 게시한 메세지를 간단히 연결한다.(예 Erlang 서비스)  


**Notes**
**노트**

1. If the channel name you specify does not exist it will be automatically created. Channel names can be any valid JavaScript object key. If the client gets disconnected and re-connects to another server instance they will automatically be re-subscribed to the same channels, providing they retain the same `sessionId`. Be sure to catch for any errors when using these commands.  
1. 지정한 채널이름이 존재하지 않는 경우 자동으로 생성되 것입니다. 채널 이름들은 유효한 JavaScript 객채 키가 될 수 있습니다. 접속이 끈긴 클라이언트가 다른 서버 인스턴스로 재 접속을 하는 경우  그것들이 가지고 있는 같은`sessionId`가 제공하기 때문에 그것들은 자동적으로 같은 채널들로 재-구독(재-참조) 될 것입니다.  

2. The SocketStream Pub/Sub system has been designed from the ground up with horizontal scalability and high-throughput in mind. The `all` and `channel` commands will be automatically load-balanced across all SocketStream servers when an external event transport is used.  
2. SocketStream Pub/Sub시스템음 수평 확장성 과 높은 처리량을 염두해 두고 하위에서 상향으로 설계되었습니다. `all`과 `channel`명령어들은 외부의 이벤트 수송이 사용되어 질 때 자동적으로 모든 SocketStream 서버들 간의 로드-밸런싱(균형-조정)될 것입니다.  

3. It is important to remember messages are never stored or logged. This means if a client/user is offline the message will be lost rather than queued. Hence, if you're implementing a real time chat app we recommend storing messages in a database (or messaging server) before publishing them.  

3. 그것은 로그를 남기거나 메세지를 저장하지 않는 것을 기억하는 점이 중요합니다. 이것은 클라리언트/사용자가 오프라인에 되면 메세지를 싫어 버리기 보다는 오히려 큐에 적제된 상태를 의미합니다. 그러므로 실시간 채팅앱을 구현하는 경우 메세지를 게시하기 전에 데이터베이스에(또는 메세징 서버에) 저장하는 것을 추천합니다.