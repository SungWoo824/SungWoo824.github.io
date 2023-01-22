---
layout: post  
title:  A <Route> is only ever to be used as the child of <Routes> element.
description:  
date: 2023-01-22 16:30 PM  
categories: error
---

### A \<Route\> is only ever to be used as the child of \<Routes\> element. 에러

![2023_01_19_1](/assets/img/post/2023-01-22-1.png)

해당 오류 또한 react-router-dom 버전을 5 -> 6 으로 변경하며, 
발생된 오류중 하나.

### 발생코드

```js
function App() {
    return (
        <HashRouter>
            <Route exact path="/" element={Home}/>
            <Route exact path="/signin" element={SignIn()}/>
            <Route exact path="/signup" element={SignUp()}/>
        </HashRouter>
    );
}
```

### 해결방법

```js
function App() {
    return (
        <HashRouter>
            <Routes>
                <Route exact path="/" element={Home}/>
                <Route exact path="/signin" element={SignIn()}/>
                <Route exact path="/signup" element={SignUp()}/>
            </Routes>
        </HashRouter>
    );
}
```
`<Route>` 사용시 겉에 `<Routes>`로 감싸줘야만 합니다.
