# Rendering Elements

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

React 내부의 createElement() 함수의 매개변수는 첫번째로 태그의 type, 두번째로 props, 세번째로 children을 받습니다.  

```jsx
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
    for (propName in config) {
      if (hasOwnProperty$1.call(config, propName) && !RESERVED_PROPS.hasOwnProperty(propName)) {
        props[propName] = config[propName];
      }
    }
  }

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
  var self = null;

  // cconfig 매개변수에 들어있는 webpack으로 번들링할 때 생성된 코드 위치
  var source = null;

  (...)
}
```

**self** 변수 형태

```js
Object
{
  __esModule: true
}
```

**source** 변수 형태

```js
Object
{
  fileName: "/Users/kakao/Documents/react-project/src/index.js"
  lineNumber: 64
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
    for (propName in config) {
      if (hasOwnProperty$1.call(config, propName) && !RESERVED_PROPS.hasOwnProperty(propName)) {
        props[propName] = config[propName];
      }
    }
  }

  (...)
}
```

**RESERVED_PROPS** 상수 형태

```js
Object
{
  key: true
  ref: true
  __self: true
  __source: true
}
```

3. **children 체크**

```js
function createElement(type, config, children) {
  (...)

  // children 매개변수가 있는지 확인 ( 있으면 1 )
  var childrenLength = arguments.length - 2;

  // children 매개변수가 있으면
  if (childrenLength === 1) {

    // props 객체에 children 이름으로 children 저장
    props.children = children;

  // type, config, children 이외에 추가로 인자가 더 들어있으면
  } else if (childrenLength > 1) {

    // 추가된 인자 + 1만큼의 빈 배열을 생성하고
    var childArray = Array(childrenLength);

    // 그 개수보다 작을때까지 반복문을 돌면서 childArray 배열에 값을 넣습니다.
    // [children, 추가된 인자, ...]
    for (var i = 0; i < childrenLength; i++) {
      childArray[i] = arguments[i + 2];
    }

    // childArray 값을 수정 못하게 Object.freeze 메서드를 사용해서 불변 객체로 만들어 줍니다.
    {
      if (Object.freeze) {
        Object.freeze(childArray);
      }
    }

    // props 객체에 children 이름으로 childArray 배열 저장
    props.children = childArray;
  }

  (...)
}
```

4. **defaultProps 체크**

```js
function createElement(type, config, children) {
  (...)

  // type 매개변수가 존재하고 type에 defaultProps 값이 있으면
  if (type && type.defaultProps) {

    // defaultProps 변수에 type.defaultProps 값을 넣고
    var defaultProps = type.defaultProps;

    // defaultProps를 탐색하면서
    for (propName in defaultProps) {

      // props 객체에 같은 이름이 존재하지 않으면
      if (props[propName] === undefined) {

        // defaultProps로 설정한 값들을 props 객체에 저장합니다.
        props[propName] = defaultProps[propName];
      }
    }
  }

  (...)
}
```

5. **key, ref 체크**

```js
function createElement(type, config, children) {
  (...)

  {
    // key나 ref 값이 있으면
    if (key || ref) {

      // type이 함수면 displayName 변수에
      // type.displayName 값을 넣고 ( 태그 이름 )
      // type.displayName 값이 없으면 type.name 값을 넣고 ( 컴포넌트 이름 )
      // type.name 값도 없으면 'Unknown' 문자열을 넣습니다.
      // type이 함수가 아니면 displayName 변수에 type을 그대로 넣습니다
      var displayName = typeof type === 'function' ? type.displayName || type.name || 'Unknown' : type;

      // key 값이 있으면
      if (key) {

        // props 객체에 key를 getter로 바인딩
        defineKeyPropWarningGetter(props, displayName);
      }

      // ref 값이 있으면
      if (ref) {

        // props 객체에 ref를 getter로 바인딩
        defineRefPropWarningGetter(props, displayName);
      }
    }
  }

  (...)
}
```

6. **ReactElement 생성 후 리턴**

```js
function createElement(type, config, children) {
  (...)

  return ReactElement(type, key, ref, self, source, ReactCurrentOwner.current, props);
}
```

