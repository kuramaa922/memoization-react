# memoization-react

## HOC memo

В общем случае мемоизация помогает запомнить результат выполнения функции при повторяющихся входных параметрах, помогая избежать лишних вызовов функции и вычислений внутри нее, сразу отдавая нужный результат.

Если компонент обернут в HOC memo и его пропсы не изменились, а, например, родитель перерендерился, то будет возвращен предыдущий мемоизированный компонент.

```javascript
import { memo } from 'react'

const SomeComponent = memo(function SomeComponent(props) {

    // ...

});
```

Это может быть полезно, например, для компонентов с родителем, который часто перерендеривается. Если бы компонент не был мемоизирован, он бы перерендеривался вместе с ререндером родителя (как мы помним, при перерендере компонента все его потомки будут тоже перерендерены). Особенно это касается компонентов, которые называют Slow components. Внутри них есть какие-то тяжелые вычисления, например, анимация. 

Не стоит злоупотреблять HOC memo и оборачивать в него много компонентов. Оборачивать нужно точечно — там, где в этом есть необходимость. Также нужно помнить, что хоть мемоизация и избавляет от лишних ререндеров, но она потребляет ресурсы памяти, так как мемоизированные значения нужно где-то хранить.

## useMemo

В React можно мемоизировать Также значения или результаты вычислений внутри компонентов. Для этого есть хук useMemo

```javascript
import { useState, useMemo } from React;



const MyComponent = () => {

  const [number, setNumber] = useState(0);

  

  const getFactorial = value => {

    if (value === 0) return 1;

    return value * getFactorial(value - 1);

  };

  

  const calculatedFactorial = useMemo(() => {

    return number ? getFactorial(number) : 0;

  }, [number]);

  

  return (

    <>

        <div>Enter a number:</div>

        <input value={number} onChange={(e) =>

          setNumber(e.target.value)}

        />

      <div className="box">Factorial: {calculatedFactorial}</div>

    </>

  );

}
```

https://www.youtube.com/watch?v=U_m0QAvcG2s

## useCallback

Еще один способ мемоизации в React – это хук useCallback. Он позволяет мемоизировать саму функцию между рендерами, а не результат ее вызова, как useMemo.
- https://www.youtube.com/watch?v=AqCkWH4ws1Y

Как правильно использовать хук useCallback:
- https://www.youtube.com/watch?v=yEPukn2rS28&t=143s

## Мемоизация контекста
С использованием контекста есть проблема – изменение значений контекста влечет за собой перерендер всех компонентов внутри провайдера. HOC memo и хук useMemo как раз могут помочь оптимизировать рендеры. Для этого необходимо values, передаваемые в провайдер, обернуть в useMemo и обернуть в memo потомков провайдера:

```javascript
const UserContext = React.createContext();



function UserContextProvider({ children }) {



  const [authUser, setAuthUser] = React.useState(null);

  const value = React.useMemo(() => ({

    authUser, setAuthUser

  }), [authUser]);

  return (

    <UserContext.Provider value={value}>

      {children}

    <UserContext.Provider>

  );

}

const Pages = memo(() => {

  return <>

      <Login />

      <Home />

  </>

})

function App() {

  return (

    <UserContextProvider>

       <Pages />

    </UserContextProvider>

  );

}
```

Пример https://codesandbox.io/p/sandbox/context-with-and-without-usememo-with-memo-forked-zff6ln?file=/src/App.js

## Дополнительно

- https://www.youtube.com/watch?v=OM-UNgMvvDk
- https://habr.com/ru/companies/timeweb/articles/684718/
- https://github.com/facebook/react/issues/15156
- https://www.youtube.com/watch?v=DDN9himU5PE&t=1949s
- https://vkvideo.ru/video-227259800_456239242
