Router 是开发 React 应用的必备功能，那 React Router 是怎么实现的呢？

今天我们就来读一下 React Router 的源码吧！

首先，我们来学一下 History API，这是基础。

什么是 history 呢？

就是这个东西：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/42a07668b5a04a98846f7e07c74f93f4~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=546&h=354&s=67131&e=png&b=2b2c2f)

我打开了一个新的标签页、然后访问 baidu.com、sougou.com、taobao.com。

长按后退按钮，就会列出历史记录，这就是 history。

现在在这里：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c386c26a93e24dceb11ae48559a8a998~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=578&h=592&s=30135&e=png&b=fdfdfd)

history.length 是 5

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ead77bfba912402dadcc569525c7faaa~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=586&h=598&s=58664&e=png&b=eae8eb)

点击两次后退按钮，或者执行两次 history.back()

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/38851702298a42e49100a33290dc471d~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1376&h=1058&s=608311&e=gif&f=47&b=faf8f8)

就会回到这里：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5809d049b4194a94b00f572b9668cb3f~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=566&h=574&s=30420&e=png&b=fcfcfc)

这时候 history.length 依然是 5

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a9de2c4ecce3446e9c4e300d0994d957~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=544&h=572&s=38740&e=png&b=fefefe)

因为前后的 history 都还保留着：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/48601f171ca24d50a6b8519a4b21c30a~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=452&h=230&s=40938&e=png&b=2c2d30)

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0373372777ce406a94bd9dbd690c6d15~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=484&h=238&s=45339&e=png&b=2b2c2f)

除了用 history.back、history.forward 在 history 之间切换外，还可以用 history.go

参数值是 delta：

history.go(0) 是刷新当前页面。

history.go(1) 是前进一个，相当于 history.forward()

history.go(-1) 是后退一个，相当于 history.back()

当然，你还可以 history.go(-2)、histroy.go(3) 这种。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/60e66d581bf2419c91007831e3aaf2f4~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1036&h=826&s=557850&e=gif&f=39&b=fcf8f7)

比如当我执行 history.go(-2) 的时候，能直接从 taobao.com 跳到 sogou.com

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7c406baaddc14c4991c3feb1c8456506~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=634&h=614&s=31798&e=png&b=fdfdfd)

你还可以通过 history.replaceState 来替换当前 history：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5531dddacb1b4662b48720a555d42f64~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=900&h=564&s=69903&e=png&b=fefefe)

```javascript
history.replaceState({aaa:1}, '', 'https://www.baidu.com?wd=光')
```

第一个参数是 state、第二个参数是 title，第三个是替换的 url。

不过第二个参数基本都不支持，state 倒是能拿到。

比如我在 [www.baidu.com](https://www.baidu.com "https://www.baidu.com") 那页 replaceState 为一个新的 url：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5d38115dfadd4da79fd9a0fef238c907~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1090&h=722&s=130590&e=png&b=ffffff)

前后 history 都没变，只有当前的变了：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9f84e5e294f94ab5831d9570cd3f8f77~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=950&h=438&s=129216&e=gif&f=44&b=fefefe)

也就是这样：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/93480a25c8114ffd890125d6976c015d~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=582&h=578&s=33075&e=png&b=faf1ed)

当然，你还可以用 history.pushState 来添加一个新的 history：

```javascript
history.pushState({bbb:1}, '', 'https://www.baidu.com?wd=东');
```

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2c13a75eb6544e09b432d6ff2dec0e15~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=900&h=644&s=76261&e=png&b=fefefe)

但有个现象，就是之后的 history 都没了：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/725dee6a3ec04a65b04c8b0b2692ed0d~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=636&h=242&s=52691&e=png&b=2b2c2f)

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/17f2fa076a7b4a6da4134ce4201225ee~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=750&h=354&s=423243&e=gif&f=59&b=fefefe)

也就是变成了这样：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/83d4fac1164642e5b9aee039c9cb9eaf~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=550&h=380&s=23961&e=png&b=fcfbfb)

为什么呢？

因为你是 history.pushState 的时候，和后面的 history 冲突了，也就是分叉了：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c66631ed532749ae94f755e933b4d8b3~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1322&h=624&s=59559&e=png&b=fdfafa)

这时候自然只能保留一个分支，也就是最新的那个。

这时候 history.length 就是 3 了。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/976417f421cf4cfb90b75e8375f67c40~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=858&h=994&s=157772&e=png&b=ffffff)

至此，history 的 length、go、back、forward、pushState、replaceState、state 这些 api 我们就用了一遍了。

