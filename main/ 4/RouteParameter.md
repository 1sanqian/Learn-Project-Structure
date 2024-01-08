# RouteParameter 路由参数

### 1. FromQuery 特性

+ 用于从请求的 `查询字符串` 中提取参数值
+ 通常用于 GET 请求，因为 GET 请求的参数通常是通过查询字符串传递的
+ 在控制器方法的参数上使用  FromQuery 特性，表示该参数的值应该从请求的查询字符串中获取

```
[HttpGet]
public IActionResult GetData([FromQuery] string parameter1, [FromQuery] int parameter2)
{
    // 参数值来自查询字符串
    // 示例 URL: /api/data?parameter1=value1&parameter2=42
    // parameter1 的值为 "value1"
    // parameter2 的值为 42
    }
```

### 2. FromBody 特性

+ 用于从 `请求体` 中提取参数值
+ 通常用于 POST、PUT、DELETE 等请求，因为这些请求的参数通常是通过请求体传递的
+ 在控制器方法的参数上使用 FromBody 特性，表示该参数的值应该从请求体中获取

```
[HttpPost]
public IActionResult PostData([FromBody] SomeModel model)
{
    // 参数值来自请求体
    // 示例 JSON 请求体: { "property1": "value1", "property2": 42 }
    // model 的属性值根据 JSON 提供的值进行绑定
}
```

### 3. FromRoute 特性

+ 用于从请求的 `URL 路由` 中提取参数值
+ 通常用于 RESTful API 中，从 URL 中提取参数

```
[HttpGet("api/{id}")]
public IActionResult GetById([FromRoute] int id)
{
    // 参数值来自 URL 路由
    // 示例 URL: /api/42
    // id 的值为 42
}
```

### 4. FromHeader 特性

+ 用于从 `请求的头部` 中提取参数值
+ 通常用于需要从 HTTP 头部中获取信息的情况

```
[HttpGet]
public IActionResult GetByHeader([FromHeader] string authorization)
{
    // 参数值来自请求头部
    // 示例请求头: Authorization: Bearer token123
    // authorization 的值为 "Bearer token123"
}
```

### 5. FromForm 特性

+ 用于从 `表单数据` 中提取参数值
+ 通常用于 POST 请求中的表单提交

```
[HttpPost]
public IActionResult PostFormData([FromForm] string username, [FromForm] string password)
{
    // 参数值来自表单数据
    // 示例表单数据: username=value1&password=value2
    // username 的值为 "value1"
    // password 的值为 "value2"
}
```

### 6. FromServices 特性

+ 用于从 `服务容器` 中获取服务实例

```
[HttpGet]
public IActionResult GetFromService([FromServices] ILogger<MyController> logger)
{
    // 参数值来自服务容器
    // 在 ConfigureServices 中注入 ILogger<MyController>
}
```
