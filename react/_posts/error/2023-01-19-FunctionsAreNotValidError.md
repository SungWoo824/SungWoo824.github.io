---
layout: post  
title:  Functions are not valid as a React child. 에러
description:  
date: 2023-01-19 9:30 PM  
categories: error
---

### Functions are not valid as a React child.

![2023_01_19_1](/assets/img/post/2023-01-19-1.png)
위의 에러가 console 에 찍히고 있었습니다.

기존에 진행했던 프로젝트를 수정진행하며 react-router-dom 버전을 5 -> 6 으로 변경하며, 
발생된 오류중 하나.

### 발생코드

```js
function App() {
    return (
        <HashRouter>
            <Routes>
                <Route exact path="/" element={Home}/>
                <Route exact path="/signin" element={SignIn}/>
                <Route exact path="/signup" element={SignUp}/>
            </Routes>
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
                <Route exact path="/" element={Home()}/>
                <Route exact path="/signin" element={SignIn()}/>
                <Route exact path="/signup" element={SignUp()}/>
            </Routes>
        </HashRouter>
    );
}
```

Route 의 element 에 함수를 실행시키면 해결됩니다.