还有个 history.scrollRestoration 是用来保留滚动位置的：

有两个值 auto、manual，默认是 auto，也就是会自动定位到上次滚动位置，设置为 manual 就不会了。

比如我访问百度到了这个位置：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e66e585bd14345cd8fbaecb02b585588~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1780&h=1612&s=385234&e=png&b=fefefe)

打开个新页面，再退回来：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/81c76946a45145ce9f6348bc44670e7f~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1886&h=1560&s=1561585&e=gif&f=47&b=fcfcfc)

依然是在上次滚动到的位置。

这是因为它的 history.scrollRestoration 是 auto

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ec369d0350f2435081bcbc514372e5c2~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=940&h=912&s=179855&e=png&b=fefefe)

我们把它设置为 manual 试试看：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/84ce6448fa4845e9a23e00c444a17c32~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=560&h=230&s=25973&e=png&b=fefefe)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d97fab9b8fbd481ebe295d63cd00a3a5~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1720&h=1526&s=2772694&e=gif&f=44&b=fbfbfb)

这时候就算滚动到了底部，再切回来也会回到最上面。

此外，与 history 相关的还有个事件：popstate

**当你在 history 中导航时，popstate 就会触发，比如 history.forwad、histroy.back、history.go。**

**但是 history.pushState、history.replaceState 这种并不会触发 popstate。**

我们测试下：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6adc5718fa754eb3905db01e118d1750~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=898&h=656&s=84337&e=png&b=fefefe)

```javascript
history.pushState({aaa:1}, '', 'https://www.baidu.com?#/aaa');

history.pushState({bbb:2}, '', 'https://www.baidu.com?#/bbb');
```

我在 [www.baidu.com](http://www.baidu.com "http://www.baidu.com") 这个页面 pushState 添加了两个 history。

加上导航页一共 4 个：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5e5869ab2a2b43e385ad504a6184d50a~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=550&h=280&s=57001&e=png&b=2a2b2e)

然后我监听 popstate 事件：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/59b5b18b623540a59d6f21532758584d~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=966&h=568&s=70993&e=png&b=fefefe)

```javascript
window.addEventListener('popstate', event => {console.log(event)});
```

执行 history.back 和 history.forward 都会触发 popstate 事件：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a96a0345ac7e4794947af160605c4a94~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1472&h=388&s=82019&e=png&b=fefefe)

事件包含 state，也可以从 target.location 拿到当前 url

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/14b32daa37ab417db6a4eca10331555f~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=820&h=938&s=182991&e=png&b=fefefe)

但是当你 history.pushState、history.replaceState 并不会触发它：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a0a77efaaecb4ccd92063736a2f960b1~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=946&h=306&s=63315&e=png&b=fffdfd)

也就是说**添加、修改 history 不会触发 popstate，只有在 history 之间导航才会触发。**

综上，history api 和 popstate 事件我们都过了一遍。

基于这些就可以实现 React Router。

有的同学说，不是还有个 hashchange 事件么？

确实，那个就是监听 hash 变化的。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/72990f6c1e68433eb9d49d8ec10c42f7~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1322&h=962&s=421516&e=gif&f=38&b=fcfcfc)

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1ce77c29812f4ad2b55baa4a62049439~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=632&h=538&s=106927&e=png&b=ffffff)

基于它也可以实现 router，但很明显，hashchange 只能监听 hash 的变化，而 popstate 不只是 hash 变化，功能更多。

所以用 popstate 事件就足够了。

其实在 react router 里，就只用到了 popstate 事件，没用到 hashchange 事件：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7b72fcf6830940e48b3b5a3b091ae479~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=682&h=584&s=50382&e=png&b=1f1f1f)

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/869c225b643842d1a8fb773b6e992c59~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=582&h=530&s=34946&e=png&b=1f1f1f)

接下来我们就具体来看下 React Router 是怎么实现的吧。

创建个 react 项目：

```lua
npx create-react-app react-router-test
```

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2975b19b12964ad78ec861256b329b2b~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=974&h=218&s=67804&e=png&b=010101)

安装 react-router 的包：

```
npm install react-router-dom
```

然后在 index.js 写如下代码：

