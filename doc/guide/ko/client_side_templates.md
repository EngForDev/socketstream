# Client-Side Templates
# 클라이언트측 템플릿들 

Client-side templates 
generate HTML in the browser, 
allowing SocketStream to send raw, layoutless data over the websocket.  
클라이언트 측 템플릿들은 브라우저에서 웹소켓으로 SocketStream이 원시 layout없는 데이터를 보낼 수 있는 HTML을 생성합니다.

This not only dramatically reduces bandwidth, but also gives you flexibility to render the data into HTML in any number of ways.  
이것은 극적으로 대역폭을 줄일수 있을 뿐만 아니라 당신에게 여러 방법으로 데이터에서 HTML로 렌더링(정제) 할 수 있는 유연성을 줍니다.  

### Why use client-side templates?
### 왜 클라이언트 측 템플릿을 써야 하나?

If your app is really simple, you might be happy manually building your HTML using jQuery functions:  
만약 당신의 앱이 정망 단순 하다면, jQuery 함수들로 수동으로 행복한 HTML을 작성 할 수 있지 모릅니다:

``` coffee-script
# client/code/main/index.coffee
people = [
  { name:'Alice', major:'Astronomy' }
  { name:'Bob',   major:'Biology' }
]
$(document).ready ->
  for person in people
    $('#people').append("<li>#{person.name} the student studies <strong>#{person.major}</strong></li>")
```

However, not only does this solution scale poorly for larger templates, but mixing together display logic and HTML is bad practice. Enter client-side templates.    
하지만, 이 솔루션에는 큰 템플릿은 좋지 않으나 HTML과 표현 로직을 을 함께 섞어 쓰는 것은 나쁜 관행이다. 클라이언트측 템플릿들을 입력하세요.


### Template Engines
### 템플릿 엔진들

SocketStream supports two types of client-side template engines out-of-the-box:  
SocketStream 두가지 형식의 클라이언트-측 팀플릿 (그 박스를 벗어난 ??) 엔진들을 지원합니다:

##### default
##### default

Simply wraps each template in a DIV tag with an ID prefix of 'tmpl-'.
간단히 각 DIV태그에 'tmpl-'의 접두어 아이디를 함께 랩핑합니다.

Suitable for use with many template engines, including jQuery Templates (as used by SocketStream 0.2). Used by default if no template engine is specified.
(SocketStream 0.2의 사용 처럼) jQuery 템플릿등 많은 템플릿 엔진과 함께 사용하기 적합합니다. 만약 특별히 템플릿 지정하지 않았다면 default가 사용됩니다.

##### ember
##### ember

Outputs templates in the correct format for Ember.js. No prefix is added.  
Ember.js에 대한 올바른 포멧으로 템플릿을 출력합니다. 접두어는 추가되지 않습니다.

To use a built-in template engine, pass the name as a string:  
내장된 템플릿 엔진을 사용하기 위해서, 문자열 이름을 전달:

```javascript
ss.client.templateEngine.use('ember');
```

As built-in template engines are only simple wrappers, most of the time you'll want to use one of the several template languages supported via optional modules on NPM:  
내장된 템플릿 엔진은 정말 단순한 래퍼이기 때문에, 당신은 대부분의 시간을 NPM에 선택적 모듈을 통해 지원되는 몇가지 템플릿 언어 중에 한가지를 이용하기를 원할 겁니다.  

