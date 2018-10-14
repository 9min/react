# React와 ReactDOM

React와 ReactDOM이 두 개의 라이브러리로 분리 된 이유는 React Native가 등장했기 때문입니다.  
React는 웹 및 모바일 앱에서 활용되는 기능을 포함하고 ReactDOM 기능은 웹 앱에서만 사용됩니다.  

**v0.14 React Beta** 릴리즈 버전 패키지에 보면
[react-native](https://facebook.github.io/react-native), [react-art](https://github.com/reactjs/react-art), [react-canvas](https://github.com/Flipboard/react-canvas), 그리고 [react-three](https://github.com/Izzimach/react-three) 가 있는데 브라우저 DOM과는 아무 상관이 없습니다.  

이를 명확하게 하고 React가 렌더링 할 수 있는 더 많은 환경을 보다 쉽게 구축 할 수 있도록 하기위해 React와 ReactDOM으로 분리합니다.  

**근본적으로, React의 아름다움과 본질은 브라우저 또는 DOM과 아무 관련이 없습니다.**  

참고
- [react-v0.14-beta-1](https://reactjs.org/blog/2015/07/03/react-v0.14-beta-1.html)
- [react-v0.14](https://reactjs.org/blog/2015/10/07/react-v0.14.html)