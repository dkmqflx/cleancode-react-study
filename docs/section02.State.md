## 06. 업데이트 되지 않는 값

```jsx
export const component = () => {

  // 아래 객체는 외부로 추출한다!
  const INFO = {
    a: "b",
    b: "b",
  };

  return <MyComp info={Info}>
};
```

- 아래는 변수를 리액트 컴포넌틐에 직접적으로 사용하는 경우이다.

```tsx
function NoUdpateValue(): Element {
  // 업데이트가 되지 않는 일반적인 객체이다.
  const INFO = {
    name: "kim",
    value: "Clean",
  };

  const [count, setCount] = useState(0);

  const onIncrement = (): any => setCount((prevCount): any => prevCount);
  const onDecrement = (): any => setCount((prevCount): any => prevCount);

  return (
    <div>
      <ShowCOunt info={INFO}>
      <ButtonGroup onIncrement={onIncrement} onDecrement={onDecrement} >
    </div>
  )
}
```

- 코드에 있는 INFO 객체와 같은 경우, 상수를 다루거나 일반적으로 방치하게 되는 경우 이렇게 많이 사용되는데

- 이 객체는 어디서도 덥데이트가 되지 않고 있다.

- 즉, 속성을 바꾸는 경우가 존재 하지 않는다

- 또한 `ShowCOunt` 컴포넌틐에 하나의 props로 객체를 내리고 있다.

- 이런 경우 좋지 못한 케이스인데, 리액트 컴포넌트 내부에 있기 때문에 매번 렌더링 될 때 마다 같은 값이더라도 또 다시 참조해서 트리거 해야하기 때문이다.

- 그렇지만 계산해야 될 시기와 기억해야 될 시기에 대한 로직이 전혀 들어가 있지 않다

- 그러니까 불필요하게 참조하고 물고 있는 것이다.

- 그렇기 때문에 이런 경우에는 아래 코드처럼 아예 외부로 내보내준다

```tsx

const INFO = {
    name: "kim",
    value: "Clean",
  };

function NoUdpateValue(): Element {

  const [count, setCount] = useState(0);

  const onIncrement = (): any => setCount((prevCount): any => prevCount);
  const onDecrement = (): any => setCount((prevCount): any => prevCount);

  return (
    <div>
      <ShowCOunt info={INFO}>
      <ButtonGroup onIncrement={onIncrement} onDecrement={onDecrement} >
    </div>
  )
}
```

## 07. 플래그 상태로 바꾸기

- 플래그 값

  - 프로그래밍에서 주로 특정 조건 혹은 제어를 위한 조건을 불리언으로 나타내는 값

- useState 대신 **플래그로 상태를 정의할 수 있다**

```jsx
// as-is
export const component = () => {
  const [isLogin, setIsLogin] = useState(false);

  useEffect(() => {
    if (a) {
      setIsLogin(true);
    }
    if (b) {
      setIsLogin(true);
    }
  }, [a, b]);
};

// to-be

export const component = () => {
  const isLogin = a || b;
};
```

- 아래는 로그인 예시로 수 많은 값을 조건으로 분기한다.

```js
export const component = () => {
  const [isLogin, setIsLogin] = useState(false);

  // useEffect를 사용해서 이런식으로 관리하는 경우가 많다.
  useEffect(() => {
    if (hasToken) {
      setIsLogin(true);
    }
    if (hasCookie) {
      setIsLogin(true);
    }
    if (isValidCookie) {
      setIsLogin(true);
    }
  }, [hasToken, hasCookie, isValidCookie]);

  return <div>{isLogin && "Hi"}</div>;
};
```

- 아래처럼 하나의 조건으로 처리를 해주는 것이 더 깔끔하다.

- 이렇게 해주면 매 렌더링 될 때 마다 판단이 되기 때문에 굳이 상태가 필요 없어진다

- 즉, 이렇게 플래그 변수를 선언 해줄 수 있다.

```js
export const component = () => {
  const isLogin = hasToken && hasCookie && isValidCookie;
  // 위 예시 기준으로 하면 || 아닌가 ?

  return <div>{isLogin && "Hi"}</div>;
};
```

## 08. 불필요한 상태 제거하기

- 불필요한 상태를 만든다면 ?

  - 결국에는 리액트에 의해 관리되는 값이 늘어나는 것

  - 그러다보면 렌더링에 영향을 주는 값이 늘어나서 관리 포인트가 더더욱 늘어난다

- props를 useState에 넣지 말고 바로 return 문에 사용하기

  - 아래 코드처럼 불필요한 useState에 넣지말고, to-be 케이스의 경우처럼 필요한 곳에서 바로 사용하라는 뜻인 듯?

  ```jsx
  return <ul>
    {
      complUserList.filter((user) => user.title);
    }
  </ul>;
  ```

- 컴포넌트 내부 변수는 렌더링마다 고유한 값을 가짐

- 따라서 useState가 아닌 const로 상태를 선언하는 게 좋은 경우도 있음

```jsx
// as-is
export const Component = () => {
  const [userList, setUserList] = useState(MOCK_DATA); // 초기 상태 저장
  const [complUserList, setComplUserList] = useState(MOCK_DATA); // 변경 후 상태 저장

  useEffect(() => {
    const newList = complUserList.filter((user) => user.completed === true);

    setUserList(newList);
  }, [userList]);
};

// to-be, 이렇게 내부의 변수로 사용한다
// 리액트 컴포넌트에서 변수는 렌더링 마다 고유의 값을 가지는 계산된 값 (Computed Value / 공식적인 용어는 아님)
const complUserList = complUserList.filter((user) => user.completed === true);
```

## 09. 리렌더링 방지가 필요하다면 useState 대신 useRef

- 리렌더링 방지가 필요하다면 useState 대신 useRef

- 한번 설정된 값을 계속 컴포넌트 내부에서 사용한다면 useState를 사용할 필요가 없다

- 즉, 컴포넌트의 전체적인 수명과 동일하게 지속적인 정보를 일관적으로 제공하는 경우

- 리액트 개발진은 isMount 같은 것을 사용하지 말라고 한다

  - [isMounted is an Antipattern](https://ko.legacy.reactjs.org/blog/2015/12/16/ismounted-antipattern.html)

  - 이전에는 마운트를 체크할 수 있는 그런 속성도 있었지만 없앤 상황이다

- 즉, useState 대신 useRef를 사용하면 컴포넌트 생명주기와 동일한 리렌더링되지 않는 상태를 만들 수 있다.

```jsx
// as-is
export const Component = () => {
  const [isMount, setIsMount] = useState(false);

  // 불필요한 리렌더링을 유발할 수 있다.
  useEffect(() => {
    if (!isMount) {
      setIsMount(true);
    }
  }, [isMount]);

  console.log("isMount", isMount);

  // isMount false
  // isMount true
};

// to-be, 이렇게 내부의 변수로 사용한다
export const Component = () => {
  const isMount = useRef(false);

  // 불필요한 리렌더링을 방지할 수 있다.
  useEffect(() => {
    isMount.current = true;

    return () => (isMount.current = false);
    // 만약 eslint에서 경고를 띄워준다면, 이 한줄은 eslint rule을 disable 할 것 같다.
  }, []);

  console.log("isMount", isMount);

  // isMount {current: false}
  // isMount {current: true}
};
```
