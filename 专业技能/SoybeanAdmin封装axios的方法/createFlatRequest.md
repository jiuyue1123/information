```ts
/**

 * create a flat request instance

 *

 * The response data is a flat object: { data: any, error: AxiosError }

 *

 * @param axiosConfig axios config

 * @param options request options

 */
 export function createFlatRequest<ResponseData = any, State = Record<string, unknown>>(

  axiosConfig?: CreateAxiosDefaults,

  options?: Partial<RequestOption<ResponseData>>

) {

  const { instance, opts, cancelRequest, cancelAllRequest } = createCommonRequest<ResponseData>(axiosConfig, options);

  

  const flatRequest: FlatRequestInstance<State, ResponseData> = async function flatRequest<

    T = any,

    R extends ResponseType = 'json'

  >(config: CustomAxiosRequestConfig) {

    try {

      const response: AxiosResponse<ResponseData> = await instance(config);

  

      const responseType = response.config?.responseType || 'json';

  

      if (responseType === 'json') {

        const data = opts.transformBackendResponse(response);

  

        return { data, error: null };

      }

  

      return { data: response.data as MappedType<R, T>, error: null };

    } catch (error) {

      return { data: null, error };

    }

  } as FlatRequestInstance<State, ResponseData>;

  

  flatRequest.cancelRequest = cancelRequest;

  flatRequest.cancelAllRequest = cancelAllRequest;

  flatRequest.state = {} as State;

  

  return flatRequest;

}
```
`createFlatRequest` 函数是一个用于创建建一个扁平化的请求的泛型函数。它允许用户自定义请求的配置和选项。
- `ResponseData`: 这是一个泛型参数，默认类型为 `any`。它指定了预期的响应数据类型。使用泛型允许调用者指定他们期望从请求中获得的数据类型，从而提供类型安全。
- `State`: 这是另一个泛型参数，默认类型为 `Record<string, unknown>`。它用于指定可能与请求相关的状态信息的类型，例如请求过程中的加载状态、错误信息等。如下
```ts
export interface RequestInstanceState {

  /** whether the request is refreshing token */

  isRefreshingToken: boolean;

  /** the request error message stack */

  errMsgStack: string[];

}
```
[[createCommonRequest]]
然后是FlatRequestInstance，这个函数是一个封装扁平化请求实例的函数，使用`createCommonRequest`返回的`instance`来发送请求
```ts
const response: AxiosResponse<ResponseData> = await instance(config);
```
在这里指定`AxiosResponse`(讲过了)的data属性的类型为`ResponseData`，这就是之前说的跟后端一起定义的统一返回类型。
`FlatRequestInstance<State, ResponseData>`这个类型的定义如下
```ts
export type FlatResponseSuccessData<T = any> = {

  data: T;

  error: null;

};

// ResponseData是为了设置AxiosError一个response属性的泛型参数，
// response?: AxiosResponse<T, D>，可以查看AxiosError源码来查看
export type FlatResponseFailData<ResponseData = any> = {

  data: null;

  error: AxiosError<ResponseData>;

};

// 这段代码定义了一个名为 FlatResponseData 的 TypeScript 联合类型，用于表示一个扁平化请求的响应数据结构。这个类型可以表示两种状态：成功或失败的响应。
export type FlatResponseData<T = any, ResponseData = any> =

  | FlatResponseSuccessData<T>

  | FlatResponseFailData<ResponseData>;
  
export interface RequestInstanceCommon<T> {

  cancelRequest: (requestId: string) => void;

  cancelAllRequest: () => void;

  /** you can set custom state in the request instance */

  state: T;

}

export interface FlatRequestInstance<S = Record<string, unknown>, ResponseData = any> extends RequestInstanceCommon<S> {
  // 定义一个方法，用来发送http请求，接受两个泛型参数，T是返回的数据类型，R是期望的http响应类型，并且必须是ResponseType中的一个值
  <T = any, R extends ResponseType = 'json'>(
    // config是这个函数的参数他的类型CustomAxiosRequestConfig，解释看下文
    config: CustomAxiosRequestConfig<R>
    // 返回值是这个，FlatResponseData，解释上文，MappedType<R, T>解释看下文
  ): Promise<FlatResponseData<MappedType<R, T>, ResponseData>>;

}
```

```ts
interface ResponseMap {
  // 表示响应为 `Blob` 类型，通常用于表示二进制数据，如图片或文件。
  blob: Blob;
  // 表示响应为纯文本字符串。
  text: string;
  // 表示响应为 `ArrayBuffer` 类型，这是一个通用的、固定长度的原始二进制数据缓冲区。
  arrayBuffer: ArrayBuffer;
  // 表示响应为 `ReadableStream<Uint8Array>` 类型，这代表了一个可读的、连续的二进制数据流。
  stream: ReadableStream<Uint8Array>;
  // 表示响应为 `Document` 类型，这通常用于表示HTML或XML文档。
  document: Document;

}

export type ResponseType = keyof ResponseMap | 'json';

/*
`R` 是一个泛型参数，它必须扩展自 `ResponseType`，表示响应的类型。
`JsonType` 是一个泛型参数，它提供了一个默认值 `any`，表示如果 `R` 不是 `keyof ResponseMap` 的一个键，则回退到 `JsonType` 类型。
R extends keyof ResponseMap? ResponseMap[R]: JsonType;这是一个逻辑判断，如果R是ResponseMap中的一个值则返回ResponseMap[R]，否则返回JsonType
*/
export type MappedType<R extends ResponseType, JsonType = any> = R extends keyof ResponseMap

  ? ResponseMap[R]

  : JsonType;
```

```ts
// 这是一个泛型类型别名，其中 `R` 是一个泛型参数，它扩展自 `ResponseType` 类型，并且默认值为 `'json'`。通过Omit排除responseType属性，然后使用&操作符再次添加，相当于重写这个属性。

export type CustomAxiosRequestConfig<R extends ResponseType = 'json'> = Omit<AxiosRequestConfig, 'responseType'> & {

  responseType?: R;

};
```
