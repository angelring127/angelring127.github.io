---
title: React Hook
author: Hoya
date: 2019-12-03 20:53:00 +0800
categories: [Blogging, react]
tags: [typography]
math: true
---

# React Hook

> Hook은 v16.8에 새로 도입된 기능, 함수형 컴포넌트에서도 상태 관리를 가능캐 하는 useState, 렌더링 직후 작업을 설정하는 useEffect 등의 기능을 제공 한다 최근에는 Hook 과 react-redux 를 이용하는 기능이 많이 지고 있다. 



## 1. useState

> Hook의 가장 기본적인 기능으로서, 함수형 컴포넌트를 가변적인 상태를 지니고 있을 수 있게 도와준다. 

- Counter.js

  ```react
  import React, { useState } from 'react';
  // useState 
  // @param : 상태의 기본값
  // @return : array => [ 원소 상태 값, 상태를 설정하는 함수]
  const Counter = () => {
    const [value, setValue] = useState(0);
    return (
      <div>
        <p>
          현재 카운터 값은 <b>{value}</b> 입니다.
        </p>
        <button onClick={() => setValue(value + 1)}>+1</button>
        <button onClick={() => setValue(value - 1)}>-1</button>
      </div>
      );
  };
  ```

  



## e. useEffect

> useEffect는 react component가 렌더링 될 때마다 특정 작업을 수행하도록 도와주는 Hook입니다.
> Class component의 componentDidMount 와 componentDidUpdate를 합친 형태로 보아도 무방합니다.

```react
import React, { useState, useEffect } from 'react';

const Info = () => {
    const [name, setName] = useState('');
    const [nickname, setNickname] = useState('');

    useEffect(() => {
        console.log('렌더링이 완료되었습니다.');
        console.log({
            name,
            nickname
        });
    }, [name]);

    const onChangeName = e => {
        setName(e.target.value);
    }

    const onChangeNickname = e => {
        setNickname(e.target.value);
    }

    return (
        <div>
            <div>
                <input value={name} onChange={onChangeName} />
                <input value={nickname} onChange={onChangeNickname} />
            </div>
            <div>
                <b>이름: </b> {name}
            </div>
            <div>
                <b>닉네임: </b> {nickname}
            </div>
        </div>
    );
}

export default Info;
```



#### 2.1 마운트 될 때만 실행하고 싶을 때

	>컴포넌트가 화면에 가장 처음 렌더링 될 때만 실행되고 업데이트 할 경우에는 실행 할 필요가 없는 경우엔 함수의 두번째 파라미터를 비어있는 배열을`[]`넣어주면 됩니다.

```react
useEffect(() => {
  console.log('마운트 될 때만 실행됩니다.');
}, []);
```



#### 2.2 특정 값이 업데이트 될 때만 실행하고 싶을 때

> 두번째 파라미터 배열안에 검사하고 싶은 값을 넣으면 됩니다.

```react
useEfect(() => {
  console.log(name);
},[name]);
```



#### 2.3 뒷정리 하기

> useEffect는 기본적으로 렌더링 되고난 직후마다 실행, 두번째 파라미터 배열에 무엇을 넣느냐에 따라 실행되는 조건이 변함
> 만약 컴포넌트가 언마운트되기 전이나, 업데이트 되기 직전에 어떠한 작업을 수행하고싶다면 useEffect에서 뒷정리(cleanup) 함수를 반환해주면 된다.



##### Info.js useEffect

```react
useEffect(() => {
  console.log('effect');
  console.log(name);
  return () => {
    console.log('cleanup');
    console.log(name);
  }; // 두번째 파라미터에 빈배열을 넣는경우 언마운트 될 때만 cleanup함수가 호출, 
  	// 배열에 특정 변수를 넣을경우 언마운트와 특정 변수의 업데이트 직전 cleanup 호출
});
```

##### App.js

