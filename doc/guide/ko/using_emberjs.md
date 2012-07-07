<!--# Use Ember MVC JS lib with SocketStream-->
# 엠버 MVC JS 라이브러리와 함께 소켓스트림 사용하기

<!--Socketstream already support Ember MVC in the core and it is really easy to integrate your ember MVC end point into SocketStream framework.
Following are steps to create an end-point with Ember view in SocketStream.
-->
소켓스트림은 이미 엠버 MVC를 코어 레벨에서 지원하고 있고 정말 쉽게 엠버 MVC 엔드 포인트를 소켓스트림 안에 적용할 수 있습니다.

<!--### Defining Ember js MVC client end-point
-->
### 엠버 js MVC 클라이언트 엔드포인트 선언하기

<!--Use `ss.client.define` to define the Ember MVC client end point in app.js
-->
app.js파일에 `ss.client.define`를 사용해 엠버 MVC 클라이언트 엔드 포인트를 선언합니다.
``` javascript
  ss.client.define('todos', {
    view:   'todos.html',
    css:    ['libs', 'todos.css'],
    code:   ['libs', 'todos'],
    tmpl:   []
});
```

<!--Add route for the newly defined end-point.
-->
새로 선언한 엔드포인트를 위한 라우트를 추가합니다.

``` javascript
  ss.http.router.on('/todos', function(req, res) { res.serveClient('todos');}
```

<!--Enable Ember template engine in the app.js configuration.
-->
app.js의 설정에서 엠버 템플릿 엔진을 활성화 시킵니다.

<!--``` javascript
  ss.client.templateEngine.use('ember');
  // or use('ember', '/todos') to limit ember only to '/todos' directory.
  ss.client.templateEngine.use('ember', '/todos');
```-->
``` javascript
  ss.client.templateEngine.use('ember');
  // 아니면 use('ember', '/todos') 로 '/todos' 디렉토리에 엠버를 한정 시킬 수 있습니다.
  ss.client.templateEngine.use('ember', '/todos');
```

<!--### Add client html views with handlebar templates
-->
### 핸들바 템블릿으로 클라이언트 html 뷰 추가하기

<!--You can copy the Todos example html file from Ember website into `client/view/todos.html`.
Note you need to add <SocketStream>  in the head so socketstream framework gets loaded.
-->
엠버 웹페이지의 Todos 예제를 `client/view/todos.html`에 복사하세요.
<SocketStream>를 가장 위에 적어 두시면 소켓스트림이 로딩됩니다.

<!--In the client todos.html, html view elements are described using handlebar scripts, not html tags directly.
The data model of the view and view event handlers are defined in the client app.js at `client/code/todos/app.js`.
-->
todos.html 클라이언트 안에서, html뷰 요소는 html 태그를 직접 사용하는게 아니라 헨들바 스크립트를 사용해 기술됩니다.
뷰의 데이터 모델과 뷰 이벤트 핸들러는 클라이언트의 app.js(`client/code/todos/app.js`)에 선언합니다.

<!--For example, in todos.html, list of todo items are placed into a ul list view with data come from an ember array controller.
-->
예를 들면, todos.html 안에서, todo아이템의 리스트는 엠버 어레이 컨트롤러에서 온 데이터와 함께 ul리스트 뷰에 들어갑니다.

<!--``` javascript

  // display createTodoView in todos.html
    {{ view Todos.CreateTodoView id="new-todo" placeholder="What needs to be done?"}}

    // list all todo items in ul view with data from ember array controller.
    {{ #collection tagName="ul" contentBinding="Todos.todosController" itemClassBinding="content.isDone"}}
        {{view Em.Checkbox titleBinding="content.title" valueBinding="content.isDone"}}
    {{/collection}}
```-->
``` javascript

  // todos.html 안에 createTodoView 를 보여줌
    {{ view Todos.CreateTodoView id="new-todo" placeholder="해야할 일은?"}}

    // 엠버 어레이 컨트롤러에서 온 데이터와 함께 ul뷰에서 모든 todo 아이템을 나열함
    {{ #collection tagName="ul" contentBinding="Todos.todosController" itemClassBinding="content.isDone"}}
        {{view Em.Checkbox titleBinding="content.title" valueBinding="content.isDone"}}
    {{/collection}}
```

