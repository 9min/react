# JSX

리액트에서는 다음과 같이 JSX 문법을 사용하는데

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
그래서 보통 [babel-plugin-transform-react-jsx](https://babeljs.io/docs/en/babel-plugin-transform-react-jsx)을 사용해서 JS 문법으로 변환을 한 후 사용하게 됩니다.  

```jsx
// JavaScript 문법으로 변환
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

jsx문법에 사용된 엘리먼트 태그들은 React.createElement() 함수에 씌워져서 `ReactDOM.render()` 함수의 첫번째 element 인자로 들어가게됩니다.  

```js
ReactDOM.render(element, container[, callback]);
```

ReactDOM 내부의 createElement() 함수는 아래와같이 첫번째 인자로는 태그의 type, 두번째 인자는 props들, 세번째 인자는 callback을 받습니다.  

```js
React.createElement(
  type, // 'div' or 'span' ...
  [props], // React component, class, function, React fragment type
  [...children]
)
```

