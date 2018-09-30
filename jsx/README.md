# JSX

```jsx
const element = <h1>Hello, world!</h1>;
```

이 재미있는 태그 구문은 문자열도 HTML도 아닙니다.  

JSX라고하며 JavaScript의 구문 확장입니다. UI가 어떻게 생겼는지 설명하기 위해 React와 함께 사용하는 것이 좋습니다. JSX는 템플릿 언어를 생각 나게 할 수도 있지만 JavaScript의 모든 기능을 제공합니다.  

JSX로 작성하면 React에서 사용하는 elements 객체를 생성합니다.  

## JSX Transform

```jsx
// JSX 문법으로 작성

ReactDOM.render(
  <div className="App">
    <header className="App-header">
      <h1 className="App-title">Welcome to React</h1>
    </header>
    <p className="App-intro">I Love React</p>
  </div>,
  document.getElementById('root')
);
```

위 코드의 JSX 문법은 자바스크립트 문법이 아니기 때문에 그냥 사용하게 되면 `SyntaxError`가 발생됩니다.  
그래서 보통 Babel을 사용해서 JS 문법으로 변환을 한 후 사용하게 됩니다.  

> React에서 JSX 코드를 변환하기 위해 사용하는 Babel Plugin으로는 [babel-plugin-transform-react-jsx](https://babeljs.io/docs/en/babel-plugin-transform-react-jsx)이 있습니다.  

Babel은 JSX 코드를 `React.createElement()`로 변환하고 호출 하여 컴파일 합니다.  

```jsx
// Babel을 통해 JavaScript 문법으로 변환

ReactDOM.render(
  React.createElement(
    "div",
    { className: "App" },
    React.createElement(
      "header",
      { className: "App-header" },
      React.createElement(
        "h1",
        { className: "App-title" },
        "Welcome to React"
      )
    ),
    React.createElement(
      "p",
      { className: "App-intro" },
      "I Love React"
    )
  ),
  document.getElementById('root')
);
```

변환된 코드를 직접 보시고 싶으면 [BABEL 공식사이트의 'Try it out'](https://babeljs.io/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=EoUwhgxgLgIg8gWQHQCcQDsAmIUAoBQABIQDyYCWAboRADZgDODAcmALYgC8ARAIIAO_bgD4ixUgAtw2FDXpNWHHgP4BaKWBkix4yQEY5jFuy59BqqOSi0QIgOohaEAPYdCUZ4VCQoJAPQSeqK6_hoyweIk_IYKJsrm5OhQKM4iAJKEADLOlCBe4ND-_BH-FJTCADRimM4QAK4cSUgA5iBQAKI2jVAAQgCeaZi4AOQpzlDDAJT4kwDcQA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=false&targets=&version=6.26.0&envVersion=) 메뉴에서 확인이 가능합니다.  

## JSX는 객체를 나타냅니다

```jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```jsx
const element = React.createElement(
  'h1',
  { className: 'greeting' },
  'Hello, world!'
);
```

위 첫번째 코드는 두번째 형태로 변환이 되고

```jsx
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

리액트 내부적으로는 위와 같은 형태로 객체를 만들어서 관리하게 됩니다.  

이러한 객체를 **`React elements`** 라고 합니다. React는 이러한 객체를 읽고 사용하여 DOM을 구성하고 최신 상태로 유지합니다.  

다음 섹션에서 React 요소를 DOM에 렌더링하는 방법을 살펴 보겠습니다.  

[Rendering Elements >>](../rendering-elements/README.md)
