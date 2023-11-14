## 10. 불필요한 Props 복사 및 연산

- 불필요하게 props를 상태로 만들지 말고 바로 사용하도록 한다

```jsx
// 불필요한 Props 복사
// as-is
function CopyProps({ value }) {
  const [copyValue] = useState(value);

  return <div>{copyValue}</div>;
}

// to-be
function CopyProps({ value }) {
  return <div>{copyValue}</div>;
}
```

- 하지만 useState를 사용해서 상태로 관리하는 게 더 나은 경우도 있는데

- 값비싼 연산이 있는 경우에는 변수에 할당하는 대신 아래처럼 state로 초기화해주면 컴포넌트가 렌더링 될 때 마다 다시 계산해줄 필요가 없다

```jsx
function CopyProps({ value }) {
  const [copyValue] = useState(값비싸고_무거운_연산(value));

  return <div>{copyValue}</div>;
}
```

- 하지만 이러한 경우에도 굳이 상태로 관리할 필요 없이 useMemo를 사용해주면 된다

```jsx
function CopyProps({ value }) {
  const [copyValue] = useMemo(() => 값비싸고_무거운_연산(value), [value]);

  return <div>{copyValue}</div>;
}
```

- 그리고 가장 베스트는 이렇게 props로 내려오기 전에 연산이 된 다음에 내려오는 것이 좋다

## 11. Curly Braces

- 리액트에서 코드를 작성할 때 아래처럼 속성에 curly braces를 사용해서 전달해주기도 한다

```js
function CurlyBraces() {
  return (
    <header
      className={"clean-header"}
      id={"clean-code"}
      style={{
        backgroundColor: "red",
        width: 100,
      }}
    ></header>
  );
}
```

- 하지만 curly braces가 필요 없는 경우가 있다

- 아래처럼 만약 문자열이 전달되는 경우에는 curly braces를 생략하는 편인데 어찿피 문자열이기 때문이다.

```js
function CurlyBraces() {
  return <header className="clean-header" id="clean-code"></header>;
}
```

- 하지만 객체 또는 값이 계산되어서 전달되는 경우라면 아래처럼 curly braces가 들어가야 한다

```js
function CurlyBraces() {
  return (
    <header
      className="clean-header"
      id="clean-code"
      value={isLogin && hasCoookie}
      style={{
        backgroundColor: "red",
        width: 100,
      }}
    ></header>
  );
}
```

- 그리고 아래처럼 double curly braces 경우에는 일반적인 객체를 넣었다고 생각주면 된다

```js
function CurlyBraces() {
  return (
    <header
      className="clean-header"
      id="clean-code"
      value={isLogin && hasCoookie}
    ></header>
  );
}
```

- 요약하자면 문자열의 경우에는 curly braces를 사용하지 않고 문자열 그대로 전달해주면 JSX에서 입장에서는 문자열만 전달된다는 것을 알 수 있고

- 동료들이 볼 때도 문자열인 것을 알 수 있다.

- 그리고 curly braces는 변수나 숫자, boolean, 객체에 해당하는 경우에만 사용하면 된다.

- 하지만 curly braces에 문자열을 넣어주면 코드를 볼 때 혼동을 줄 수 있기 때문에 문자열이 전달될 때는 curly braces를 사용하지 않는 편이 좋다.

- prettier와 같은 도구를 사용하면 이러한 문제를 쉽게 해결할 수 있다.

## 12. Shorthand Props

- props를 하위 컴포넌트로 전달해줄 때 spread 연산자를 사용해서 자식 컴포넌트로 전달할 수 있다.

- 이렇게 해주면 똑같은 props를 전달할 때 `props.속서이름`과 같이 일일이 지정하지 않아도 된다

```js
function ShorthandeProps(props) {
  return (
    <header
      className="clean-header"
      id="clean-code"
      isDarkMode={true}
      isFixed={true}
      isAdmin={true}
    >
    <ChildComponent {...props}>
    </header>
  );
}
```

- Shorthand Props를 사용하면 다음과 같이 boolean을 전달할 때 항상 true인 값을 전달한다면 `={true}` 부분을 생략할 수 있다.

```js
function ShorthandeProps({isDarkMode, isFixed, isAdmin }) {
  return (
    <header
      className="clean-header"
      id="clean-code"
      isDarkMode
      isFixed
      isAdmin
    >
    <ChildComponent {...props}>
    </header>
  );
}
```

