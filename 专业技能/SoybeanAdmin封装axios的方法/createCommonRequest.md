```ts
function createCommonRequest<ResponseData = any>(

  axiosConfig?: CreateAxiosDefaults,

  options?: Partial<RequestOption<ResponseData>>

) {

  const opts = createDefaultOptions<ResponseData>(options);

  

  const axiosConf = createAxiosConfig(axiosConfig);

  const instance = axios.create(axiosConf);

  

  const cancelTokenSourceMap = new Map<string, CancelTokenSource>();

  

  // config axios retry

  const retryOptions = createRetryOptions(axiosConf);

  axiosRetry(instance, retryOptions);

  

  instance.interceptors.request.use(conf => {

    const config: InternalAxiosRequestConfig = { ...conf };

  

    // set request id

    const requestId = nanoid();

    config.headers.set(REQUEST_ID_KEY, requestId);

  

    // config cancel token

    const cancelTokenSource = axios.CancelToken.source();

    config.cancelToken = cancelTokenSource.token;

    cancelTokenSourceMap.set(requestId, cancelTokenSource);

  

    // handle config by hook

    const handledConfig = opts.onRequest?.(config) || config;

  

    return handledConfig;

  });

  

  instance.interceptors.response.use(

    async response => {

      const responseType: ResponseType = (response.config?.responseType as ResponseType) || 'json';

  

      if (responseType !== 'json' || opts.isBackendSuccess(response)) {

        return Promise.resolve(response);

      }

  

      const fail = await opts.onBackendFail(response, instance);

      if (fail) {

        return fail;

      }

  

      const backendError = new AxiosError<ResponseData>(

        'the backend request error',

        BACKEND_ERROR_CODE,

        response.config,

        response.request,

        response

      );

  

      await opts.onError(backendError);

  

      return Promise.reject(backendError);

    },

    async (error: AxiosError<ResponseData>) => {

      await opts.onError(error);

  

      return Promise.reject(error);

    }

  );

  

  function cancelRequest(requestId: string) {

    const cancelTokenSource = cancelTokenSourceMap.get(requestId);

    if (cancelTokenSource) {

      cancelTokenSource.cancel();

      cancelTokenSourceMap.delete(requestId);

    }

  }

  

  function cancelAllRequest() {

    cancelTokenSourceMap.forEach(cancelTokenSource => {

      cancelTokenSource.cancel();

    });

    cancelTokenSourceMap.clear();

  }

  

  return {

    instance,

    opts,

    cancelRequest,

    cancelAllRequest

  };

}
```

这个`createCommonRequest`函数创建了一个通用的Axios HTTP请求实例，配置了请求和响应拦截器，提供了请求取消功能，并且允许用户通过传入的参数自定义请求选项和Axios配置。它返回一个对象，其中包含Axios实例、配置选项、取消单个请求和取消所有请求的方法。
进入函数首先是使用[[createDefaultOptions]]和[[createAxiosConfig]]创建出选项和`axios`配置，然后
```ts
const instance = axios.create(axiosConf);
```
创建出一个`axios`实例，然后我们使用[axios-retry](https://www.npmjs.com/package/axios-retry)来实现`axios`的请求重试，首先使用[[createRetryOptions]]创建出参数需要的选项，然后传给`axiosRetry`来实现。

取消请求的方式是通过[CancelToken](https://www.axios-http.cn/docs/cancellation#canceltoken-deprecated)的方式实现的，在发送请求的时在`axios`配置里面添加由`CancelToken.source();`创建的cancelToken来`cancelToken: cancelToken`实现的。接下来使用
```ts
// 取消请求（message 参数是可选的）  
source.cancel('Operation canceled by the user.');
```
来取消请求。

```ts
instance.interceptors.request.use(conf => {

    const config: InternalAxiosRequestConfig = { ...conf };

  

    // set request id

    const requestId = nanoid();

    config.headers.set(REQUEST_ID_KEY, requestId);

  

    // config cancel token
	// 创建新的cancelToken
    const cancelTokenSource = axios.CancelToken.source();
	// 配置cancelToken
    config.cancelToken = cancelTokenSource.token;
    // kv存储requestId和cancelToken
    cancelTokenSourceMap.set(requestId, cancelTokenSource);

  

    // handle config by hook
	// 尝试调用`opts`对象中的`onRequest`函数（如果存在），并传入`config`对象。如果`onRequest`函数存在，它将处理`config`并返回新的配置对象；如果不存在，则直接使用原始的`config`。
    const handledConfig = opts.onRequest?.(config) || config;

  

    return handledConfig;

  });
```
在请求拦截器里面设置了`requestId`，设置它的目的是为了追踪单个请求。
```ts
instance.interceptors.response.use(

    async response => {
	 // 设置响应的数据类型，默认是json
      const responseType: ResponseType = (response.config?.responseType as ResponseType) || 'json';

  
	  // 如果不是json而且后端处理成功则直接返回
      if (responseType !== 'json' || opts.isBackendSuccess(response)) {

        return Promise.resolve(response);

      }

  
	  // 调用`opts.onBackendFail`函数来处理请求失败
      const fail = await opts.onBackendFail(response, instance);

      if (fail) {

        return fail;

      }

  
      // 后端失败且没有通过`onBackendFail`处理，则创建一个新的`AxiosError`实例，表示后端请求错误。
      const backendError = new AxiosError<ResponseData>(

        'the backend request error',

        BACKEND_ERROR_CODE,

        response.config,

        response.request,

        response

      );

  
      // 调用`opts.onError`函数来处理错误。
      await opts.onError(backendError);

  

      return Promise.reject(backendError);

    },
    // 第二个参数用于捕获错误进行处理

    async (error: AxiosError<ResponseData>) => {

      await opts.onError(error);

  

      return Promise.reject(error);

    }

  );
```

