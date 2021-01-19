
```javascript
    //1.简易版 只能定义一个state 没有上下文 只能在一个函数里使用
    let state
    const useState = (defaultState)=>{
    
        const setState = (newState)=>{
            state = newState;
            schedule()
        }
    
        if(!state){
            state = defaultState
        }
        return [state,setState]
    }
```

```javascript
    //精版
    let contextStack = []
    const useState = (defaultStatus)=>{
        const context = contextStack[contextStack.length - 1];
        const nu = context.nu++;
    
        const {states} = context;
        const setState = (newState)=>{
            states[nu] = newState
        }
    
        if(!states[nu]){
            states[nu] = defaultStatus
        }
        return [states[nu],setState]
    }
    
    const withState = (func)=>{
        const states = {};
        //高阶做一层
        return (...args)=>{
            //保留上下文
            contextStack.push({nu:0,states});
            const result = func(...args);
            //用完后直接清除
            contextStack.pop();
            return result
        }
    }
    
    const function1 = withState(
        function render(){
            const [state,setState] = useState(0);
            const [name,setName] = useState("name");
            render1();
            console.log('render',state);
            setState(state + 1)
        }
    )
```

```
    useState的3个流程 hook本身是个链表 通过.next往下找 如果用while循环找 就没这个问题

    1.mountState 
        const [state,setState] = useState(0)
        if: const [state,setState] = useState(0)
        const [state,setState] = useState(0)

    2.dispathAction 

    3.updateState  这个阶段就是通过.next往下找
        const [state,setState] = useState(0)
        //第一个取顶层的
        currentlyRenderingFiber.memoizedState
        worknInprogresssHook.next
          if: const [state,setState] = useState(0)
        worknInprogresssHook.next
        const [state,setState] = useState(0)
```
