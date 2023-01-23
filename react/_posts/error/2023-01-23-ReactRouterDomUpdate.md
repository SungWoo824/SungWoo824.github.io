---
layout: post  
title:  React-Router-Dom v5 -> v6 Update 파생 에러.
description:  
date: 2023-01-23 12:30 PM  
categories: error
---

## React-Router-Dom v5 -> v6 Update 파생 에러

react-router-dom 버전을 5 -> 6 으로 변경하며,
발생된 오류들을 정리하였습니다.


### 1. Cannot read properties of undefined (reading 'match')
![2023_01_19_1](/assets/img/post/2023-01-23-1.png)

#### 발생코드

```js
function App() {
    return (
        <HashRouter>
            <Routes>
                <Route path="/team/:teamDomain?/:topicNo?" element={Topic()}/>
            </Routes>
        </HashRouter>
    );
}
function Topic({match}) {
    return (
        <div className="collapse-item">
            <ChatLeftSideComponent teamDomain={match.params.teamDomain}/>
            <div className="content-container">
                <ChatTopNavComponent teamDomain={match.params.teamDomain} topicNo={match.params.topicNo}/>
                <ChatAreaComponent/>
            </div>
        </div>
    );

}
```

### 해결방법

```js
function Topic() {
    const {teamDomain, topicNo} = useParams();
    return (
        <div className="collapse-item">
            <ChatLeftSideComponent teamDomain={teamDomain}/>
            <div className="content-container">
                <ChatTopNavComponent teamDomain={teamDomain} topicNo={topicNo}/>
                <ChatAreaComponent/>
            </div>
        </div>
    );

}
```
Path Variable 을 이용할시 useParams() Hook 을 이용하여 사용.