## 13. Single Quotes vs Double Quotes

- 언제 Single Quotes 그리고 언제 Double Quotes 사용할지에 대한 논쟁 분분하지만 아래처럼 사용하면 될 것 같다.

1. 팀에서 일관성을 갖춘 규칙이 있고 일관성을 지키기 위해서 노력하면 된다

2. HTML, JavaScript에서 차이를 두는 방식도 있다

```jsx
function HelloWorld() {
  const obj = {
    hello: 'world',
  };

  return (
    <div>
      {/* good, 일반적으로 html은 double quotes를 많이 사용하므로 */}
      <a href="http://www.naver.com"></a>

      {/* bad, single과 double이 혼용되고 있다. */}
      <input class='ccc' type="button">

      {/* bad, 위의  자바스크립트 obj에서는 single을 사용했는데 아래 자바스크립트 표현식에서는 double quotes를 사용하고 있다 */}
      <Clean style={{backgroundPosition:"left"}}>
    </div>
  );
}
```

- 이러한 것들은 프리티어를 사용하면 쉽게 규칙을 정하고 적용해줄 수 있다.

- [eslint - quotes](https://eslint.org/docs/latest/rules/quotes)에 보면 quotes에 대한 설정 확인할 수 있다.

- 결론적으로, 규칙을 정하고 그 맥락을 파악하고 공유하는게 좋다

- 그리고 lint, 포맷팅 도구(Prettier)에 위임하도록 하자

## 14. 알아두면 좋은 Props 네이밍

```js
function PropsNaming() {
  // bad
  return (
    <ChildComponent
      classname="clean-header"
      Clean="code"
      clean_code="clean-code"
      otherComponent={OtherComponnetn}
      isFixed={true}
    />
  );
}
```

- 아래처럼 코드를 변경해주어야 한다

```js
function PropsNaming() {
  // bad
  return (
    <ChildComponent
      className="clean-header" // 카멜 케이스로 변경
      clean="code" // 문자열이 전달되므로
      cleanCode="clean-code" // props는 key-value로 되어있는 객체 이므로 카멜케이스로 수정해주도록 한다
      OtherComponent={OtherComponnetn} // 컴포넌트는 대문자로 전달을 해준다
      isFixed // 무조건 true인 경우에는 shorthand property를 사용하도록 한다
    />
  );
}
```

## 15. inline style 주의하기

```jsx
// 아래는 틀린 코드
function InlineStyle() {
  return (
    <button style="background-color: 'red'; font-size:'14px'">
      Clean Code
    </button>
  );
}

// js이므로 아래처럼 camelCase로 바꾸어주어야 한다
// 그리고 key-value이므로 객체로 감싸주어야 한다

// 객체를 이렇게 넣어주면 렌더링 될 때 마다 비교하기 때문에 좋지 못하다
// 그리고 아래 스타일을 변하지 않는다

function InlineStyle() {
  return (
    <button style={{ backgroundColor: "red", fontSize: "14px" }}>
      Clean Code
    </button>
  );
}

// 그러면 아래처럼 컴포넌트 밖으로 빼주어도 된다
const myStyle = { backgroundColor: "red", fontSize: "14px" };

function InlineStyle() {
  return <button style={myStyle}>Clean Code</button>;
}

// 이런식으로 다른 스타일을 적용할 수 도 있다.
const myStyle = {
  warning: { backgroundColor: "yellow", fontSize: "14px" },
  danger: { backgroundColor: "red", fontSize: "14px" },
};

function InlineStyle() {
  return (
    <>
      <button style={myStyle.warning}>Warning Clean Code</button>
      <button style={myStyle.danger}>Danger Clean Code</button>
    </>
  );
}
```

## 16. HTML Attributes 주의하기

- HTML 기본 속성 주의하기

- prop들이 HTML 기본 예약어와 겹치는지 주의할 것

```jsx
function HTMLDefaultAttribute() {
  const MyButton = ({ children, ...rest }) => (
    // rest로 className, type과 같은 예약어들을 전달하고 있다.
    <button {...rest}>{children}</button>
  );

  return (
    <>
      <MyButton className="mt-0" type="submit">
        Clean Code
      </MyButton>

      <input type='number' maxLength='99'>
    </>
  );
}
```