```react
import React, { useState } from 'react';
import logo from './logo.svg';
import * as Component from './component';
import './App.css';

function App() {
  const [visible, setVisible] = useState(false);
  return (
    <div>
      <button
        onClick={() => {
          setVisible(!visible);
        }}
      >
        {visible ? '숨기기' : '보이기'}
      </button>
      <br />
      {visible && <Component.Info />}
      <Component.Counter />
    </div>
  );
}

export default App;
```



## 3. useContext

> 이 Hook 을 사용하면 함수형 컴포넌트에서 Context 를 보다 더 쉽게 사용 할 수 있다.



Context.js

```react
import React, { createContext, useContext } from 'react'

const ThemeContext = createContext('black');
const ContextSample = () => {
  const thene = useContext(ThemeContext);
  const style = {
    width: '24px',
    height: '24px',
    background : theme
  };
  return <div style={style} />;
};

export default ContextSample;
```





## 4. useReducer

> useReducer 는 useState 보다 컴포넌트에서 더 다양한 상황에 따라 다양한 상태를 다른 값으로 업데이트해주고 싶을 때 사용하는 Hook 입니다.
>
> Reducer는 Current, Update를 위해 필요한 정보를 담은 액션(action) 값을 전달 받아 새로운 상태를 반환하는 함수 **Reducer함수에서는 새로운 상태를 만들 때 꼭 불변성을 지켜야합니다.**

```react
function reducer(state, action) {
  return { ... }; // 불변성을 지키면서 업데이트한 새로운 상태를 반환합니다.
```

```react
{
  type: 'INCREMENT',
}
```

Redux 에서는 액션 객체에는 어떤 액션인지 알려주는 type 필드가 꼭 있어야 하지만, useReducer에서 사용하는 액션 객체는 꼭 type 를 지니고 있을 필요가 없습니다. 심지어, 객체가 아니라 문자열이나, 숫자여도 상관이 없습니다.



#### 4.1 카운터 구현하기

먼저, 기존의 Counter 컴포넌트를 useReducer 를 사용하여 구현해봅시다.



CounterUseReducer.js

```react
import React, { useReducer } from 'react';

function reducer(state, action) {
  // action.type 에 따라 다른 작업 수행
  switch (action.type) {
    case 'INCREMENT' :
      return { value : state.value + 1 };
    case 'DECREMENT' :
      return { value : state.value - 1 };
    default :
    	// 아무것도 해당되지 않을 때 기존 상태 반환
      return state;
  }
}

const CounterUseReducer = () => {
  // state 현재 value 
  const [state, dispatch] = useReducer(reducer, { value: 0 });
  
  return (
    <div>
      <p>
        현재 카운터 값은 <b>{state.value}</b>
      </p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>+1</button>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>-1</button>
    </div>
    );
};

export default CounterUseReducer;
        
```

useReducer 의 첫번째 param는 리듀서 함수(reducer), 두번째 param는 해당 리듀서의 기본 값을 넣습니다. 이 Hook 을 사용 했을 때에는 state 값과  dispatch 함수를 받아오게 됩니다. state 는 현재 상태, dispatch 는 액션을 발생시키는 함수 입니다. dispatch(action) 와 같은 형태로, 함수 안에 param로 액션 값을 넣어주면 리듀서 함수가 호출되는 구조 입니다.

useReducer 을 사용했을 때의 가장 큰 장점은 컴포넌트 업데이트 로직을 컴포넌트 바깥으로 빼낼 수 있다는 점 입니다.



## 4.2 인풋 상태 관리하기

> useReducer 를 사용하여 Info 컴포넌트에서 인풋 상태를 관리해보겠습니다.

>useReducer 를 사용한다면 기존에 클래스형 컴포넌트에서 input 태그에 name 값을 할당하고 e.target.name 을 참조하여 setState 를 해준 것과 유사한 방식으로 작업을 처리 할 수 있습니다.



#### InfoUseReducer