* [ss-hogan](https://github.com/socketstream/ss-hogan) Mustache templates (compiled on the server, requires small client-side lib). This is our recommended template engine.
* [ss-hogan](https://github.com/socketstream/ss-hogan) Mustache templates (서버에서 컴파일, 작은 클라이언트측 라이브러리 필요). 이것이 우리가 추천천하는 템플릿 엔진입니다.  
* [ss-coffeekup](https://github.com/socketstream/ss-coffeekup) CoffeeKup templates (compiled on the server, no client-side code required)  
* [ss-coffeekup](https://github.com/socketstream/ss-coffeekup) CoffeeKup templates (서버에서 컴파일, 클라이언트측 코드 필요 없음)  
* [ss-clientjade](https://github.com/sveisvei/ss-clientjade) Client-side Jade templates (compiled on the server, requires small client-side lib)  
* [ss-clientjade](https://github.com/sveisvei/ss-clientjade) Client-side Jade templates (서버에서 컴파일, 작은 클라이언트측 라이브러리 필요)  

To use an external optional template engine, pass the module as so:  
외부의 선택적인 템플릿 엔진을 사용하기 위해서,  이 처럼 그 모듈을 전달:

```javascript
ss.client.templateEngine.use(require('ss-hogan'));
```

If you can't find a module for your favorite templating library it's easy to [create your own](https://github.com/socketstream/socketstream/blob/master/doc/guide/en/template_engine_wrappers.md).  
당신이 선호하는 템플릿라이브러리를 찾을수 없다면 쉽게 [자신의 것](https://github.com/socketstream/socketstream/blob/master/doc/guide/en/template_engine_wrappers.md) 을 생성 할 수 있습니다.
(!!! 머지 시점에서 위에 문서 링크 [자신의 것](https://github.com/socketstream/socketstream/blob/master/doc/guide/en/template_engine_wrappers.md) 적당히 바꿔준다.)


### Mix and match different template engines
### 다른 템플릿 엔진들과 섞기 그리고 매치(일치) 시키기 

All client-side templates live in the `client/templates` folder; however you don't have to serve every template with the same engine.  
모든 클라리언트 템플릿들은 `client/templates`폴더 안에 있습니다; 하지만 같은 엔진과 모든 템플릿을 제공하지 않아도 됩니다.  

SocketStream allows you to mix and match different templates, perfect for trying out something like Ember.js without having to convert all your exiting templates over at once.  
SocketStream 당신이 다른 템플릿들을 섞고 매치(일치)시키는 것을 허용하고, Ember.js처럼 존재하는 당신의 모든 템플릿들의 변환 종료 없이 시도하는 것에 알맞다. ???? 엉???   

You may limit the scope of a template engine by passing the name of a directory as the second argument.  
두번때 인자로 디렉토리 이름을 전달하여 템플릿엔진의 지역을 제안할 수 있다. 

```javascript
// serve all templates with ss-hogan
ss.client.templateEngine.use(require('ss-hogan'));
// apart from any in the /client/templates/em directory
ss.client.templateEngine.use('ember', '/em');
```


### Example
### 예제

Here we're using the [Hogan](http://twitter.github.com/hogan.js/) templating library, using the `ss-hogan` module bundled by default when you create a new project.  
여기서 우리는 [Hogan](http://twitter.github.com/hogan.js/) 템플릿 라이브러리로, 프로젝트를 생성할때 default로 번들된 `ss-hogan` 모듈을 이용하고 있습니다. 

In this folder, let's create a file called `person.html`:

``` html
<!-- client/templates/person.html -->
<li>{{ name }} the student studies <strong>{{ major }}</strong></li>
```

**NOTE:** If you prefer, you may use a formatter to construct your HTML templates. For example, to use Jade, use `.jade` instead of `.html` for your template's file extension.  
**메모:** 만약 완벽하게 하고 싶다면, 당신의 HTML 템믈릿들을 구성하기 위한 포매터를 사용하시오. 예로 Jade 사용할 경우 당신의 템플릿 파일들의 확장자를 `.jade` 대신 `.html` 사용하세요. 

If you refresh the page and view the HTML source code you'll see a new `<script>` tag containing the compiled template.  
페이지를 새로고친 후 HTML 소스를 보면 당신은 컴파일된 템플릿을 포함하는 새로운 `<script>`태그를 볼 수 있을 것입니다.  

The `person.html` file in the `templates` folder is now accessible via `ss.tmpl['person']`. If the file was in a subdirectory `model/person.html`, then it would be accessible via `ss.tmpl['model-person']`.  
`templates` 폴더 안에 `person.html`파일은 이제  `ss.tmpl['person']`을 통해 접근할 수 있습니다. 파일이 `model/person.html` 처럼 서브디렉토리에 있다면 `ss.tmpl['model-person']로 접근 할 수 있습니다.  

Now that we have a template, let's put it to good use by refactoring our code:
이제 우리는 템플릿을 얻었고,  우리 코드를 리펙토링해서 잘 동작하도록 집어 넣어 봅시다.

``` coffee-script
# in your client-side code
people = [
  { name:'Alice', major:'Astronomy' }
  { name:'Bob',   major:'Biology' }
]
$(document).ready ->
  for person in people
    $('#people').append ss.tmpl['person'].render(person)
```

### Serving different templates to different clients
### 다른 클라이언트에 다른 템플릿들을 서비스 하기

By default all templates will be sent to all single-page clients you define with:  
기본적으로 모든 템플릿들은모든 클라이언트에게 당신이 정의한 것과 함께 실글-페이지를 전송할 것입니다.  

``` javascript
ss.client.define()
```

However, by organizing your templates into directories, you can specify which templates will be sent to each client as so:
그러나 디렉토리에 템플리을 만듦으로서, 당신은 각각의 클라리언트에게 전 송될 템플릿을 지정할 수 있습니다. 

``` javascript
// app.js
ss.client.define('iphone', {
  view: 'app.jade',
  css:  ['libs', 'app.styl'],
  code: ['libs', 'app'],
  tmpl: ['main', 'mobile']  // will only send templates in the 'main' and 'mobile' directories 
                            // 오직 템플릿들을 'main' 과 'mobile' 디렉토리들로 전달 할 것이다.
});
```



