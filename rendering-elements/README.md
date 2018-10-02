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

  return ReactElement(
    type,
    key,
    ref,
    self,
    source,
    ReactCurrentOwner.current, // 자기 자신을 감싸고있는 부모 컴포넌트
    props
  );
}
```

`ReactCurrentOwner.current`는 **FiberNode** 객체로 되어있는데 가르키는 위치는 아래와 같습니다.  

**Fiber**는 React 16에서 새로나온 핵심 아키텍처입니다.  

> **Fiber**는 오류 경계 및 파편과 같은 React 16의 대부분의 새로운 기능을 담당합니다.
> 다음 몇 가지 릴리스에서 React의 잠재력을 최대한 발휘하기 시작하면 더 많은 새로운 기능을 기대할 수 있습니다.
> 우리가 작업하는 가장 흥미로운 영역은 비동기 렌더링이다.
> 이는 주기적으로 브라우저에 실행을 함으로써 렌더링 작업을 협업적으로 계획하는 전략이다.
> 결론은 비동기 렌더링을 사용하면 React가 주 스레드를 차단하지 않기 때문에 앱이 더 반응성이 있다는 것입니다.
> [새로운 핵심 아키텍처](https://reactjs.org/blog/2017/09/26/react-v16.0.html#new-core-architecture)


>Fiber는 오류 경계와 단편과 같은 React 16의 새로운 기능 대부분을 담당합니다. 다음 몇 가지 릴리스에서 우리는 React의 잠재력을 최대한 활용하기 위해 더 많은 새로운 기능을 기대할 수 있습니다.
>아마도 우리가 작업하고있는 가장 흥미로운 부분은 비동기 렌더링, 즉 브라우저에 대한 실행을 주기적으로 수행하여 렌더링 작업을 협업 적으로 예약하는 전략입니다. 결과적으로 비동기 렌더링에서는 React가 메인 스레드를 차단하지 않기 때문에 애플리케이션의 응답 성이 향상되었습니다.

```js
<Wrapper> // <- null
  <h1>Hello World</h1> // <- Wrapper
  <p>I Love React</p> // <- Wrapper
  <List/> // <- List ( 하위 자식들도 List )
<Wrapper>
```

글 쓰는중... ReactElement 이이서 써야됨
