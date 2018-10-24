# React Fiber

**React Fiber**는 React reconciler(조정자)의 재 구현입니다.  
React팀은 이 아키텍처를 구현하기 위해 2년 넘도록 연구를 진행해서 완성시켰습니다.  
React Fiber의 목표는 애니메이션, 레이아웃 및 제스처와 같은 영역에 대한 적합성을 높이는 것입니다.  
주요 기능은 **incremental rendering**(렌더링 작업을 청크로 분할하여 여러 프레임에 분산시키는 기능)입니다.  

React팀에서는 이 개념을 **Time Slicing**이라고 이름을 정했습니다.  

## reconciler를 왜 재 작성했는가?

아마도 주된 이유 중 하나는 우리가 브라우저에서 예전부터 겪어왔던 문제 때문입니다.  

**메인 스레드는 UI 스레드와 동일합니다.**  

페이지 렌더링, 사용자 액션에 대한 응답, JS 계산, 네트워크 활동 관리 및 DOM 조작은 모두 브라우저 메인 스레드에서 처리합니다.  
이러한 것들 중 일부는 워커를 사용하여 다른 스레드에서 처리할 수 있지만 **DOM은 메인 스레드에서만 변경할 수 있습니다**.  

React에서 state가 변경되거나 props가 업데이트되면 `render()`함수는 React Elements의 새 트리를 만들고 다음 React가 조정 알고리즘을 실행하여 UI를 효율적으로 업데이트해서 새 트리와 일치시키는 방법을 찾습니다.  

`React Stack` reconciler는 항상 단일 패스에서 컴포넌트 트리를 동기화하여 처리합니다.  
이렇게 하면 재귀 프로세스가 완료될 때까지 메인 스레드가 다른 긴급한 작업을 해행하지 못합니다.  
사용자가 텍스트를 입력하는 동안 실시간으로 계속 계산이 실행된다면 앱이 응답하지 않아 프레임 속도가 고르지 않고 입력이 느려질 수 있습니다.  

> 새로운 Fiber reconciler의 주요 목표는 중단 가능한 작업을 여러 유형의 업데이트에 우선순위를 할당해서 메인 스레드가 diff 알고리즘을 일시 중지하고 다른 더 긴급한 작업을 수행할 수 있도록 하고 나중에 중단한 곳에서 다시 계속 진행할 수 있도록 중단 가능한 작업을 청크로 나누는 것입니다. - [Giamir Blog - What is React Fiber](https://giamir.com/what-is-react-fiber)  

`Fiber`는 다음과 같은 기능을 사용할 수 있습니다.  

- **아직 화면에 표시되지 않은 로직이 있으면 나중에 처리되도록 지연시킬 수 있습니다.**  
  => 데이터가 프레임 속도보다 빠르게 처리되면 통합 및 배치 업데이트를 수행하는 데 있어서 유리합니다. 
- **작업의 우선순위를 제어할 수 있습니다.**  
  => 프레임이 끊기지 않도록 덜 중요한 작업들(예: 네트워크에서 방금 로드한 새로운 콘텐츠를 렌더링)보다  
  사용자의 상호작용(예: 버튼 클릭으로 인한 애니메이션)에서 오는 작업의 우선순위를 더 높게 해서 먼저 처리하게 할 수 있습니다.  

## React Fiber의 알고리즘 단계

1. **render/reconciliation**
2. **commit**

첫번째로는 Fiber가 진행중인 작업 트리를 만들고 UI를 업데이트 하기 위해 필요한 변경 사항 목록을 파악하는 렌더/조정 단계가 있습니다.  
이 단계에서는 실제적인 변화를 일으키지는 않습니다.  

이 첫번째 단계에서는 각각의 컴포넌트들에게 변화가 있는지 없는지 검사하면서 현재 작업에 할당된 시간이 얼마나 남았는지, 그리고 다른 우선순위가 높은 작업이 없는지 지속적으로 검사합니다.  
만약 할당된 시간을 모두 소진하거나 다른 우선순위가 높은 작업이 들어왔을 때에는 현재 작업 상태를 저장하고 다른 작업에 프로세스를 양보합니다.  

그런 다음 두번째로 커밋 단계가 있으며 이 단계에서 모든 변경 사항들을 실제 DOM에 적용합니다.  
이 커밋 단계에서는 Fiber가 중간에 개입에서 중단하지 않으며 끝까지 진행됩니다. 

Fiber도 lifecycle hooks를 호출하고 커밋 단계의 일부 오류를 처리합니다.  

## 예제로 달라진 리액트 아키텍쳐의 퍼포먼스를 알아봅시다  

React Core팀의 기술 리더인 **Sebastian Markbåge**가 `React Fiber`의 이점을 잘 이해하기 위해 유용한 프랙탈 예제를 만들었습니다.  

![ReactFiberExample](./resource/react_fiber_example.png)  

[Fiber Example 동영상](https://twitter.com/twitter/statuses/846456239693344769)  

> Triangles Demo의 소스 코드는 [Official Github React Repository](https://github.com/facebook/react/blob/master/fixtures/fiber-triangle/index.html)에서 확인할 수 있습니다.  

### Time Slicing 전용 전
![타임슬라이스 미적용](./resource/triangles-demo-without-time-slicing.png)  

### Time Slicing 적용 후
![타임슬라이스 적용](./resource/triangles-demo-with-time-slicing.png)  

[참고]  
- [What is React Fiber ?](https://giamir.com/what-is-react-fiber)
- [남다른 개선방법을 다시 보여준 페이스북의 React Fiber](https://medium.com/@codesquad_yoda/%EB%82%A8%EB%8B%A4%EB%A5%B8-%EA%B0%9C%EC%84%A0%EB%B0%A9%EB%B2%95%EC%9D%84-%EB%8B%A4%EC%8B%9C-%EB%B3%B4%EC%97%AC%EC%A4%80-%ED%8E%98%EC%9D%B4%EC%8A%A4%EB%B6%81%EC%9D%98-react-fiber-80b7ca5bd9bb)
- [React Fiber](https://tech.wanted.co.kr/frontend/2018/01/07/react-fiber.html)
- [setState 메서드 파라미터로 객체 대신 함수 사용하기](https://www.vobour.com/-setstate-%EB%A9%94%EC%8F%98%EB%93%9C-%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%EB%A1%9C-%EA%B0%9D%EC%B2%B4-%EB%8C%80%EC%8B%A0-%ED%95%A8%EC%88%98-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-using)
