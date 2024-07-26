# react 学习笔记

## 安装

```md
npx create-react-app my-app
cd my-app
npm start
```

## 组件

```jsx
import React from 'react';

function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

export default Welcome;
```

## 状态

## 获取DOM元素和组件实例
  ### class组件
  ```jsx
    // 不推荐
    <div ref='box'></div>
    this.refs.box

    // 推荐
    box
    <div ref={ele => this.box = ref}></div>

    //推荐
    this.box = React.createRef()
    this.box.current // 获取dom
    <div ref={this.box}></div>

    let child = React.createRef()
    <Child ref={child}></Child>
    child.current // 获取子组件实例
  ```
  ### 函数组件
  > 函数组件没有实例this，不能使用上面的第一种方法  

  ```jsx 
  
    // 不推荐
    let box;
    <div ref={ele => box = ref}></div>


    const box = React.createRef()
    box.current // 获取dom
    <div ref={box}></div>


    
  ```


  ```jsx
  // 会报错，没有实例对象出来
    let child = React.createRef()
    <Child ref={child}></Child>
    
    // 基于forwardRef转发，获取的子组件内的某个DOM元素
    let child = useRef(null)
    <Child ref={child}></Child>

    const Child = React.forwardRef((props, ref) => {
      return <div ref={ref}></div>
    })

    // 获取子组件方法和状态
    const Parent = () => {
      let child  =  useRef(null)
      return <div>
        <Child ref={child}></Child>
      </div>
    }

    const Child = React.forwardRef((props, ref) => {
      const submit = () => {}
      useImperativeHandle(() => {
        //返回的就是需要传递的数据，父组件通过ref获取数据
        return {submit}
      })
      return <div></div>
    })
    
  ```
  ### hooks函数

  **useRef**
  ```jsx 
    let box = useRef(null)
    <div ref={box}></div>
  ```

  **useImperativeHandle**
  ```jsx
    // 获取子组件方法和状态
    const Parent = () => {
      let child  =  useRef(null)
      return <div>
        <Child ref={child}></Child>
      </div>
    }

    const Child = React.forwardRef((props, ref) => {
      const submit = () => {}
      useImperativeHandle(() => {
        //返回的就是需要传递的数据，父组件通过ref获取数据
        return {submit}
      })
      return <div></div>
    })
  ```


::: info 总结:
  createRef 可以在类组件和函数组件中使用，useRef只能在函数组件，函数使用useRef，类组件使用 createRef 节约性能
:::

::: warning tip
 在函数组件中useRef在组件更新时不会创建新的ref对象 createRef 在组件更新时会创建新的ref对象 useRef 节约性能
 在类组件中更新时只是重新执行reader函数，不会重新创建ref 
:::

## 其他hooks 函数

  ### useMemo

  ::: tip 语法
    useMemo(callback, [dependencies])
  :::

  ```jsx
    const Test = () => {
      const [num1, setNum1] = useState(0)
      const [num2, setNum2] = useState(0)
      const total = num1 + num2
      return (<div>
        {num1}
        {num2}
        {total}
        <button onClick={
          setNum1(num1 + 1)
        }> </button>
      </div>)
    }
  ```
  ```md
  上面看着时没问题的，但是组件更新时会重新执行函数，每一次都会执行num1+num2会导致性能问题。
  这地方很实用计算属性，依赖当num1 num2 发生改变执行计算
  ```
 

  ```jsx
  const Test = () => {
    const [num1, setNum1] = useState(0)
    const [num2, setNum2] = useState(0)
    const total = useMemo(() => {
      return num1 + num2
    }, [num1, num2])
    return (<div>
      {num1}
      {num2}
      {total}
      <button onClick={
        setNum1(num1 + 1)
      }> </button>
    </div>)
  }
  ```

  ### useCallback

  ::: tip 语法
    useCallback(callback, [dependencies])
  :::

  ```jsx
    const Test = () => {
      const [txt, setTxt] = useState('')
      // 需要进行防抖处理
      const getData = debounce(() => {
        fetch('')
      }, 2000) 
      return (<div>
        <input value={txt} onChange={(e) => {
          setText(e.target.value)
          getData()
        }}>
      </div>)
    }
  ```
  ::: tip 
  以为在onchange 函数中setTxt 会导致组件更新，防抖函数每次渲染重新生成，也就失去防抖功能. 使用了useCallback就不会重新生成防抖函数
  :::

  ```jsx
    const Test = () => {
      const [txt, setTxt] = useState('')
      // 需要进行防抖处理
      const getData = useCallback(debounce(() => {
        fetch('')
      }, 2000)) 
      return (<div>
        <input value={txt} onChange={(e) => {
          setText(e.target.value)
          getData()
        }}>
      </div>)
    }
  ```

::: warning tip
  父组件更新子组件会更新吗??????? react.memo()函数 pureComponent()
:::

## 自定义hooks
::: warning tip
  自定义hooks只能放在组件函数直接作用域下，不能放在if for 循环中，自定义hooks 必须以use开头
:::


## react 复合组件通讯方案
::: tip 复合组件
 兄弟 父子 祖先 后代 平行组件等
:::

### 父子组件通信
1. 基于属性
2. 修改数据方法，基于属性传给儿子，儿子调用方法
3. 基于插槽
4. 给儿子设置ref，获取儿子的组件实例

### 祖孙组件通讯
 #### 类组件
 通过content = react.createContext() 创建一个context对象，通过
 Provider「<content.provider value={}></content.provider>」依赖注入的方式传递给后代组件，通过content取出对应的值
   
   ```jsx 
   import TContent from '@/components/TContent'
   class V extends React.Component {
    static contentType = TContent
    render() {
      //content 存储注入的信息
      let {} = this.content
      return (
        <div>
        </div>
      )
    }
   }
   ```
   ```jsx 
   <content.consumer>
    {
      content => {
        //content 存储注入的信息
        return <div></div>
      }
    }
   </content.consumer>
   ```
  #### 函数组件
  ```jsx 
   <content.consumer>
    {
      content => {
        //content 存储注入的信息
        return <div></div>
      }
    }
   </content.consumer>
   ```
   ```jsx 
  const data = useContent(content)
   ```

 1. props
 2. context
 3. redux
 4. eventBus
 5. 发布订阅
 6. 全局变量
 7. 路由传参
 8. 本地存储
 9. 全局事件
  


## 样式私有化

> vue 实现原理，是在每个元素上添加一个data-v-xxxxx 属性，通过正则匹配到对应的类名，通过类名实现私有化
>


react 
 1. 行内样式
 2. 基于样式表，样式类
 3. css modules
    ```less
    // style.module.less
      .test {
        color: red;
      }
    ```
    替换了原有类名 
    text: "Style-Text-随机" 文件名-原有类名-hash
    ```jsx 
    import styles from './index.less'
     // text: "Style-Text-hash"

     function Text() {
      return (
        <div className={styles.test}>
          <span>{text}</span>
        </div>
      )
    }
    ```
    4. react-jss 
    ```jsx
    const useStyle = createUseStyles({
      test: {
        color: 'red'
      }
    })
    ```
    ```jsx
    const classes = useStyle()
    ```
    ```jsx
    <div className={classes.test}>
      <span>{text}</span>
    </div>
    ```
    5. 
::: tip 
  1. 命名空间
  2. 类名前缀
  3. 作用域插槽
  4. css in js
  5. css modules
  6. css 预处理器 
  7. 动态样式
  8. 动态类名
  9. 动态style
:::
