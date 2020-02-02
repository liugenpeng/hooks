---
title: useRequest
group:
  title: Async Hooks
  path: /async
---

# useRequest

Production-ready React Hooks library for manage asynchronous data.

**Core Characteristics**

* Auto Request / Manual Request
* SWR(stale-while-revalidate)
* Cache / Preload
* Refresh On Window Focus
* Polling
* Debounce
* Throttle
* Concurrent Request
* Loading Delay
* Pagination
* Load more, data recovery + scroll position recovery
* [ ] Error retry
* [ ] Request timeout management
* [ ] Suspense
* ......

## Examples

### Default request

<code src="./demo/demo1.tsx" />

### Manual trigger

<code src="./demo/demo2.tsx" />

### Polling

<code src="./demo/demo4.tsx" />

### Concurrent Request

<code src="./demo/demo10.tsx" />

### Debounce

<code src="./demo/demo5.tsx" />

### Throttle

<code src="./demo/demo6.tsx" />

### Cache & SWR

<code src="./demo/demo7.tsx" />

### Preload

<code src="./demo/demo8.tsx" />

### Refresh On Window Focus

<code src="./demo/demo9.tsx" />

### Mutate

<code src="./demo/demo3.tsx" />

### Loading Delay

<code src="./demo/demo11.tsx" />

### RefreshDeps

When some `state` changes, we need to re-execute the asynchronous request. Generally, we will write code like this:

```jsx | pure
const [userId, setUserId] = useState('1');
const { data, run, loading } = useRequest(()=> getUserSchool(userId));
useEffect(() => {
  run();
}, [userId]);
```

`refreshDeps` is a syntactic sugar that makes it easier for you to implement the above functions. When `refreshDeps` changes, the service will be re-executed using the previous params.

<code src="./demo/demo12.tsx" />

## Basic API

```javascript
const {
  data,
  error,
  loading,
  run,
  params,
  cancel,
  refresh,
  mutate,
  fetches,
} = useRequest(service, {
  manual,
  initialData,
  refreshDeps,
  onSuccess,
  onError,
  formatResult,
  cacheKey,
  loadingDelay,
  defaultParams,
  pollingInterval,
  pollingWhenHidden,
  fetchKey,
  refreshOnWindowFocus,
  focusTimespan,
  debounceInterval,
  throttleInterval,
}); 
```

### Result

