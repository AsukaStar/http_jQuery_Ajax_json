# jQuery笔记

## jQuery常用的选择器
$('p') 选取所得的p元素

$('.class') 获取class属性的元素

$('#id') 获取id属性的元素


# 事件的绑定

##页面加载完成以后，绑定事件
$(document).ready(function() {
  $('button').事件(function() {
    // 执行的代码
  });
});


# jquery中的ajax
`jquery`对`Ajax`操作进行了封装，在`jquery`中的`$.ajax()`方法属于最底层的方法，第`2`层是`load()`、`$.get()`、`$.post();`第`3`层是`$.getScript()`、`$.getJSON()`，第`2`层使用频率很高

**`$.get()`方法参数解析**

| 参数             | 类型       | 说明                                                         |
| ---------------- | ---------- | ------------------------------------------------------------ |
| `url`            | `String`   | 请求`HTML`页的地址                                           |
| `data(可选)`     | `Object`   | 发送至服务器的`key`/ `value` 数据会作为`QueryString`附加到请求URL中 |
| `callback(可选)` | `Function` | 载入成功的回调函数（只有当`Response`的返回状态是success才调用该方法） |
| `type(可选)`     | `String`   | 服务器返回内容的格式，包括`xml`、`html`、`script`、`json`、`text`和`_default` |

**$.post()方法**

- 它与$.get()方法的结构和使用方式相同，有如下区别
  - `GET`请求会将参数跟张乃URL后进行传递，而`POST`请求则是作为`Http`消息的实体内容发送给web服务器，在`ajax`请求中，这种区别对用户不可见
  - `GET`方式对传输数据有大小限制（通常不能大于`2KB`），而使用`POST`方式传递的数据量要比`GET`方式大得多（理论不受限制）
  - `GET`方式请求的数据会被浏览器缓存起来，因此其他人可以从浏览器的历史纪录中读取这些数据，如：账号、密码。在某种情况下，`GET`方式会带来严重的安全问题，而`POST`相对来说可以避免这些问题
  - `GET`和`POST`方式传递的数据在服务端的获取也不相同。在`PHP`中，`GET`方式用`$_GET[]`获取；`POST`方式用`$_POST[]`获取;两种方式都可用`$_REQUEST[]`来获取

###### $.ajax()方法

------

- `$.ajax()`方法是`jquery`最底层的`Ajax`实现，它的结构为`$.ajax(options)`
- 该方法只有一个参数，但在这个对象里包含了`$.ajax()`方式所需要的请求设置以及回调函等信息，参数以`key` / `value`存在，所有参数都是可选的

- **$.ajax()方式常用参数解析**

| 参数         | 类型       | 说明                                                         |
| ------------ | ---------- | ------------------------------------------------------------ |
| `url`        | `String`   | (默认为当前页地址)发送请求的地址                             |
| `type`       | `String`   | 请求方式（`POST`或`GET`）默认为`GET`                         |
| `timeout`    | `Number`   | 设置请求超时时间（毫秒）                                     |
| `dataType`   | `String`   | 预期服务器返回的类型。可用的类型如下<br /><br /> **xml**:返回`XML`文档，可用`jquery`处理<br />**html**:返回纯文本的`HTML`信息，包含的`script`标签也会在插入`DOM`时执行<br />**script**：返回纯文本的`javascript`代码。不会自动缓存结果，除非设置`cache`参数。注意：在远程请求时，所有的`POST`请求都将转为`GET`请求<br />**json**:返回`JSON`数据<br />**jsonp**:`JSONP`格式，使用`jsonp`形式调用函数时，例如：`myurl?call back=?,jquery`将自动替换后一个`？`为正确的函数名，以执行回调函数<br />**text**:返回纯文本字符串 |
| `beforeSend` | `Function` | 发送请求前可以修改`XMLHttpRequest`对象的函数，例如添加自定义`HTTP`头。在`beforeSend`中如果返回`false`可以取消本次`Ajax`请求。`XMLHttpRequest`对象是唯一的参数<br /> function(XMLHttpRequest){<br />          `this`;//调用本次`Ajax`请求时传递的`options`参数 } |
| `complete`   | `Function` | 请求完成后的回调函数（请求成功或失败时都调用）<br />参数：`XMLHttpRequest`对象和一个描述成功请求类型的字符串<br />function(XMLHttpRequest,textStatus){          `this`;//调用本次Ajax请求时传递的`options`参数 } |
| `success`    | `Function` | 请求成功后调用的回调函数，有两个参数<br />(1)由服务器返回，并根据`dataTyppe`参数进行处理后的数据<br />(2)描述状态的字符串<br />`function`(data,textStatus){          //`data`可能是`xmlDoc、``jsonObj`、`html`、`text`等          `this`;//调用本次`Ajax`请求时传递的`options`参数<br />} |
| `error`      | `Function` | 请求失败时被调用的函数                                       |
| `global`     | `Boolean`  | 默认为`true`。表示是否触发全局`Ajax`事件，设置为`false`将不会触发。`AjaxStart`或`AjaxStop`可用于控制各种`Ajax`事件 |


# json
SON（JavaScript Object Notation）是一种轻量级的数据交换格式。它以简洁、易于阅读和编写的方式表示结构化数据，常用于前后端数据交互或存储配置信息。

假设有一个简单的应用场景：前端需要向后端发送一个用户注册的请求，并将用户的姓名和年龄作为 JSON 数据发送给后端。后端收到请求后会解析 JSON 数据，并保存用户信息到数据库。
-- 首先，在后端定义一个 User 类，用于表示用户信息：
public class User {
    private String name;
    private int age;

    // 省略构造函数、Getter 和 Setter 方法
}
-- 然后，创建一个 RESTful API 接口，用于处理用户注册请求：
@RestController
@RequestMapping("/api/users")
public class UserController {
  
    @PostMapping("/register")
    public ResponseEntity<String> registerUser(@RequestBody User user) {
        // 处理注册逻辑，例如将用户信息保存到数据库
        // 假设这里只打印用户信息
        System.out.println("Received user: " + user.getName() + ", " + user.getAge());
        
        return ResponseEntity.ok("User registered successfully");
    }
}
    在上述代码中，通过 @PostMapping 注解指定了一个路由为 /register 的 POST 请求处理方法 registerUser，其中 @RequestBody 注解表示从请求中解析并绑定 JSON 数据到 User 对象。


