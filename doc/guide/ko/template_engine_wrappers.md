<!--# Template Engine Wrappers-->
# 템플릿 엔진 래퍼


<!--Writing your own template engine wrapper is easy. For this quick tutorial we'll be looking at a [ss-hogan](https://github.com/socketstream/ss-hogan), a wrapper for Twitter's [Hogan library](http://twitter.github.com/hogan.js/).-->

나만의 템플릿 엔진 래퍼를 만드는 것은 쉽습니다. 이 짤막한 튜토리얼에서는, [ss-hogan](https://github.com/socketstream/ss-hogan)을 살펴볼 것입니다. ss-hogan은 트위터의 [Hogan library](http://twitter.github.com/hogan.js/)용 래퍼입니다. 

<!--You can see the file structure for a template engine wrapper in the [ss-hogan repository]([ss-hogan](https://github.com/socketstream/ss-hogan)). Let's look at the key file, `engine.js`:-->

[ss-hogan repository]([ss-hogan](https://github.com/socketstream/ss-hogan)) 이곳에 보면 템플릿 엔진 래퍼의 파일 구조를 볼 수 있습니다. 핵심 파일인 `engine.js`을 함께 살펴 봅시다:

<!--
```javascript
/* engine.js */

// Hogan Template Engine wrapper for SocketStream 0.3


var fs = require('fs'),
    path  = require('path'),
    hogan = require('hogan.js');

exports.init = function(ss, config) {

  // Send Hogan VM to the client
  var clientCode = fs.readFileSync(path.join(__dirname, 'client.js'), 'utf8');
  ss.client.send('lib', 'hogan-template', clientCode);

  return {

    name: 'Hogan',

    // Opening code to use when a Hogan template is called for the first time
    prefix: function() {
      return '<script type="text/javascript">(function(){var ht=Hogan.Template,t=require(\'socketstream\').tmpl;'
    },

    // Closing code once all Hogan templates have been written into the <script> tag
    suffix: function() {
      return '}).call(this);</script>';
    },

    // Compile template into a function and attach it to ss.tmpl
    process: function(template, path, id) {

      var compiledTemplate;

      try {
        compiledTemplate = hogan.compile(template, {asString: true});
      } catch (e) {
        var message = '! Error compiling the ' + path + ' template into Hogan';
        console.log(String.prototype.hasOwnProperty('red') && message.red || message);
        throw new Error(e);
        compiledTemplate = '<p>Error</p>';
      }

      return 't[\'' + id + '\']=new ht(' + compiledTemplate + ');';    
    }
  }
}
```
-->

```자바스크립트
/* engine.js */

// 소켓스트림 0.3 용 호건 템플릿 엔진 래퍼


var fs = require('fs'),
    path  = require('path'),
    hogan = require('hogan.js');

exports.init = function(ss, config) {

  // 클라이언트 측에 Hogan VM을 보낸다.
  var clientCode = fs.readFileSync(path.join(__dirname, 'client.js'), 'utf8');
  ss.client.send('lib', 'hogan-template', clientCode);

  return {

    name: 'Hogan',

    // 처음으로 Hogan 템플릿이 호출되었을 때, 코드를 사용할 수 있게 열어준다.
    prefix: function() {
      return '<script type="text/javascript">(function(){var ht=Hogan.Template,t=require(\'socketstream\').tmpl;'
    },

    // 일단 모든 Hogan 템플릿이 <script> 태그 안에 써졌으면, 코드를 닫아준다.
    suffix: function() {
      return '}).call(this);</script>';
    },

    // 템플릿을 함수로 컴파일해준 후, 이것을 ss.tmpl에 추가시켜준다.
    process: function(template, path, id) {

      var compiledTemplate;

      try {
        compiledTemplate = hogan.compile(template, {asString: true});
      } catch (e) {
        var message = '! Error compiling the ' + path + ' template into Hogan';
        console.log(String.prototype.hasOwnProperty('red') && message.red || message);
        throw new Error(e);
        compiledTemplate = '<p>Error</p>';
      }

      return 't[\'' + id + '\']=new ht(' + compiledTemplate + ');';    
    }
  }
}
```

<!--Here `prefix` and `suffix` are called once each. In between those calls, `process` gets called for every file this template engine handles.-->

여기에서 `prefix`와 `suffix`는 각각 한 번씩 호출됩니다. 이 호출되는 것 사이에 이 템플릿 엔진이 핸들링하는 모든 파일에 대해서 `process`가 호출됩니다.

<!--In this example, the wrapper is compiling templates on the server side, then passing the compiled templates to the client. The `prefix` and `suffix` functions wrap the compiled templates inside a `<script>` tag, so that the templates are ready to use when the page loads.-->

이 예제에서, 래퍼는 템플릿을 서버 측에서 컴파일하고 있습니다. 그리고 나서 컴파일된 템플릿을 클라이언트 쪽에 넘겨주고 있습니다. `prefix`와 `suffix` 함수는 컴파일된 템플릿을 `<script>` 태그로 감싸줍니다. 그 결과 페이지가 로드 되었을 때, 템플릿은 사용할 수 있는 상태가 됩니다.


<!--### Selecting a Formatter (optional)-->

### 포맷기(formatter)를 선택하기 (필수 아님)

<!--Formatters are used to transform the contents of each template **before** it is sent to the template engine.-->

포맷기는 각 템플릿이 템플릿 엔진에 전송되기 **전에** 각 템플릿의 내용을 변환하는데 사용합니다.

<!--By default SocketStream selects a formatter based on the template file's extension. Most of the time this will prove useful (e.g. automatically transforming .jade files to HTML), but occasionally you will need to overwrite this behavior (e.g. if you want the .jade file to be sent to the template engine as-is).-->


디폴트로, 소켓스트림에서는 템플릿 파일의 확장자에 따라 포맷기를 선택합니다. 대부분의 경우는 이런 방식이 좋습니다. (예를 들어, .jade 파일은 자동으로 HTML 파일로 전환해줍니다.) 하지만 때때로 여러분은 이 방식을 다르게 정의하고 싶을 수도 있습니다. (예를 들어, .jade파일을 is 파일로 템플릿 엔진에게 넘겨주고 싶은 경우에 말이죠.)

<!--Simply add a `selectFormatter` function to your engine:-->

이렇게 하려면 엔진에다가 `selectFormatter` 함수를 추가하세요 : 


```javascript
/* lib/engine.js */

exports.init = function (root, config) {
  // ...
  return {
    // ...
    selectFormatter: function (path, formatters, defaultFormatter) {
      return defaultFormatter;
    },
    // ...
  }
};
```

<!--
* `path` is the path of the template file relative to the `client/templates` folder
* `formatters` is a map of formatters SocketStream already knows about:
    * Each key is a file extension (without a dot `.`)
    * Each value is a formatter for that extension type
* `defaultFormatter` is the formatter that SocketStream selects by default
-->

* `path`(경로)란 템플릿 파일의 경로를 `client/templates` 폴더에 대한 상대경로로 표시한 것입니다.
* `formatters`(포맷기)는 소켓스트림이 이미 알고 있는 포맷기의 맵을 의미합니다:
    * 각각의 키는 파일의 확장자입니다. (`.`은 제외하고요)
    * 각각의 값은 확장자 유형에 따른 포맷기입니다.
* `defaultFormatter`는 소켓스트림이 디폴트로 선택하는 포맷기입니다.


<!--The formatter that `selectFormatter` returns is the formatter that SocketStream will use to render the template **before** passing it to the template engine's `process` function. If you return `false`, SocketStream will pass the contents of the file directly to the template engine without any pre-processing.-->

`selectFormatter`함수가 반환하는 포맷기는 소켓스트림이 템플릿을 렌더링하는 데에 사용합니다. 렌더링한 후에, 그것을 템플릿 엔진의 `process` 함수에게 넘겨줍니다. `false`를 반환하면, 소켓스트림은 파일의 내용을 아무 처리를 하지 않고, 그대로 템플릿 엔진에게 넘겨줄 것입니다. 


<!--### Tips-->

### 참고하세요.

<!--When building your own template wrapper be sure to consider the following points:-->

여러분이 직접 템플릿 래퍼를 만들 때에는 다음과 같은 점에 대해 한 번 생각해 보세요: 

<!--
* Should you pre-compile the templates on the server before sending them over the wire? (generally a good idea)
* Does the browser need a VM (small client-side lib) to render the templates?->

* 템플릿을 인터넷을 통해 보내기 전에 서버에서 미리 컴파일 해야 할까? (보통은 하는 것이 좋습니다.)
* 템플릿을 렌더링하려면 브라우저에 VM(클라이언트 측의 작은 라이브러리)이 있어야 할까? 

<!--
Try to pick the best trade off between performance and overall bytes sent to the client, bearing in mind it's not uncommon for a large app to have 50 or more templates.

If a client-side VM is needed to render the template (as is normally the case) you should include the client code within your module and tell SocketStream to server it using the `ss.client.send()` command (see Hogan example above). -->

성능을 좋게 할 것인지, 아니면 클라이언트에 보내는 데이터의 양을 줄일 것인지, 그 가운데의 적정한 균형을 찾아야 합니다. 다만, 큰 앱의 경우에, 50개 이상의 템플릿을 포함하는 경우가 흔하다는 점을 기억하세요.

템플릿을 렌더링하려면, 클라이언트 측에서 VM이 필요하다면 (보통은 그렇죠.), 클라이언트 코드를 여러분의 모듈안에 포함시켜야 합니다. 그리고 소켓스트림에게 `ss.client.send()` 명령어를 사용해서 이 모듈을 server하라고 말해주어야 합니다. (위의 Hogan 예제를 참고하세요.)