```react
import React, { useReducer } from 'react';

function reducer(state, action) {
    return {
        ...state,
        [action.name]: action.value
    };
}

const InfoUseReducer = () => {
    const [state, dispatch] = useReducer(reducer, {
        name: '',
        nickname: ''
    });
    const { name, nickname } = state;
    const onChange = e => {
        dispatch(e.target);
    };

    return (
        <div>
            <div>
                <input name="name" value={name} onChange={onChange} />
                <input name="nickname" value={nickname} onChange={onChange} />
            </div>
            <div>
                <div>
                    <b>이름:</b> {name}
                </div>
                <div>
                    <b>닉네임: </b>
                    {nickname}
                </div>
            </div>
        </div>
    );
} 

export default InfoUseReducer;
```

> useReducer 에서의 액션은 그 어떤 값이 되어도 됩니다. 그래서 이번에 우리는 이벤트 객체가 지니고있는 e.target 값 자체를 액션 값으로 사용하였습니다.
>
> 이런 식으로 인풋을 관리하면, 아무리 인풋의 개수가 많아져도 코드를 짧고 깔끔하게 유지 할 수 있습니다.





## 5. useMemo

> useMemo 를 사용하면 함수형 컴포넌트 내부에서 발생하는 연산을 최적화 할 수 있습니다.  리스트에 숫자를 추가하면서 해당 숫자들의 평균을 나타내는 함수형 컴포넌트를 작성해봅시다. 



#### Average.js

```react
import React, { useState, useMemo } from 'react';

const getAverage = numbers => {
    console.log('평균값 계산중....');
    if (numbers.length === 0) return 0;
    const sum = numbers.reduce((a, b)=> a + b);
    return sum / numbers.length;
};

const Average = () => {
    const [list, setList] = useState([]);
    const [number, setNumber] = useState('');

    const onChange = e => {
        setNumber(e.target.value);
    };
    const onInsert = () => {
        const nextList = list.concat(parseInt(number));
        setList(nextList);
        setNumber('');
    };

    const avg = useMemo(() => getAverage(list), [list]);

    return (
        <div>
            <input value={number} onChange={onChange} />
            <button onClick={onInsert}>등록</button>
            <ul>
                {list.map((value, index) => (<li key={index}>{value}</li>))}
            </ul>
            <div>
                <b>평균 값:</b> {avg}
            </div>
        </div>
    );
}

export default Average;
```

> `const avg = useMemo(() => getAverage(list), [list]);`부분 덕분에 list 배열의 내용이 바뀔 때에만 getAverage 함수가 호출되도록 할 수 있습니다.



## 6. useCallback

> useCallback 은 useMemo와 상당히 비슷한 함수입니다. useCallback 은 주로 렌더링 성능을 최적화해야 하는 상황에서 사용됩니다. 이 Hook을 사용하면 이벤트 핸들러 함수를 필요할 때만 생성 할 수 있습니다.
> 우리가 5. useMemo 구현한 Average 컴포넌트를 보면 onChange, onInsert 라는 함수를 선언해주었습니다. 이렇게 선언을 하게 되면 컴포넌트가 리렌더링 될 때마다 이 함수들이 새로 생성됩니다. 대부분의 경우에는 이러한 방식이 문제가 되지 않지만, 컴포넌트의 렌더링이 자주 발생하거나 렌더링 해야할 컴포넌트의 개수가 많아진다면 이 부분을 최적화 해주시는 것이 좋습니다.
>
> useCallback 를 사용하여 최적화를 해봅시다.

#### Average.js

```react
import React, { useState, useMemo, useCallback } from 'react';

const getAverage = numbers => {
    console.log('평균값 계산중....');
    if (numbers.length === 0) return 0;
    const sum = numbers.reduce((a, b)=> a + b);
    return sum / numbers.length;
};

const Average = () => {
    const [list, setList] = useState([]);
    const [number, setNumber] = useState('');

    const onChange = useCallback(e => {
        setNumber(e.target.value);
    },[]); // 컴포넌트가 처음 렌더링 될 때만 함수 생성 

    const onInsert = useCallback(
        e => {
            const nextList = list.concat(parseInt(number));
            setList(nextList);
            setNumber('');
        },
        [number, list]
    ); // number 혹은 list 가 바뀌었을 때만 함수 생성 

    // @params EventHandler 최적화 시킬 이벤트
    // @params any 관찰 대상 값
    const avg = useMemo(() => getAverage(list), [list]);

    return (
        <div>
            <input value={number} onChange={onChange} />
            <button onClick={onInsert}>등록</button>
            <ul>
                {list.map((value, index) => (<li key={index}>{value}</li>))}
            </ul>
            <div>
                <b>평균 값:</b> {avg}
            </div>
        </div>
    );
}

export default Average;
```