<!--In client app.js, we create an ember array controller to hold todo data model, as well as other views used in the html.
-->
클라이언트 app.js안에서, 엠버 어레이 컨트롤러를 생성해 todo 데이터 모델을 저장할 뿐만 아니라 html의 다른 뷰에서 사용할 수 있습니다.

<!--``` javascript

    window.Todos = Todos = Ember.Application.create({ ... });

  // define Ember array controller that holds all todo items data model.
    Todos.todosController = Em.ArrayController.create({
        clearCompletedTodos: function() {
            this.filterProperty('isDone', true).forEach(this.removeObject, this);
        }

        allAreDone: function(key, value) {
            if (value !== undefined) {
                this.setEach('isDone', value);
                return value;
            } else {
                return !!this.get('length') && this.everyProperty('isDone', true);
            }
        }.property('@each.isDone')
    }

    // define new todo item view and view event handler.
    Todos.CreateTodoView = Em.TextField.extend({
      insertNewLine: function() {
           Todos.todosController.createTodo(this.get('value');
        }
    });

```-->
``` javascript

    window.Todos = Todos = Ember.Application.create({ ... });

  // 모든 todo 데이터 모델을 저장할 엠버 어레이 컨트롤러를 선언
    Todos.todosController = Em.ArrayController.create({
        clearCompletedTodos: function() {
            this.filterProperty('isDone', true).forEach(this.removeObject, this);
        }

        allAreDone: function(key, value) {
            if (value !== undefined) {
                this.setEach('isDone', value);
                return value;
            } else {
                return !!this.get('length') && this.everyProperty('isDone', true);
            }
        }.property('@each.isDone')
    }

    // new todo아이템과 이벤트 핸들러를 선언.
    Todos.CreateTodoView = Em.TextField.extend({
      insertNewLine: function() {
           Todos.todosController.createTodo(this.get('value');
        }
    });

```

<!--### Wiring up everything-->
### 모든걸 엮기

<!--First, make sure all templates declared in the html have been defined properly in your client app.js file.-->
먼저 모든 템플릿이 html에 선언 되어있고 클라이언트 app.js안의 모든 프로퍼티가 선억되어있는지 확인합니다.

<!--Second, we need ember.js lib to be downloaded to the client before the html file gets parsed.
In client/code/libs, ensure lib loading order: 1.jquery, 2.ember.min.js, 3. …-->
그런 다음, html파일이 파싱되기 전에 ember.js 라이브러리를 클라이언트쪽에 다운로드 해둘 필요가 있습니다.
client/code/libs안에서, 로딩 순서를 확인 해주세요: 1.jquery, 2.ember.min.js, 3. …

<!--Finally, we need to load the client app.js that creates ember app and controller as early as possible so views are defined before DOM elements are created.-->
마지막으로, DOM객체가 생성되기전에 뷰가 선언 되도록 가능한한 일찍 엠버 엡과 컨트롤러가 선언된 클라이언트 app.js 를 로드 해야합니다.
<!--In client/code/ember/entry.js-->
client/code/ember/entry.js 파일에서

``` javascript
  $(document).ready(function() { require('/app');}
```

<!--That's it, as simple as this.-->
이게 다입니다. 간단하죠?

<!--Client Html file contains handlebar templates gets downloaded to browser, rendered by Ember, and all of the view variables and event handlers bound properly.
Now client ember MVC works seamlessly within socketstream framework.-->
핸들바 탬플릿을 포함한 클라이언트 Html 파일은 프라우저에 다운로드 되고, 엠버로 렌더링되고, 뷰 변수와 이벤트 핸들러들이 올바르게 바인딩 됩니다.
이제 클라이언트 엠버 MVC가 소켓스트림 안에서 원활하게 동작합니다.

<!--In client app.js, you can use `ss.rpc()` to retrieve data from backend as usual.
You can also add client routing libraries such as Path.js, crossroads.js to give more capabilities to your MVC client.-->
클라이언트 app.js 파일 안에서 보통 `ss.rpc()`를 사용해 백엔드의 데이터를 검색합니다.
MVC 클라이언트에 더 많은 기능을 재공 하고 싶으면 Path.js, crossroads.js를 사용해 클라이언트 라우팅을 할수도 있습니다.
