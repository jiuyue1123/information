```ts
export function createAxiosConfig(config?: Partial<CreateAxiosDefaults>) {
  // CreateAxiosDefaults是axios内置接口，定义看下文
  const TEN_SECONDS = 10 * 1000;

  

  const axiosConfig: CreateAxiosDefaults = {

    timeout: TEN_SECONDS,

    headers: {

      'Content-Type': 'application/json'

    },

    validateStatus: isHttpSuccess,

    paramsSerializer: params => {

      return stringify(params);

    }

  };

  
  // 允许用户覆盖配置
  Object.assign(axiosConfig, config);

  

  return axiosConfig;

}
```

```ts
export interface CreateAxiosDefaults<D = any> extends Omit<AxiosRequestConfig<D>, 'headers'> {

  headers?: RawAxiosRequestHeaders | AxiosHeaders | Partial<HeadersDefaults>;

}
```