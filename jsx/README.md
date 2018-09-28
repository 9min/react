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

jsx문법에 사용된 엘리먼트 태그들은 `React.createElement()` 함수에 씌워져서 `ReactDOM.render()` 함수의 첫번째 element 인자로 들어가게됩니다.  

```js
ReactDOM.render(element, container[, callback]);
```

React 내부의 createElement() 함수는 다음과 같이 첫번째 인자로는 태그의 type, 두번째 인자는 props, 세번째 인자는 children을 받습니다.  

```js
React.createElement(
  type, // 'div' or 'span' ...
  [props], // React component, class, function, React fragment type
  [...children]
)
```

실제 createElement() 함수 내부는 어떻게 구성되어 있는지 한번 알아봅시다.

```js
function createElement(type, config, children) {
  var propName = void 0;

  // Reserved names are extracted
  var props = {};

  var key = null;
  var ref = null;
  var self = null;
  var source = null;

  if (config != null) {
    if (hasValidRef(config)) {
      ref = config.ref;
    }
    if (hasValidKey(config)) {
      key = '' + config.key;
    }

    self = config.__self === undefined ? null : config.__self;
    source = config.__source === undefined ? null : config.__source;
    // Remaining properties are added to a new props object
    for (propName in config) {
      if (hasOwnProperty$1.call(config, propName) && !RESERVED_PROPS.hasOwnProperty(propName)) {
        props[propName] = config[propName];
      }
    }
  }

  // Children can be more than one argument, and those are transferred onto
  // the newly allocated props object.
  var childrenLength = arguments.length - 2;
  if (childrenLength === 1) {
    props.children = children;
  } else if (childrenLength > 1) {
    var childArray = Array(childrenLength);
    for (var i = 0; i < childrenLength; i++) {
      childArray[i] = arguments[i + 2];
    }
    {
      if (Object.freeze) {
        Object.freeze(childArray);
      }
    }
    props.children = childArray;
  }

  // Resolve default props
  if (type && type.defaultProps) {
    var defaultProps = type.defaultProps;
    for (propName in defaultProps) {
      if (props[propName] === undefined) {
        props[propName] = defaultProps[propName];
      }
    }
  }
  {
    if (key || ref) {
      var displayName = typeof type === 'function' ? type.displayName || type.name || 'Unknown' : type;
      if (key) {
        defineKeyPropWarningGetter(props, displayName);
      }
      if (ref) {
        defineRefPropWarningGetter(props, displayName);
      }
    }
  }
  return ReactElement(type, key, ref, self, source, ReactCurrentOwner.current, props);
}
```

차근차근 하나씩 나눠서 살펴봅시다.  

1. **변수 선언**

```js
function createElement(type, config, children) {
  // props 객체에 담을 키 이름
  var propName = void 0;

  // ReactElement에 넘겨 줄 props 데이터들
  var props = {};

  // config 매개변수에 들어있는 고유 key 값
  var key = null;

  // config 매개변수에 들어있는 ref 값
  var ref = null;

  // config 매개변수에 들어있는 webpack으로 번들링할 때 생성된 this
  /*
    Object
    {
      __esModule: true
    }
  */
  var self = null;

  // cconfig 매개변수에 들어있는 webpack으로 번들링할 때 생성된 코드 위치
  /*
    Object
    {
      fileName: "/Users/kakao/Documents/react-project/src/index.js"
      lineNumber: 64
    }
  */
  var source = null;

  (...)

}
```

2. **config 유효성 체크**

```js
function createElement(type, config, children) {

  (...)

  // config 매개변수 값이 존재하면
  if (config != null) {

    // config 매개변수 프로퍼티에 ref가 존재하는지 체크
    if (hasValidRef(config)) {

      // 들어있으면 ref 변수에 저장
      ref = config.ref;
    }

    // config 매개변수 프로퍼티에 key가 존재하는지 체크
    if (hasValidKey(config)) {

      // 들어있으면 key 변수에 문자열로 저장
      key = '' + config.key;
    }

    // config 매개변수 프로퍼티에 __self 값이 존재하면 self 변수에 저장
    self = config.__self === undefined ? null : config.__self;

    // config 매개변수 프로퍼티에 __source 값이 존재하면 source 변수에 저장
    source = config.__source === undefined ? null : config.__source;

    // config 객체를 탐색하면서 RESERVED_PROPS 객체의 프로퍼티 이름과 다른게 있으면 별도로 props 객체에 저장
    /*
      RESERVED_PROPS
      {
        key: true
        ref: true
        __self: true
        __source: true
      }
    */
    for (propName in config) {
      if (hasOwnProperty$1.call(config, propName) && !RESERVED_PROPS.hasOwnProperty(propName)) {
        props[propName] = config[propName];
      }
    }
  }
}
```

