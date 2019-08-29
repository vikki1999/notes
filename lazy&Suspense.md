### React.lazy() 和 Suspense

React.lazy 用于做Code-Splitting，代码拆分。类似于按需加载，渲染的时候才加载代码。
本质是：在组件渲染的时候，再去加载包含引入路径的组件。等待组件加载的时候，暂停渲染。

##### 用法：

```
import React, {lazy} from 'react';
const OtherComponent = lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <OtherComponent />
    </div>
  );
}
```

##### lazy(() => import('./OtherComponent'))使用es6的import()返回一个promise，类似于：

```
lazy(() => new Promise(resolve =>
  setTimeout(() =>
    resolve(
      // 模拟ES Module
      {
        // 模拟export default 
        default: function render() {
          return <div>Other Component</div>
        }
      }
    ),
    3000
  )
));
```

React.lazy的提出是一种更优雅的条件渲染解决方案。

之所以说他更优雅，是因为他将条件渲染的优化提升到了框架层。

这里我们引出suspense。

当我们组件未渲染完成，需要loading时，可以这么写：

```
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```
在我们的业务场景中，OtherComponent可以代表多个条件渲染组件，我们全部加载完成才取消loding。

只要promise没执行到resolve，suspense都会返回fallback中的loading。

代码简洁，loading可提升至祖先组件，易聚合。相当优雅的解决了条件渲染。

##### 相关链接：
[react v16.6 动态 import，React.lazy()、Suspense、Error boundaries](http://www.ptbird.cn/react-lazy-suspense-error-boundaries.html#menu_index_2)
[React新特性实例详解（memo、lazy、suspense、hooks）](http://react-china.org/t/react-memo-lazy-suspense-hooks/28789)
