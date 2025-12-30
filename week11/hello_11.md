
    ### **1. Context**
    
    - 리액트 컴포넌트 트리 전체를 대상으로 데이터 공급하는 기능입니다.
    - 'Props Drilliing' 문제 해결하기 위해서 사용합니다.
        - 'Props Drilliing: 컴포넌트 계층 구조에서 컴포넌트 간에 값으 전달할 때 발생합니다.
        - 목적지까지 데이터를 전달하기 위해서 컴포넌트에 일일이 Props 전달합니다.
        - 데이터 교환 구조 파악하기 어렵다는 한계가 있습니다.
    - "컴포넌트 A 와 컴포넌트 B가 동일한 문맥 아래에 있다" -> 컴포넌트 A,B가 동일한 목적(기능)을 가지고 있다라는 뜻으로 이해할 수 있습니다. (TodoEditer,TodoList,TodoItem-모두 할일 관리하기 문맥 컴포넌트)
    - 일일이 Props를 전달하지 않아도 Contetext를 사용하면 컴포넌트 트리 전역에 데이터 공급 가능합니다,
    - Body.Sidebar,Main을 하나의 Context로 묶고, 공유 가능합니다.
    
    ### **1.1 ContextAPI**
    
    - Context를 만들고 다루는 리액트 기능입니다.
    - React.createContext: 새로운 Context를 만들 수 있습니다.
    
    ```
    import React from 'react';
    const MyContext = React.createContext(defaultValue);
    ```
    
    - **Conrtext에 데이터 공급하려면 Context.Provider기능 사용해야 합니다.(Context객체에 기본으로 포함된 기능)**
    
    ```
    import React from 'react';
    const MyContext = React.createContext(defaultValue);
    
    function App(){
    	const data='data';
        return(
        	<div>
        		<header/>
            	<MyContext.Provider vlaue=(data)>
            	<Body/>
            	</MyContext.Provider>
        	</div>
    	);
    }
    export default App;
    ```
    
    - **Context가 공급하는 데이터 사용하기-useContext사용하여 특정 Context가 공급하는 훅**
    - useContext를 호출한 컴포넌트가 인수로 전달한 Context그룹에 속해있지 않으면 오류가 발생합니다.
    - Main 컴포넌트는 MyContext 그룹에 속하기 때문에 문제가 발생하지 않습니다.
    
    ```
    import React, {useContent} from 'react'
    const MyContent = React.createContext(defaultValue);
    function App(){
    	...
    }
    function Main(){
    	const data = useContext(MyContext);
        ..}
    ```
    
    ### **1.2 Context로 [할 일 관리] 앱 리팩토링하기**
    
    - 리팩토링: 사용자에게 제공하는 기능은 변경하지 않고, 내부 구조를 개선하는 작업입니다.
    
    (Props Drilling-제거, 할 일 아이템-추가,수정,삭제,검색 기능은 변합없이 동작하도록 만들어야함)
    
    [할 일 관리] 컴포넌트 트리와 데이터 공급 구조/ Context를 이용해 [할 일 관리]앱의 Props Drilling문제 해결
    
    - Context 방식은 Context API를 사용하여 중간단계의 Body를 일일이 거치지 않고, 필요한 하위 컴포넌트들이 데이터들을 직접 공유받는 형식입니다.
    - Provider 에 값을 전달하면 TodoContext아래에 있는 모든 컴포넌트는 필요한 데이터를 공급받아 사용 가능합니다.
    
    ### **1.3 TodoContext를 만들어 데이터 공급하기**
    
    - Todocontext 만들기
    - Context는 반드시 컴포넌트 밖에서 생성해야합니다.(안에서 생성-의도대로 동작 불가능/ 별도의 파일로 분리)
    - TodoContext하위에 배치한 컴포넌트는 다른경로로 props를 받을 필요가 없기 때문에 기존의 props를 모두 제거합니다.
    
    ```
    <!-- import React, { useCallback,useReducer,useRef} from"react";
    (...)
    const TodoConte= React.createContent();
    function App(){
    	(...)
        return(
         <div className="App">
           <Header/>
           <TodoContext.Provider>
             <TodoEditer onCreate={onCreate}/>
             <TodoList todo={todo} onUpdate={onUpdate} onDelete={onDelete}/>
           </TodoContext.Provider>
         </div>
        };
    }
    export default App;
    ``` -->
    
    ### **1.4 오류 해결하기**
    
    - props를 제거하면 오류가 발생합니다.(오류 발생 메세지-"Cannot read properties of undefined(reading 'length')")
    - todo의 기본값을 빈 배열로 하는 defaultProps를 설정하면 오류는 발생하지 않습니다.
    
    ```
    (...)
        const analyzeTodo = useMemo(()=>{
            const totalCount= todo.length;
            const doneCount = todo.filter((it)=>it.isDone).length;
            const notDoneCount = totalCount- doneCount;
            return{
                totalCount,
                doneCoubt,
                notDoneCount,
            };
        },[todo]};
    (...)
    ```
    
    ```
    const TodoList=({todo,onUpdate,onDelete})=>{
    (...)
    }
    TodoList.defaultProps={
    	todo;[],
    };
    export default TodoList;
    ```
    
    ### **1.5 TodoList 컴포넌트에서 Context 데이터 사용하기**
    
    - useContext를 사용하여 데이터를 불러오기 전에 Context를 불러 올수 없기 때문에 export로 내보내야 합니다.
    - export const TodoList = React. createContext();
    - 일일이 Props를 꺼내오지 않고, useContext를 사용해서 한번에 데이터를 꺼내올 수 있습니다.
    
    ```
    import { useContext. useMenu, useState } from "react";
    import {  TodoContext} from ".../App";
    (...)
    const TodoList=({ todo, onUpdate, onDelete})=>{
    	const storeData= useContext(TodoContext);
        console.log(storeDate);
        (...)
    };
    ```
    
    ### **1.6 TodoItem 컴포넌트 에서 Context 데이터 사용하기**
    
    - onUpdate,onDelete를 TodoList에서 받았고, TodoItem도 함수를 Context에서 직접 불러와서 사용합니다.
    - TodoList에[서도 이 함수들을 전달할 필요성이 없어졌기 때문에 Context에서 Props로 받을 필요가 없습니다.
    - TodoItem 컴포넌트에서 TodoContext가 공급하는 데이터를 사용하도록 히팩토링하고, 페이지에서 할 일 아이템을 수정,삭제하며 정상적으로 동작하는지 확인해야 합니다.
    
    ### **1.7 TodoEditer 컴포넌트에 데이터 공급하기**
    
    - TodoEditer 컴포넌트에서 할 일 아이템을 생성하는 함수 onCreate를 TodoContext에서 받도록 수정합니다.
    - useContext를 사용하여 수정합니다.
    
    ### **1.8 체크포인트**
    
    (1) 리팩토링이 잘 되었는지 확인하기1
    
    - 리팩토링이란 전달하고자 하는 기능은 그대로 유지하되 내부 구조만 변경해 개선하는 일입니다.
    - 원래 정상적인 기능들이 잘 작동하지 않는다면 리팩토링이 잘 돼지 않은 것입니다.
    - 최적화 하는데 문제가 없는지 확인해야 합니다.
    - TodoItem 컴포넌트는 할 일 아이템의 개수만큼 반복하여 렌더링하기 때문에 최적화 문제가 생기면 서비스에 문제가 발생합니다.
    - 할 일 아이템을 생성하고, 모든 컴포넌트가 리렌더링 됩니다. 그 후에 React.memo가 정상적으로 동작하지 않고, 리팩토링이 정상적으로 작동하지 않은것을 볼 수 있습니다.
    
    (2) 문제의 원인 파악하기
    
    - Context 리팩토링 이후 불필요한 상황에서도 리팩토링 되고 있습니다, value값이 바뀌면 리렌더 되고,TodoContext.Provider 컴포넌트들도 함께 리렌더되는 상황이 발생합니다.
    - todo가 변하면, Todocontext.Provider에서 전달하는 Props 모두 변환합니다. onCreate,onUpdate.onDelete만 받는 컴포넌트도 불필요한 리렌더됩니다.
    
    (3) 구조 재설계하기
    
    - 문제의 원인은 todo,onCreate,onUpdate,onDelete와 같은 dispatch 관련 함수들이 하나의 객체로 묶여 동일한 Props로 Context에 전달되기 때문입니다.
    - 불필요한 리렌더가 발생하기 때문에 Context를 두가지로 나눕니다.
        - TodoStateContext ; todo가 업데이트되면 영향받는 컴포넌트를 위한 Context
        - > todo 데이터를 받는 컴포넌트만 리렌더됩니다.
        - TOdoDispatchContent: dispatch함수 onCreate, onUpdate, onDelete가 변경되면 영향을 받는 컴포넌트를 위한 Context
        - -> dispatch 함수를 사용한느 컴포넌트는 더 이상 리렌더되지 않습니다.
    
    (4) 재설계된 구조로 변경하기
    
    - 역할별로 분리하여 최적화 문제가 생기지 않도록 리팩토링합니다.
    - Context 분리하기: TodoContext를 삭제하고, TodoDispatchContext를 각각 만들어 배치합니다.
    - 밑의 코드를 저장하면 오류가 발생하고,하나씩 해걀합니다.
    
    ```
    export const TodoStateContext = React.createContent();
    export const TodoDispatchContext = React.createContent();
    
    function App(){
    (...)
      return(
      	<div clasName="App">
        	<Header/>
            <TodoStateContext.Provider value={todo}>
            	<TodoDispatchContext.Provider value={{ onCreate,onUpdate,onDelete}}>
                	<TodoEditer/>
                    <TodoList/>
                 </TodoDispatchContext.Provider>
            </TodoStateContext.Provider>
        </div>
      };
    }
    export default App;
    ```
    
    - useCallbaxk을 적용한 함수는 다시 생성되지 안혹, Props로 전달하기 위해 묶은 3개의 함수는 다시 생성됩니다. 다시 생성되지 않도록 usMemo를 사용합니다.
    - useMemo를 사용해 dispatch 함수를 다시 생성하지 않도록 만드는 것 입니다.
    
    ```
    import React, { useMemo, useReducer, useCallback, useRef{} from "react";
    (...)
    function App(){
     (...)
      const memoizedDispatches = useMemo(() => {
        return { onCreate, onUpdate, onDelete };
      }, []);
    
      return (
        <div className="App">
          <Header />
          <TodoStateContext.Provider value={{todo}}>
            <TodoDispatchContext.Provider value={memoizedDispatches}>
              <TodoEditor />
              <TodoList />
            </TodoDispatchContext.Provider>
          </TodoStateContext.Provider>
        </div>
      );
    }
    
    export default App;
    ```
    
    (5) TodoEditer 수정하기
    
    - App에서 할 일 아이템을 생성하는 함수인 oncreate함수가 필요하고, TodoStateContext에서 데이터를 받을 필요 없고, TodoDispatchContext에서 함수 onCreate만 받으면 됩니다.
    
    ```
    import { TodoDispatchContext } from "../App";
    
    const TodoEditor = () => {
      const { onCreate } = useContext(TodoDispatchContext);
    
      return (
        // ...
      );
    };
    
    export default TodoEditor;
    ```
    
    (6) TodoList 수정하기
    
    - 할 일 데이터인 todo를 사용하기 때문에 TodoStateContext에서 받도록 수정합니다.
    
    ```
    import { TodoStateContext } from "../App";
    
    const TodoList = () => {
      const todo = useContext(TodoStateContext);
    
      return (
      );
    };
    
    export default TodoList;
    ```
    
    (7) TodoItem 수정하기
    
    - 할 일을 수정하고 삭제하는 함수 onUpdate,onDelete를 사용하기 때문에 TodoDispatchContext에서 함수를 받도록 수정합니다.
    - 주의: 리팩토링은 기본 코드를 수정하는 일이 많기 때문에 오류가 많이 발생합니다. 오류를 줄이기 위해 처음에 작성한 코드를 잘 확인하고, 리액트 훅을 잘 불러오는 지 확인해야 합니다. 경고 메세지가 뜰 수 있으나 프로그램에 지장이 없기 때문에 무시해도 되는 부분입니다.
    
    ```
    import { TodoDispatchContext } from "../App";
    
    const TodoItem = ({ id, content, isDone, createdDate }) => {
      console.log(`${id} TodoItem 업데이트`);
      const { onUpdate, onDelete } = useContext(TodoDispatchContext);
    
      return (
        // ...
      );
    };
    
    export default React.memo(TodoItem);
    ```
    
    (8) 리팩토링 잘 되었는지 확인하기2
    
    - 최적화를 위해 Todocontext를 두가지로 분리했습니다. 새로운 할 일 아이템을 생성하면 TodoItem만 렌더링하고 나머지 컴포넌트는 더 이상 리렌더 하지 않습니다.