| Property | Description                                                                                                                                                                                                                                                             | Type                                                                    |
|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| data     | <ul><li> Data returned by the service。</li><li> If there is `formatResult`, the data is formatted data.</li></ul>                                                                                                                                                      | `undefined / any`                                                       |
| error    | exception thrown by service, default is `undefined`                                                                                                                                                                                                                     | `undefined / Error`                                                     |
| loading  | Whether the service is executing                                                                                                                                                                                                                                        | `boolean`                                                               |
| run      | Manually trigger service execution, and its parameters will be passed to service                                                                                                                                                                                        | `(...args: any[]) => Promise`                                           |
| params   | An array of parameters for the service being executed. For example, you triggered `run (1, 2, 3)`, then params is equal to [[1, 2, 3] `                                                                                          | `any[]`                              |
| cancel   | <ul><li>Cancel current request </li><li>If there is a poll, stop it. </li></ul>                                                                                                                                                                                         | `() => void`                                                            |
| refresh  | Using the last params, re-execute the service                                                                                                                                                                                                                           | `() => void`                                                            |
| mutate   | Modify data directly                                                                                                                                                                                                                                                    | `(newData) => void / ((oldData)=>newData) => void`                      |
| fetches  | <ul><li>By default, new requests overwrite old ones. If `fetchKey` is set, multiple requests can be implemented in parallel, and` fetches` stores the status of multiple requests.</li><li>The status of the outer layer is the newly triggered fetches data.</li></ul> | `{[key:string]: {loading,data,error,params,cancel,refresh,mutate,run}}` |

### Params

All Options are optional.

| Property             | Description                                                                                                                                                                                                                                                                                                                                                                                                | Type                                    | Default |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|---------|
| manual               | <ul><li> The default `false`. That is, the service is automatically executed during initialization.</li><li>If set to `true`, you need to call `run` manually to trigger execution. </li></ul>                                                                                                                                                                                                             | `boolean`                               | false   |
| initialData          | initial data                                                                                                                                                                                                                                                                                                                                                                                               | `any`                                   | -       |
| refreshDeps          | When `manual = false`,`refreshDeps` changes will trigger the service to re-execute                                                                                                                                                                                                                                                                                                                         | `any[]`                                 | `[]`    |
| formatResult         | Format request results                                                                                                                                                                                                                                                                                                                                                                                     | `(response: any) => any`                | -       |
| onSuccess            | <ul><li> Triggered when the service resolved, the parameters are `data` and` params` </li><li> If `formmatResult` is present,` data` is the formatted data.</li></ul>                                                                                                                                                                                                                                      | `(data: any, params: any[]) => void`    | -       |
| onError              | Triggered when the service reports an error. The parameters are `error` and` params`.                                                                                                                                                                                                                                                                                                                      | `(error: Error, params: any[]) => void` | -       |
| fetchKey             | According to params, get the key of the current request. After setting, we will maintain the request status of different key values ​​at the same time in fetches.                                                                                                                                                                                                                                             | `(...params: any[]) => string`          | -       |
| cacheKey             | <ul><li> Request a unique identifier. If `cacheKey` is set, we will enable the cache mechanism</li><li> We cache `data`,` error`, `params`,` loading` for each request </li><li> Under the cache mechanism, the same request will return the data in the cache first, and a new request will be sent behind the scene. After the new data is returned, the data update will be triggered again. </li></ul> | `string`                                | -       |
| defaultParams        | If `manual = false`, the default parameters when run is executed automatically,                                                                                                                                                                                                                                                                                                                            | `any[]`                                 | -       |
| loadingDelay         | Set delay time for display loading to avoid flicker                                                                                                                                                                                                                                                                                                                                                        | `number`                                | -       |
| pollingInterval      | Polling interval in milliseconds. After setting, it will enter polling mode and trigger `run` periodically.                                                                                                                                                                                                                                                                                                | `number`                                | -       |
| pollingWhenHidden    | <ul><li> Whether to continue polling when the page is hidden. Default is `true`, that is, polling will not stop </li><li> If set to `false`, polling is temporarily stopped when the page is hidden, and the last polling is continued when the page is redisplayed      </li></ul>                                                                                                                        | `boolean`                               | `true`  |
| refreshOnWindowFocus | <ul><li> Whether to re-initiate the request when the screen refocus or revisible. The default is `false`, which means the request will not be re-initiated. </li><li>If set to `true`, the request will be re-initiated when the screen is refocused or revisible.</li></ul>                                                                                                                               | `boolean`                               | `false` |
| focusTimespan        | <ul><li>  If the request is re-initiated every time, it is not good. We need to have a time interval. In the current time interval, the request will not be re-initiated. </li><li> Needs to be used with refreshOnWindowFocus. </li></ul>                                                                                                                                                                 | `number`                                | `5000`  |
| debounceInterval     | debounce interval, the unit is millisecond. After setting, request to enter debounce mode.                                                                                                                                                                                                                                                                                                                 | `number`                                | -       |
| throttleInterval     | throttle interval, the unit is millisecond. After setting, request to enter throttle mode.                                                                                                                                                                                                                                                                                                                 | `number`                                | -       |

## Advanced usage

Based on the basic useRequest, we can further encapsulate and implement more advanced customization requirements. Currently useRequest has three scenarios: `Integrated Request Library`,` Pagination` and `Load More`. You can refer to the code to implement your own encapsulation. Refer to the implementation of [useRequest](./src/useRequest.ts)、[usePaginated](./src/usePaginated.ts)、[useLoadMore](./src/useLoadMore.ts) 的实现。

### Integration Request Library

If service is `string`,` object`, `(... args) => string | object`, we will automatically use [umi-request] (https://github.com/umijs/umi-request/blob /master/README_zh-CN.md) to send network requests. umi-request is a request library similar to axios and fetch.

```javascript
// Usage 1
const { data, error, loading } = useRequest('/api/userInfo');

// Usage 2
const { data, error, loading } = useRequest({
  url: '/api/changeUsername',
  method: 'post',
});

// Usage 3
const { data, error, loading } = useRequest((userId)=> `/api/userInfo/${userId}`);

// Usage 4
const { loading, run } = useRequest((username) => ({
  url: '/api/changeUsername',
  method: 'post',
  data: { username },
}), {
  manual: true,
});
```

<code src="./demo/demo13.tsx" />

<br>

<code src="./demo/demo14.tsx" />

#### API

```typescript
const {...} = useRequest<R>(
  service: string | object | ((...args:any) => string | object), 
  {
    ...,
    requestMehod?: (service) => Promise
  })
```

#### Service

If service is `string`,` object`, `(... args) => string | object`, then automatically use` umi-request` to send the request.

#### Params

| Property      | Description                                                                                                                                                                         | Type                                   | Default |
|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------|---------|
| requestMethod | Asynchronous request method, the parameters are the service or parameters returned by service. If this parameter is set, this function is used to send network requests by default. | `(service: string/object)) => Promise` | -       |

### Pagination

By setting `options.paginated = true`, useRequest will run in pagination mode, which will have the following characteristics:

- useRequest will automatically manage the `current`,` pageSize`. The first parameter of service is `{current, pageSize}`.
- The data structure returned by service must be `{list: Item [], total: number}`. If it is not satisfied, it can be converted once by `options.formatResult`.
- Additional pagination field will be returned, which contains all pagination information and functions for manipulating pagination.
- The `refreshDeps` change will reset` current` to the first page and re-initiate the request. Generally you can put the pagination dependent conditions here.

<code src="./demo/demo15.tsx" />

<br>

<code src="./demo/demo16.tsx" />

<br>

<code src="./demo/demo17.tsx" />

#### API

```javascript
const {
  ...,
  pagination: {
    current: number;
    pageSize: number;
    total: number;
    totalPage: number;
    onChange: (current: number, pageSize: number) => void;
    changeCurrent: (current: number) => void;
    changePageSize: (pageSize: number) => void;
  };
  tableProps: {
    dataSource: Item[];
    loading: boolean;
    onChange: (
      pagination: PaginationConfig,
      filters?: Record<keyof Item, string[]>,
      sorter?: SorterResult<Item>,
    ) => void;
    pagination: {
      current: number;
      pageSize: number;
      total: number;
    };
  };

  sorter?: SorterResult<Item>;
  filters?: Record<keyof Item, string[]>;
} = useRequest(service, {
  ...,
  paginated,
  defaultPageSize,
  refreshDeps,
}); 
```

#### Result
| Property   | Description                                                                                                                                  | Type |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------|------|
| pagination | Paging data and methods for operating paging                                                                                                 | -    |
| tableProps | The data structure of the [antd Table] (https://ant.design/components/table-cn/) component can be used directly on the AntD Table component. | -    |
| sorter     | antd Table sorter                                                                                                                            | -    |
| filters    | antd Table filters                                                                                                                           | -    |

#### Params

| Property        | Description                                                                                                                                                                                                                                                          | Type     | Default |
|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|------|
| paginated       | <ul> <li> If set to `true`, paging mode is turned on. In paging mode, the first parameter of service is `{curret, pageSize, sorter, filters}` </li> <li> The service response result or `formatResult` result must be` {list: Item [], total: number} `. </li> </ul> |
| `boolean`       | false                                                                                                                                                                                                                                                                |
| defaultPageSize | default each page size                                                                                                                                                                                                                                               | `number` | `10` |
| refreshDeps     | In pagination mode, changing refreshDeps will reset current to the first page and re-initiate the request. Generally you can put the dependent conditions here.                                                                                                      | `any[]`  | `[]` |

### Load More

By setting `options.loadMore = true`, useRequest will run in loadMore mode, which will have the following characteristics:

- The data structure returned by the service must contain `{list: Item [], nextId: string | undefined}`, if it is not satisfied, it can be converted once by `options.formatResult`.
- useRequest automatically manages list data. The first parameter of service is nextId.
- Will return `result.loadingMore` and` result.loadMore` additionally.
- The `refreshDeps` change will clear the current data and re-initiate the request. Generally, you can put the conditions that loadMore depends on here.

<code src="./demo/demo18.tsx" />

#### API

```javascript
const {
  ...,
  loadMore,
  loadingMore,
} = useRequest(service, {
  ...,
  loadMore,
  refreshDeps,
}); 
```

#### Result
| Property    | Description | Type |
|-------------|--|--|
| loadMore    | Trigger load more |
| `()=>void`  |
| loadingMore | Is loading more |
| `boolean`   |

#### Params

| Property    | Description                                                                                                                                                                                                                                                                  | Type    | Default |
|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|------|
| loadMore    | <ul> <li> Enable loading more mode </li> <li> If set to `true`, enable loading more mode. In this mode, the first parameter of service is `nextId` </li> <li> The response result or` formatResult` result must be `{list: Item [], nextId: string / undefined}` </li> </ul> |
| `boolean`   | false                                                                                                                                                                                                                                                                        |
| refreshDeps | When loading more modes, `refreshDeps` changes, it will clear the current data and re-initiate the request. Generally you can put the dependent conditions here.                                                                                                             | `any[]` | `[]` |

## Global configuration

### UseAPIProvider
You can set global options at the outermost level of the project via `UseAPIProvider`.

```javascript
import {UseAPIProvider} from '@umijs/use-request';

export function ({children})=>{
  return (
    <UseAPIProvider value={{
      refreshOnWindowFocus: true,
      requestMethod: (param)=> axios(param),
      ...
    }}>
      {children}
    </UseAPIProvider>
  )
}
```

## Thanks
- [zeit/swr](https://github.com/zeit/swr)
- [tannerlinsley/react-query](https://github.com/tannerlinsley/react-query)