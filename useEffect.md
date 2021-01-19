```javascript
    //简版
    const useEffect = (callback,depArray = [])=>{
        const hasNoDeps = !depArray;
        const deps = memoizedState[cursor];
        const hasChangedDeps = deps
            ? !depArray.every((el,i)=> el === deps[i])
            : true
        if(hasNoDeps || hasChangedDeps){
            callback();
            memoizedState[cursor] = depArray;
        }
        cursor++
    }
```