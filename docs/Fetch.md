# Fetch的使用

Fetch API 提供了一个 JavaScript接口，用于进行网络操作，例如请求和响应。它还提供了一个全局 fetch()方法，该方法提供了一种简单，合理的方式来跨网络异步获取资源。

Fetch 与古老的 XMLHttpRequest相比，比 XMLHttpRequest提供了更加强大以及灵活的特性

- 当接收到一个代表错误的 HTTP 状态码时，从 fetch()返回的 Promise 不会被标记为 reject， 即使该 HTTP 响应的状态码是 404 或 500。相反，它会将 Promise 状态标记为 resolve （但是会将 resolve 的返回值的 ok 属性设置为 false ），仅当网络故障时或请求被阻止时，才会标记为 reject。

- 默认情况下，fetch 不会从服务端发送或接收任何 cookies, 如果站点依赖于用户 session，则会导致未经认证的请求（要发送 cookies，必须设置 credentials 选项）。

#### 最简单的使用

```JavaScript
let url = `https://api.devio.org/uapi/test/test?requestPrams=aa`;

fetch(url)

    .then(response => response.text()) // 将response中的data转成String

    .then(responseText => {

        console.log(responseText);

    })

    .catch(e => { //捕获异常

        console.log(e.toString());

    });
```

这里我们通过网络获取一个JSON文件并将其打印到控制台。最简单的用法是只提供一个参数用来指明想fetch()到的资源路径，然后返回一个包含响应结果的promise(一个 Response 对象)。

当然它只是一个 HTTP 响应，而不是真的JSON或String。为了获取String的内容，我们需要使用 text()方法来将response中的data转成String

## 支持的请求参数

```JavaScript
Promise fetch(input,init);
```

### 参数

- input：定义要获取的资源。这可能是
  - 一个 String 字符串，包含要获取资源的 URL

- 一个 Request 对象。

- init：[可选] 一个配置项对象，包括所有对请求的设置。可选的参数有：
  - **method**: 请求使用的方法，如 GET, POST, PUT, DELETE等。

- **headers**: 请求的头信息，形式为 Headers 的对象或包含 ByteString 值的对象字面量。

- **body**: 请求的 body 信息：可能是一个 Blob、BufferSource、FormData、URLSearchParams 或者String 对象。注意 GET 或 HEAD 方法的请求不能包含 body 信息。

- mode: 请求的模式，如 cors、 no-cors 或者 same-origin。

- credentials: 请求的 credentials，如 omit、same-origin 或者 include。为了在当前域名内自动发送 cookie ， 必须提供这个选项， 从 Chrome 50 开始， 这个属性也可以接受 FederatedCredential 实例或是一个 PasswordCredential 实例。

- cache: 请求的 cache 模式: default 、 no-store 、 reload 、 no-cache 、 force-cache 或者 only-if-cached 。

- redirect: 可用的 redirect 模式: follow (自动重定向), error (如果产生重定向将自动终止并且抛出一个错误), 或者 manual (手动处理重定向). 在Chrome中，Chrome 47之前的默认值是 follow，从 Chrome 47开始是 manual。

- referrer: 一个 USVString 可以是 no-referrer、client或一个 URL。默认是 client。

- referrerPolicy: 指定 referer HTTP header的值，可选值[no-referrer、 no-referrer-when-downgrade、 origin、 origin-when-cross-origin、 unsafe-url]。

- integrity: 包括请求的 subresource integrity 值 （ 例如： sha256-BpfBw7ivV8q2jLiT13fxDYAe2tJllusRSZ273h2nFSE=）。

### 示例

```JavaScript
fetch(url, {

    body: JSON.stringify(data), // 数据类型要和 'Content-Type' header保持一致

    cache: 'no-cache', // default, no-cache, reload, force-cache 或者 only-if-cached

    credentials: 'same-origin', // omit、same-origin 或者 include

    headers: {

      'user-agent': 'Mozilla/4.0 MDN Example',

      'content-type': 'application/json'

    },

    method: 'POST', // GET, POST, PUT, DELETE 等.

    mode: 'cors', // no-cors, cors 或same-origin

    redirect: 'follow', // manual, follow 或 error

    referrer: 'no-referrer', // client 或no-referrer

})

.then(response => response.json()) // 将数据解析成JSON
```

### 请求错误与异常处理

如果遇到网络故障，[fetch()](https://developer.mozilla.org/zh-CN/docs/Web/API/GlobalFetch/fetch) promise 将会调用reject，带上一个[TypeError](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError) 对象。

需要注意的是：一次请求没有调用reject并不代表请求就一定成功了，通常情况我们需要在resolved 的情况，再判断 Response.ok 是不是为 true：类似以下代码：

```JavaScript
let url = `https://api.devio.org/uapi/test/test?requestPrams=aa`;

fetch(url)

    .then(response => {

        if (!response.ok) {

            return response.text();//将response中的data转成String

        }

        throw new Error('Network response was not ok.');

    })

    .then(responseText => {

         console.log(responseText);

    })

    .catch(e => {//捕获异常

        console.log(e.toString());

    });
```