```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import {
  createBrowserRouter,
  Link,
  Outlet,
  RouterProvider,
} from "react-router-dom";

function Aaa() {
  return <div>
    <p>aaa</p>
    <Link to={'/bbb/111'}>to bbb</Link>
    <br/>
    <Link to={'/ccc'}>to ccc</Link>
    <br/>
    <Outlet/>
  </div>;
}

function Bbb() {
  return 'bbb';
}

function Ccc() {
  return 'ccc';
}

function ErrorPage() {
  return 'error';
}

const routes = [
  {
    path: "/",
    element: <Aaa/>,
    errorElement: <ErrorPage/>,
    children: [
      {
        path: "bbb/:id",
        element: <Bbb />,
      },
      {
        path: "ccc",
        element: <Ccc />,
      }    
    ],
  }
];
const router = createBrowserRouter(routes);

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<RouterProvider router={router} />);
```

通过 react-router-dom 包的 createBrowserRouter 创建 router，传入 routes 配置。

然后把 router 传入 RouterProvider。

有一个根路由 /、两个子路由 /bbb/:id 和 /ccc

把开发服务跑起来：

```arduino
npm run start
```

测试下：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/40b9b376320747ae8da5eef83e5c6b43~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=788&h=470&s=95350&e=gif&f=32&b=fdfdfd)

子路由对应的组件在 <Outlet/> 处渲染。

当没有对应路由的时候，会返回错误页面：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/22938755e032405bb192b579294f3ea5~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=648&h=254&s=17382&e=png&b=ffffff)

那它是怎么实现的呢？

我们断点调试下：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/62d534c7c24a40fcae719b0455897702~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=810&h=570&s=73493&e=png&b=191919)

创建调试配置文件 launch.json，然后创建 chrome 类型的调试配置：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/63ff3adf3f054976b54babde161c8b05~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=984&h=588&s=104982&e=png&b=1f1f1f)

在 createBrowserRouter 的地方打个断点：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0b8de2f3b7604d5484ef383bc40131a8~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=974&h=882&s=107841&e=png&b=1f1f1f)

点击 debug：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2047dcfbb0194d1f85bbc6250f3265cb~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=772&h=508&s=34568&e=png&b=191919)

代码会在这里断住：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4aa8c88cc84547e0940b6d54256bc8ed~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1884&h=1102&s=327096&e=png&b=1d1d1d)

点击 step into 进入函数内部：

它调用了 createRouter：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/834ae2622494470b9183f0233808c647~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=956&h=576&s=105743&e=png&b=1f1f1f)

这里传入了 history。

这个不是原生的 history api，而是包装了一层之后的：

关注 listen、push、replace、go 这 4 个方法就好了：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/769530049c7a4f8aa11392b2e4b38b05~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1134&h=812&s=145275&e=png&b=1f1f1f)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d4751dd7b55d4696bb04c89e71dc04e5~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=548&h=506&s=60060&e=png&b=1f1f1f)

listen 就是监听 popstate 事件。

而 push、replace、go 都是对 history 的 api 的封装：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/79cc7fd4fceb4547ab975f226f165f3b~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1024&h=906&s=201003&e=png&b=1f1f1f)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/89acfeabb9204c4a82e77a4f06808fcd~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=932&h=370&s=89091&e=png&b=1f1f1f)

此外，history 还封装了 location 属性，不用自己从 window 取了。

然后 createRouter 里会对 routes 配置和当前的 location 做一次 match：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/57ac03407bd044a29a57d35b39a701a1~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1350&h=588&s=191043&e=png&b=202020)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3d398cb558bd4230a08d71c67e161446~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1224&h=286&s=90704&e=png&b=212121)

matchRoutes 会把嵌套路由拍平，然后和 location 匹配：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1331f9e6486d4475a19bd6de738c13d6~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1022&h=1116&s=254327&e=png&b=1f1f1f)

然后就匹配到了要渲染的组件以及它包含的子路由：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c54cbc07d74b40c088b608312082ae34~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=844&h=792&s=158682&e=png&b=212121)

这样当组件树渲染的时候，就知道渲染什么组件了：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ef6c603eeb6d47cebee75f31c0ee068e~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=982&h=1016&s=153094&e=png&b=202020)

就是把 match 的这个结果渲染出来。

这样就完成了路由对应的组件渲染：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c17014ac2ee04873b289e0ab767cf1b3~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=542&h=314&s=20628&e=png&b=ffffff)

也就是这样的流程：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/63628d1c38924740af562ae3e3ac5125~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1516&h=604&s=116878&e=png&b=fefcfc)

当点击 link 切换路由的时候：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b79930a0ff484beb8c0a445bf6209f33~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1084&h=946&s=132594&e=png&b=1f1f1f)

会执行 navigate 方法：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f773621a1d894bed897cb1dd63758a8b~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1060&h=538&s=110699&e=png&b=202020)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/643710d88a2e481dad8315782856f2e2~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=936&h=334&s=65925&e=png&b=1f1f1f)

