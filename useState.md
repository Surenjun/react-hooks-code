
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

```text
    useState的3个流程 hook本身是个链表 通过.next往下找 如果用while循环找 就没这个问题

    1.mountState 
        const [state,setState] = useState(0)
        if: const [state,setState] = useState(0)
        const [state,setState] = useState(0)
        
        默认值是functino，执行function，得到初始的state
        state是存放在memoizedState
        新建一个queue
        将queue传递给dispatch
        返回默认值和dispatch

    2.dispatchAction 
        创建一个update
        update添加到quene里
        空闲的时候：提前计算出新的state，保存在eagerState
        最后调用一次scheduleUpdateOnFiber，进入schedule，触发function重新执行一次

    3.updateState  这个阶段就是通过.next往下找
        const [state,setState] = useState(0)
        //第一个取顶层的
        currentlyRenderingFiber.memoizedState
        worknInprogresssHook.next
          if: const [state,setState] = useState(0)
        worknInprogresssHook.next
        const [state,setState] = useState(0)

        递归执行queue里的update
        在LayoutEffects里调用commitHookEffectListMount方法
```
