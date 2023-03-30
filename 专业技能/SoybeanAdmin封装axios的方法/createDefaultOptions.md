```ts
export function createDefaultOptions<ResponseData = any>(options?: Partial<RequestOption<ResponseData>>) {

  const opts: RequestOption<ResponseData> = {
	// 在请求之前调用的hook
    onRequest: async config => config,
	// 检查后端响应成功与否的hook
    isBackendSuccess: _response => true,
	// 后端失败时调用的hook
    onBackendFail: async () => {},
	// 当 responseType 为 json 时转换后端响应
    transformBackendResponse: async response => response.data,
	// 处理错误的hook
    onError: async () => {}

  };

  
  // 这行代码使用`Object.assign`方法将传入的`options`参数（如果有的话）合并到`opts`对象中。这允许用户自定义或覆盖默认配置。
  Object.assign(opts, options);

  

  return opts;

}
```

```ts
export interface RequestOption<ResponseData = any> {

  /**

   * The hook before request

   *

   * For example: You can add header token in this hook

   *

   * @param config Axios config

   */

  onRequest: (config: InternalAxiosRequestConfig) => InternalAxiosRequestConfig | Promise<InternalAxiosRequestConfig>;

  /**

   * The hook to check backend response is success or not

   *

   * @param response Axios response

   */

  isBackendSuccess: (response: AxiosResponse<ResponseData>) => boolean;

  /**

   * The hook after backend request fail

   *

   * For example: You can handle the expired token in this hook

   *

   * @param response Axios response

   * @param instance Axios instance

   */

  onBackendFail: (

    response: AxiosResponse<ResponseData>,

    instance: AxiosInstance

  ) => Promise<AxiosResponse | null> | Promise<void>;

  /**

   * transform backend response when the responseType is json

   *

   * @param response Axios response

   */

  transformBackendResponse(response: AxiosResponse<ResponseData>): any | Promise<any>;

  /**

   * The hook to handle error

   *

   * For example: You can show error message in this hook

   *

   * @param error

   */

  onError: (error: AxiosError<ResponseData>) => void | Promise<void>;

}
```