然后又到了 matchRoutes 的流程：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0003ef65fe9a4b45b3bb2e23bf27b6fc~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1104&h=372&s=115211&e=png&b=212121)

match 完会 pushState 或者 replaceState 修改 history，然后更新 state：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f3e5a61e33cb462eba304bbb4eda1d08~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=872&h=668&s=132904&e=png&b=202020)

然后触发了 setState，组件树会重新渲染：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/54568a3f79034241bc2b41746455902d~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=926&h=302&s=56891&e=png&b=1f1f1f)

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8e7dbc25fd514d1d9bdebd24ca105311~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=944&h=620&s=134743&e=png&b=1f1f1f)

也就是这样的流程：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6b97c9824f7348dfaa9e6529666b9c48~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=2210&h=562&s=135638&e=png&b=fefcfc)

router.navigate 会传入新的 location，然后和 routes 做 match，找到匹配的路由。

之后会 pushState 修改 history，并且触发 react 的 setState 来重新渲染，重新渲染的时候通过 renderMatches 把当前 match 的组件渲染出来。

而渲染到 Outlet 的时候，会从 context 中取出当前需要渲染的组件来渲染：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/963b92c14eb140a2989fd889276c9625~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1088&h=158&s=34312&e=png&b=1f1f1f)

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dc527157d2a24cf080b06e370e07c9e2~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1182&h=602&s=150356&e=png&b=202020)

这就是 router 初次渲染和点击 link 时的渲染流程。

那点击前进后退按钮的时候呢？

这个就是监听 popstate，然后也做一次 navigate 就好了：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cb9bb0311d0e4b17b6aee47cdf85fdf2~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=986&h=670&s=110235&e=png&b=202020)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/07291c65d32340e5a5e8b19d5c9bfef2~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=914&h=338&s=59873&e=png&b=1f1f1f)

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f6ad52e831d04c5da6315009c9546fac~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=940&h=838&s=128050&e=png&b=1f1f1f)

后续流程一样。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/08d5adcfdea441a3a221002cfc278ecc~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=2040&h=692&s=136999&e=png&b=fffdfd)

回过头来，其实 react router 的 routes 其实支持这两种配置方式：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b114c52c3d7340608a1cc59965797d19~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=632&h=584&s=51664&e=png&b=e7f0e5)

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/99f12345a59a4cce975a9d7d8588aa2a~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=926&h=446&s=71620&e=png&b=f7f7f7)

效果一样。

看下源码就知道为什么了：

首先，这个 Route 组件就是个空组件，啥也没：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e6f843d1a6e44a57b6c597167973b3f2~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1098&h=254&s=54902&e=png&b=1f1f1f)

而 Routes 组件里会从把所有子组件的参数取出来，变成一个个 route 配置：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5cf9802f9ee74169b035f47616d8bde8~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=1002&h=228&s=43795&e=png&b=1f1f1f)

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3939646a01034aa6af9d7eca94061c17~tplv-k3u1fbpfcp-jj-mark:1600:0:0:0:q75.image#?w=794&h=1096&s=185726&e=png&b=1f1f1f)

结果不就是和对象的配置方式一样么？

## 总结

我们学习了 history api 和 React Router 的实现原理。

history api 有这些：

* length：history 的条数
* forward：前进一个
* back：后退一个
* go：前进或者后退 n 个
* pushState：添加一个 history
* replaceState：替换当前 history
* scrollRestoration：保存 scroll 位置，取值为 auto 或者 manual，manual 的话就要自己设置 scroll 位置了

而且还有 popstate 事件可以监听到 history.go、history.back、history.forward 的导航，拿到最新的 location。

这里要注意 pushState、replaceState 并不能触发 popstate 事件。也就是 history 之间导航（go、back、forward）可以触发 popstate，而修改 history （push、replace）不能触发。

React Router 就是基于这些 history api 实现的。

首次渲染的时候，会根据 location 和配置的 routes 做匹配，渲染匹配的组件。

之后点击 link 链接也会进行 location 和 routes 的匹配，然后 history.pushState 修改 history，之后通过 react 的 setState 触发重新渲染。

前进后退的时候，也就是执行 history.go、history.back、history.forward 的时候，会触发 popstate，这时候也是同样的处理，location 和 routes 的匹配，之后通过 react 的 setState 触发重新渲染。

渲染时会用到 Outlet组件 渲染子路由，用到 useXxx 来取一些匹配信息，这些都是通过 context 来传递的。

这就是 React Router 的实现原理，它和 history api 是密不可分的。