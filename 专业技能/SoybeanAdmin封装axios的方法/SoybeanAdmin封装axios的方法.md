# 为什么需要封装axios？
当 axios 应用于一些前端项目中，如后台管理系统中，需要 axios 基本功能之外，常常需要加入防止接口重复请求、取消请求、消息提示、请求重试、状态码检查处理，特别是后台管理系统中，大部分还需要：请求携带 token、刷新 token、token 校验、token 刷新、错误退出登录等功能。
# 具体的封装方式
首先通过阅读源码确定函数之间的调用关系
![Pasted image 20240705213733](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/Pasted%20image%2020240705213733.png)
以下是各个部分的主要代码
[[index.ts]]
[[createFlatRequest]]
[[createCommonRequest]]
[[createDefaultOptions]]
[[createAxiosConfig]]
[[createRetryOptions]]

如果直接使用axios发送请求的话就会返回一个 `Promise<AxiosResponse<any, any>>`类型，`AxiosResponse`是一个axios内置的类型，定义如下
```ts
export interface AxiosResponse<T = any, D = any> {
  // `data` 由服务器提供的响应
  data: T;
  // `status` 来自服务器响应的 HTTP 状态码
  status: number;
  // `statusText` 来自服务器响应的 HTTP 状态信息
  statusText: string;
  // `headers` 是服务器响应头  
  // 所有的 header 名称都是小写，而且可以使用方括号语法访问  
  // 例如: `response.headers['content-type']`
  headers: RawAxiosResponseHeaders | AxiosResponseHeaders;
  // `config` 是 `axios` 请求的配置信息
  config: InternalAxiosRequestConfig<D>;
  // `request` 是生成此响应的请求  
  // 在node.js中它是最后一个ClientRequest实例 (in redirects)，  
  // 在浏览器中则是 XMLHttpRequest 实例
  request?: any;

}
```
官网链接：[https://www.axios-http.cn/docs/res_schema](https://www.axios-http.cn/docs/res_schema)
可以看到data的类型为泛型类型，在使用中则为any，这样就会失去ts的特性支持。我们希望他应该是这样的类型，
```ts
/** The backend service response data */

    type Response<T = unknown> = {

      /** The backend service response code */

      code: string;

      /** The backend service response message */

      msg: string;

      /** The backend service response data */

      data: T;

    };
```
这是跟后端进行一起定义的统一格式。因此我们应该应该封装一个函数用来创建一个扁平化的请求实例。
然后我们看[[createFlatRequest]]，然后我们就可以如[[index.ts]]那样来使用了