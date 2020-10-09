## Router-传参方式  

[官方 WEB Router文档](https://reactrouter.com/web/guides/quick-start)  

我也尝试弄清楚 {% em type="green" %}Query{% endem %} 和 {% em type="green" %}Params{% endem %} 的深层语义，但我查找了很多资料也没有找到合适的答案，也许是姿势不对 :wink: :wink:，期待得到你的答案。接下来只能留下我自己的理解。  
  
React-Router和Vue-Router有一定的差异。  
  
>  在实际业务当中，路由带参常用于无非两种场景，React-Router同Vue-Router一样，提供了标签及函数式跳转，如下： 

-   标签式跳转携参,&nbsp;&nbsp;例：```<Link to='xxx'>click</Link>```
-   函数跳转并携参,&nbsp;&nbsp;例：```class组件 this.props.history.push(xxx) Or Hooks组件 history.push(xxxx)```  

#### 显式传参
路由格式： xxxx?name=yourname&key=value  
***优势***：  
1. 用户刷新后,地址栏参数仍然存在

***劣势***：  
1. 安全（私密）性差，虽然我感觉仅靠客户端的安全都是胡扯 :speak_no_evil: :speak_no_evil:
2. 参数过多时，url长度和阅读度上都极差
3. url本身为string，所以仅支持值类型数据，且解析后都会转换为string

```js
  // 类组件
  improt React from 'react';
  import { Link } from 'react-router-dom'
  export default class XXX extend React.Component{
    ……
    jump = () => {
      this.props.history.push(`xxxx?name=${yourname}&key=${value}`)
    }
    render(){
      retrun(
        <>
          // 标签跳转
          <Link to='xxxx?name=yourname&key=value'>jump</Link>
          // 函数跳转
          <button onClick={ ()=>this.jump() }>jump</button>
        </>
      )
    }
  }

  // 获取参数 该方式需额外处理 url解析
  this.props.location.search
  ```

  ```js
  // Hooks
  improt React from 'react';
  import { Link, useHistory } from 'react-router-dom'

  export default function XXXX(){
    const history = useHistory();
    return(
      <>
          // 标签跳转
          <Link to='xxxx?name=yourname&key=value'>jump</Link>
          // 函数跳转
          <button onClick={ ()=> history.push(`xxxx?name=${yourname}&key=${value}`) }>jump</button>
      </>
    )
  }

  // 获取参数
  import { useLocation } from 'react-router-dom'

  const querys = useLocation().search 
  // Or
  const location = useLocation()
  const querys = location.search
```

#### 隐式传参
路由格式： xxxx

**隐式 query & params 传参**：

***优势***：  
1. 安全（私密）性略微提升, :rage2: :rage2:
2. 无论参数传递多少，Url地址栏更优雅
3. 参数解析为Object，可通过Object.key的方式直接获取

***劣势***：  
1. 用户刷新后, 数据存在丢失问题

```js
  // Hooks
  improt React from 'react';
  import { Link, useHistory } from 'react-router-dom'

  export default function XXXX(){
    const history = useHistory();
    return(
      <>
          // 标签跳转
          <Link to={ {pathname: '/xxxx', query:{ name: 'yourname', key: value }} }>jump</Link>
          <Link to={ {pathname: '/xxxx', params:{ name: 'yourname', key: value }} }>jump</Link>
          // 函数跳转
          <button onClick={ () => history.push({ pathname: '/xxxx', query:{ name: 'yourname', key: value } }) }>jump</button>
          <button onClick={ () => history.push({ pathname: '/xxxx', params:{ name: 'yourname', key: value } }) }>jump</button>
      </>
    )
  }

  //获取参数
  import { useLocation } from 'react-router-dom'

  const location = useLocation()
  const querys = location.query  // { name: yourname, key: value }
  const params = location.params  // { name: yourname, key: value }
```

**隐式 state 传参**：

***优势***：  
1. 安全（私密）性略微提升, :rage2: :rage2:
2. 无论参数传递多少，Url地址栏更优雅
3. 参数解析为Object，可通过Object.key的方式直接获取
4. 用户刷新后，数据仍然存在（圈重点）


***劣势***：  
1. 暂时还没有发现有啥缺点

```js
  // Hooks
  improt React from 'react';
  import { Link, useHistory } from 'react-router-dom'

  export default function XXXX(){
    const history = useHistory();
    return(
      <>
          // 标签跳转
          <Link to={ {pathname: '/xxxx', state:{ name: 'yourname', key: value }} }>jump</Link>
          // 函数跳转
          <button onClick={ () => history.push({ pathname: '/xxxx', state:{ name: 'yourname', key: value } }) }>jump</button>
      </>
    )
  }

  //获取参数
  import { useLocation } from 'react-router-dom'

  const location = useLocation()
  const states = location.state  // { name: yourname, key: value }
```



#### 动态路由
格式： xxxx/2
算显式还是隐式呢。 :see_no_evil: :see_no_evil:

```js
  // router.js
  <Route path='/path/:id' component={ Component }/>
  
  // Hooks
  improt React from 'react';
  import { Link, useHistory } from 'react-router-dom'

  export default function XXXX(){
    const history = useHistory();
    const id = 666;
    return(
      <>
          // 标签跳转
          <Link to={ {pathname: `/xxxx/${ id } `} }>jump</Link>
          // 函数跳转
          <button onClick={ () => history.push({pathname: `/xxxx/${ id } `}) }>jump</button>
      </>
    )
  }

  //获取参数
  import { useParams } from 'react-router-dom'

  const params = useParams()  // { id: 666 }
```

#### 写在最后
曾看到 V3 和 V4 版本差异较大，但毕竟我现在面的 V5.2.x版本，故而也没再去查阅版本差异。  

Router传参的方式仅不限于此，刚开始接触React，还是会按照Vue当中的思路来考虑在React该如何实现，慢慢尝试着转变吧，双向借鉴，及早跳出思维固局。