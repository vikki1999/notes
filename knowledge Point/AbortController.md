### 在项目中加入中断fetch请求操作
#### model文件
```
const controller = new AbortController;
const { signal } = controller;


* fetch ({payload}, {call}) {
  try {
    yield call(APIFunction.fetch, payload, signal); //将信号一同传过去
  } catch (e) {
    if (e.name === 'AbortError') {
      //终止之后的操作
    }
  }
}


controller.abort(); //中断请求
```
#### index.js service文件
```
const gen = param => {
  ...
  return function (data, signal, headers, config) {
    return http[method](url, data, signal, headrs, config);
  };
};
```

#### http.js
```
async get(api, data = {}, signal, headers = {}, config = {}) {
  return request(`${api}${query}`, signal, headrs ,config);
}

async request(url, signal = null, init, headers = {}, config = {}) {
  if(signal) {
    options.signal = signal;
  }
  
  try{
    let response = await fetch(url, options);
    return response;
  } catch (e) {
    if (e.name === 'AbortError) {
      console.log('abortError');
      throw e;
    }
  }
}
```

### 相关资料
[AbortController](https://developer.mozilla.org/zh-CN/docs/Web/API/FetchController)

[fetch请求](https://www.cnblogs.com/qq3279338858/p/11057795.html)
