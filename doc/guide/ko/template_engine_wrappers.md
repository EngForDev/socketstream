<!--# Template Engine Wrappers-->
# ���ø� ���� ����


<!--Writing your own template engine wrapper is easy. For this quick tutorial we'll be looking at a [ss-hogan](https://github.com/socketstream/ss-hogan), a wrapper for Twitter's [Hogan library](http://twitter.github.com/hogan.js/).-->

������ ���ø� ���� ���۸� ����� ���� �����ϴ�. �� ©���� Ʃ�丮�󿡼���, [ss-hogan](https://github.com/socketstream/ss-hogan)�� ���캼 ���Դϴ�. ss-hogan�� Ʈ������ [Hogan library](http://twitter.github.com/hogan.js/)�� �����Դϴ�. 

<!--You can see the file structure for a template engine wrapper in the [ss-hogan repository]([ss-hogan](https://github.com/socketstream/ss-hogan)). Let's look at the key file, `engine.js`:-->

[ss-hogan repository]([ss-hogan](https://github.com/socketstream/ss-hogan)) �̰��� ���� ���ø� ���� ������ ���� ������ �� �� �ֽ��ϴ�. �ٽ� ������ `engine.js`�� �Բ� ���� ���ô�:

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

```�ڹٽ�ũ��Ʈ
/* engine.js */

// ���Ͻ�Ʈ�� 0.3 �� ȣ�� ���ø� ���� ����


var fs = require('fs'),
    path  = require('path'),
    hogan = require('hogan.js');

exports.init = function(ss, config) {

  // Ŭ���̾�Ʈ ���� Hogan VM�� ������.
  var clientCode = fs.readFileSync(path.join(__dirname, 'client.js'), 'utf8');
  ss.client.send('lib', 'hogan-template', clientCode);

  return {

    name: 'Hogan',

    // ó������ Hogan ���ø��� ȣ��Ǿ��� ��, �ڵ带 ����� �� �ְ� �����ش�.
    prefix: function() {
      return '<script type="text/javascript">(function(){var ht=Hogan.Template,t=require(\'socketstream\').tmpl;'
    },

    // �ϴ� ��� Hogan ���ø��� <script> �±� �ȿ� ��������, �ڵ带 �ݾ��ش�.
    suffix: function() {
      return '}).call(this);</script>';
    },

    // ���ø��� �Լ��� ���������� ��, �̰��� ss.tmpl�� �߰������ش�.
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

���⿡�� `prefix`�� `suffix`�� ���� �� ���� ȣ��˴ϴ�. �� ȣ��Ǵ� �� ���̿� �� ���ø� ������ �ڵ鸵�ϴ� ��� ���Ͽ� ���ؼ� `process`�� ȣ��˴ϴ�.

<!--In this example, the wrapper is compiling templates on the server side, then passing the compiled templates to the client. The `prefix` and `suffix` functions wrap the compiled templates inside a `<script>` tag, so that the templates are ready to use when the page loads.-->

�� ��������, ���۴� ���ø��� ���� ������ �������ϰ� �ֽ��ϴ�. �׸��� ���� �����ϵ� ���ø��� Ŭ���̾�Ʈ �ʿ� �Ѱ��ְ� �ֽ��ϴ�. `prefix`�� `suffix` �Լ��� �����ϵ� ���ø��� `<script>` �±׷� �����ݴϴ�. �� ��� �������� �ε� �Ǿ��� ��, ���ø��� ����� �� �ִ� ���°� �˴ϴ�.


<!--### Selecting a Formatter (optional)-->

### ���˱�(formatter)�� �����ϱ� (�ʼ� �ƴ�)

<!--Formatters are used to transform the contents of each template **before** it is sent to the template engine.-->

���˱�� �� ���ø��� ���ø� ������ ���۵Ǳ� **����** �� ���ø��� ������ ��ȯ�ϴµ� ����մϴ�.

<!--By default SocketStream selects a formatter based on the template file's extension. Most of the time this will prove useful (e.g. automatically transforming .jade files to HTML), but occasionally you will need to overwrite this behavior (e.g. if you want the .jade file to be sent to the template engine as-is).-->


����Ʈ��, ���Ͻ�Ʈ�������� ���ø� ������ Ȯ���ڿ� ���� ���˱⸦ �����մϴ�. ��κ��� ���� �̷� ����� �����ϴ�. (���� ���, .jade ������ �ڵ����� HTML ���Ϸ� ��ȯ���ݴϴ�.) ������ ������ �������� �� ����� �ٸ��� �����ϰ� ���� ���� �ֽ��ϴ�. (���� ���, .jade������ is ���Ϸ� ���ø� �������� �Ѱ��ְ� ���� ��쿡 ������.)

<!--Simply add a `selectFormatter` function to your engine:-->

�̷��� �Ϸ��� �������ٰ� `selectFormatter` �Լ��� �߰��ϼ��� : 


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

* `path`(���)�� ���ø� ������ ��θ� `client/templates` ������ ���� ����η� ǥ���� ���Դϴ�.
* `formatters`(���˱�)�� ���Ͻ�Ʈ���� �̹� �˰� �ִ� ���˱��� ���� �ǹ��մϴ�:
    * ������ Ű�� ������ Ȯ�����Դϴ�. (`.`�� �����ϰ��)
    * ������ ���� Ȯ���� ������ ���� ���˱��Դϴ�.
* `defaultFormatter`�� ���Ͻ�Ʈ���� ����Ʈ�� �����ϴ� ���˱��Դϴ�.


<!--The formatter that `selectFormatter` returns is the formatter that SocketStream will use to render the template **before** passing it to the template engine's `process` function. If you return `false`, SocketStream will pass the contents of the file directly to the template engine without any pre-processing.-->

`selectFormatter`�Լ��� ��ȯ�ϴ� ���˱�� ���Ͻ�Ʈ���� ���ø��� �������ϴ� ���� ����մϴ�. �������� �Ŀ�, �װ��� ���ø� ������ `process` �Լ����� �Ѱ��ݴϴ�. `false`�� ��ȯ�ϸ�, ���Ͻ�Ʈ���� ������ ������ �ƹ� ó���� ���� �ʰ�, �״�� ���ø� �������� �Ѱ��� ���Դϴ�. 


<!--### Tips-->

### �����ϼ���.

<!--When building your own template wrapper be sure to consider the following points:-->

�������� ���� ���ø� ���۸� ���� ������ ������ ���� ���� ���� �� �� ������ ������: 

<!--
* Should you pre-compile the templates on the server before sending them over the wire? (generally a good idea)
* Does the browser need a VM (small client-side lib) to render the templates?->

* ���ø��� ���ͳ��� ���� ������ ���� �������� �̸� ������ �ؾ� �ұ�? (������ �ϴ� ���� �����ϴ�.)
* ���ø��� �������Ϸ��� �������� VM(Ŭ���̾�Ʈ ���� ���� ���̺귯��)�� �־�� �ұ�? 

<!--
Try to pick the best trade off between performance and overall bytes sent to the client, bearing in mind it's not uncommon for a large app to have 50 or more templates.

If a client-side VM is needed to render the template (as is normally the case) you should include the client code within your module and tell SocketStream to server it using the `ss.client.send()` command (see Hogan example above). -->

������ ���� �� ������, �ƴϸ� Ŭ���̾�Ʈ�� ������ �������� ���� ���� ������, �� ����� ������ ������ ã�ƾ� �մϴ�. �ٸ�, ū ���� ��쿡, 50�� �̻��� ���ø��� �����ϴ� ��찡 ���ϴٴ� ���� ����ϼ���.

���ø��� �������Ϸ���, Ŭ���̾�Ʈ ������ VM�� �ʿ��ϴٸ� (������ �׷���.), Ŭ���̾�Ʈ �ڵ带 �������� ���ȿ� ���Խ��Ѿ� �մϴ�. �׸��� ���Ͻ�Ʈ������ `ss.client.send()` ��ɾ ����ؼ� �� ����� server�϶�� �����־�� �մϴ�. (���� Hogan ������ �����ϼ���.)