> useCallback 의 첫번째 파라미터에는 우리가 생성해주고 싶은 함수를 넣어주고, 두번째 파라미터에는 배열을 넣어주면 되는데 이 배열에는 어떤 값이 바뀌었을 때 함수를 새로 생성해주어야 하는지 명시해주어야 합니다.
>
> 만약에 onChange 처럼 비어있는 배열을 넣게 되면 컴포넌트가 렌더링 될 때 단 한번만 함수가 생성되며, onInsert 처럼 배열 안에 number, list 를 넣게 되면 input 내용이 바뀌거나 새로운 항목이 추가 될 때 마다 함수가 생성됩니다. 
>
> 함수 내부에서 기존의 상태 값을 의존해야 할 때는 꼭 두번째 파라미터 안에 포함을 시켜주어야 합니다. 

>참고로 useMemo, useCallback 의 차이를 보여주는 코드 입니다.
>
>```react
>useCallback(() => {
>  console.log('hello world');
>}, [])
>
>useMemo(() => {
>  const fn = () => {
>    console.log('hello world');
>  }
>  return fn;
>}, [])
>```
>
>위의 두함수는 똑같은 기능을 하는 함수 입니다.
>useCallback 은 결국 useMemo 의 함수를 반환하는 상황에서 좀 더 편하게 사용 할 수 있는 Hook입니다. 
>
>useMemo : 숫자, 문자열, 객체 처럼 일반 값을 재사용하기 위해서 사용
>useCallback : 함수를 재사용 하기 위해서 사용



## 7. useRef

> useRef Hook 은 함수형 컴포넌트에서 ref 를 쉽게 사용 할 수 있도록 도와줍니다. Average 컴포넌트에서 등록 버튼을 눌렀을 때 포커스가 인풋 쪽으로 넘어가도록 코드를 작성 해보겠습니다.

####  7.1. Ref

​	 간단하게 말하면 javaScript 의 ID를 대신 하여 사용된다.

