## JSX语法

xml和js的结合,js语句使用{}包裹

### 渲染列表

使用数组的map()方法

```jsx
const list = [
 {id:1001,name:'vue'},
 {id:1002,name:'react'},
 {id:1003,name:'angular'}
]
function App() {
  return (
    <div className="App">
    <ul>
      {list.map(item=> <li key={item.id}>{item.name}</li>)}
    </ul>
    </div>
  );
}
```

### 条件渲染

逻辑与运算符或者三元表达式

```jsx
{flag && <span>...</span>}
{flag ? <span>aaa</span> : <span>...</span>}
```

## React基础事件绑定

```jsx
function App() {
  const handleClick = (e)=>{
      //e为事件参数,若第二种方式调用则为自定义参数
    console.log('button被点击了',e);
  }
  const handleClick = (name,e)=>{
      //e为事件参数,name为自定义参数
    console.log('button被点击了',name,e);
  }
  return (
    <div className="App">
      <button onClick={handleClick}> button</button>
      <button onClick={()=>handleClick("hhh")}> button2</button>
      <button onClick={(e)=>handleClick2("hhh",e)}> button3</button>
    </div>
  );
}

```

## React组件

概念：界面的一部分，可以嵌套、复用

在React中，一个组件就是一个**首字母大写**的函数，内部存放逻辑和视图ui，渲染时同html标签一样使用

### 定义组件

```jsx
function Button(){
  return <button>click me!</button>
}
const Button2 = ()=>{
  return <button>click me!!</button>
}
```

## useState使用

Hook函数，状态变量控制视图的渲染(状态驱动视图)

```jsx
function App() {
  const handleClick = ()=>{
    setCount(count +1)
  }
  const [count,setCount] = useState(0)
  return (
    <div className="App">
      <button onClick={handleClick}>{count}</button>
    </div>
  );
}
```

### 状态修改规则

在React中，状态被认为是只读的，我们应该替换它而不是修改它，直接修改状态不能引发视图更新

## 组件样式控制

行内

```jsx
<span style={{color:'red',fontSize:'50px'}}>...</span>
```

class类名控制

```jsx
import './xxx.css'
<span className='foo'>...</span>
```

## React中获取Dom

1.使用useRef创建ref对象，并与jsx绑定

```jsx
const inputRef = useRef(null)
<input type="text" ref={inputRef}
```

2.在Dom可用时，通过input.current拿到Dom对象

```jsx
 const inputRef = useRef(null)
 const showDom = ()=>{
      console.dir(inputRef.current);
  }
  return (
    <div className="App">
      <input type='text'  ref={inputRef}/>
      <button onClick={showDom}>获取dom</button>
    </div>
  );
```

## 组件间通信

### 1.父传子，子组件通过props接收

```js
function App(){
	return(<Son name={'ggg'}>)
}
function Son(props){
    console.log(props.name)    
}
```

特殊的props: 当内容嵌套在子组件的标签中时，该内容作为props的children字段传递

```jsx
function App(){
	return <Son> <span>hhh</span> </Son>
}
function Son(props){
    //props.children即为：<span>hhh</span>
    return <div> props.children</div>
}
```

### 2.子传父

在子组件中调用父组件的函数并传递参数

### 3.使用状态提升实现兄弟组件通信

A组件传递数据给父组件的state，父组件再传递给B组件

### 4.使用Context跨层级通信

```jsx
const MsgContext = createContext();

function App(){
    const msg = 'this is app msg';
    return (
    	<div>
        	<MsgContext.Provider value = {msg}>
            	this is App
                <A/>
            </MsgContext.Provider>
        </div>
    )
}

function A(){
    return <div>this is A <B/> </div>
}
function B(){
    const msg = useContext(MsgContext);
    return <div>this is B {msg} </div>
}
```

## useEffect的使用

useEffect(function,args)

组件渲染完毕后，会调用一次该函数

```jsx
useEffect(()=>{},[])
```

args:依赖项

没有args时：组件渲染完毕执行一次，每次组件更新再次执行

args为[]空数组时：只会在组件第一次渲染完毕后执行一次

args为特定依赖项(state)时：组件渲染完毕执行一次，依赖项变化时再次执行

清除副作用：

```jsx
useEffect(()=>{
    const timer = setInterval=(()=>{
    console.log('定时器执行中')
	},1000)
    return ()=>{
        //组件卸载时会执行此函数
        clearInterval(timer)
    }
},[])
```

## 自定义Hook函数