> 일반적인 데이터 플로우에서 벗어나 직접적으로 자식을 수정해야 하는 경우도 가끔씩 있습니다. 수정할 자식은 React 컴포넌트의 인스턴스일 수도 있고, DOM 엘리멘트일 수도 있습니다. React의  Ref는 두 셩우 모두를 위한 해결책을 제공합니다 - [React Site](https://ko.reactjs.org/docs/refs-and-the-dom.html)



#### Average.js

```react
import React, { useState, useMemo, useCallback, useRef } from 'react';

const getAverage = numbers => {
    console.log('평균값 계산중....');
    if (numbers.length === 0) return 0;
    const sum = numbers.reduce((a, b)=> a + b);
    return sum / numbers.length;
};

const Average = () => {
    const [list, setList] = useState([]);
    const [number, setNumber] = useState('');
    const inputEl = useRef(null);

    const onChange = useCallback(e => {
        setNumber(e.target.value);
    },[]); // 컴포넌트가 처음 렌더링 될 때만 함수 생성 

    const onInsert = useCallback(
        e => {
            console.log(typeof parseInt(number));
            if (!isNaN(parseInt(number))) {
                const nextList = list.concat(parseInt(number));
                setList(nextList);
            }
            setNumber('');
            inputEl.current.focus();
        },
        [number, list]
    ); // number 혹은 list 가 바뀌었을 때만 함수 생성 

    // @params EventHandler 최적화 시킬 이벤트
    // @params any 관찰 대상 값
    const avg = useMemo(() => getAverage(list), [list]);

    return (
        <div>
            <input value={number} onChange={onChange} ref={inputEl}/>
            <button onClick={onInsert}>등록</button>
            <ul>
                {list.map((value, index) => (<li key={index}>{value}</li>))}
            </ul>
            <div>
                <b>평균 값:</b> {avg}
            </div>
        </div>
    );
}

export default Average;
```

> useRef 를 사용하여 ref 를 설정 `ref={inputEl}`,  `inputEl`객체 안의 current 값이 실제 엘리멘트`input`를 가리키게 됩니다.

#### 7.2 로컬 변수 사용하기

> 추가적으로, 컴포넌트 로컬 변수를 사용해야 할 때도 useRef 를 활용 할 수 있습니다. 여기서 로컬 변수라 함은, 렌더링이랑은 관계 없이 바뀔 수 있는 값을 의미합니다. 만약에 클래스 형태의 컴포넌트로 따지자면 다음 괕은 코드 입니다.

```react
import React, { Component } from 'react';

Class MyComponent extends Component {
  id = 1;
  setId = (n) => {
    this.id = n;
  }
  printId = () => {
    console.log(this.id);
  }
  render() {
    return (
      <div>
        MyCompoent
      </div>
    );
  }    
};

export default MyComponent
```

이러한 코드를 만약에 함수형 컴포넌트로 작성한다면 다음과 같이 작성 될 수 있습니다.



```react
import React, { useRef } from 'react'

const RefSamepl = () => {
  const id = useRef(1);
  const setId = (n) => {
    id.current = n;
  };
  
  const printId = () => {
    console.log(id.current);
  };
  return {
    <div>
      refSample
    </div>
  };
};

export default RefSample;
```

주의 하실 점은, 이렇게 넣은 ref 안의 값은 바뀌어도 컴포넌트가 렌더링 되지 않는다는 점입니다. 렌더링과 관련 되지 않는 값을 관리 할 때만 이러한 방식으로 코드를 작성해주세요



## 8. 커스텀 Hooks 만들기

> 만약에 여러 컴포넌트에서 비슷한 기능을 공유하게 되는 일이 발생한다면 이를 우리들만의 Hook 을 작성하여 로직을 재사용 할 수 있습니다.



#### 8.1 useInputs

> 한번 우리가 기존에 Info 컴포넌트에서 여러개의 Input을 관리하기 위하여 useReducer 로 해결했던 로직을 useInputs 라는 Hook 으로 따로 분리 해보겠습니다.

#### useInputs.js

```react
import { useReducer } from 'react';

function reducer(state, action) {
    return {
        ...state,
        [action.name]: action.value
    };
}

export default function useInputs(initialForm) {
    const [state, dispatch] = useReducer(reducer, initialForm);
    const onChange = e => {
        dispatch(e.target);
    };
    return [state, onChange];
}
```

#### InfoUseInput.js

```react
import React from 'react';
import useInputs from './useInputs';

const InfoUseInput = () => {
  const [state, onChange] = useInputs({
    name: '',
    nickname: ''
  });
  const { name, nickname } = state;

  return (
    <div>
      <div>
        <input name="name" value={name} onChange={onChange} />
        <input name="nickname" value={nickname} onChange={onChange} />
      </div>
      <div>
        <div>
          <b>이름:</b> {name}
        </div>
        <div>
          <b>닉네임:</b> {nickname}
        </div>
      </div>
    </div>
  );
};

export default InfoUseInput;
```



#### 8.2 usePromise

> 이번에는 함수형 컴포넌트에서 Promise 를 더 쉽게 사용 할 수 있는 Hook 을 만들어봅시다.

usePromise.js

```react
import { useState, useEffect } from 'react';

export default function usePromise(promiseCreator, deps) {
  const [resolved, setResolved] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  const process = async () => {
    setLoading(true);
    try {
      const result = await promiseCreator();
      setResolved(result);
    } catch (e) {
      setError(e);
    }
    setLoading(false);
  };

  useEffect(() => {
    process();
  }, deps);

  return [loading, resolved, error];
}
```





출처 : [velopert](https://velog.io/@velopert/react-hooks#3.-usecontext)