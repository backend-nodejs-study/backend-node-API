## 타이머
타이머 기능을 제공하는 함수로 노드에서 window대신 global객체 안에 들었다.  

```javascript
setTimeout(callback, 밀리초)
setInterval(callback, 밀리초) // 주어진 킬리초마다 콜백 함수를 반복 실행
setImmediate(callback) // 콜백 함수를 즉시 실행 
```

이 타이머 함수들은 모두 아이디를 반환한다. 아이디를 사용하여 타이머를 취소할 수 있다.  

```javascript
clearTimeouf(아이디) // setTimeout을 취소한다.
clearInterval(아이디) // setInterval을 취소한다.
clearImmediate(아이디) // setImmediate을 취소한다.
```

```javascript
- 아이디.unref() // 이 타이머가 이벤트루프에서 유일하게 남은 것이라면 콜백함수실행을 보류함.
- 아이디.ref(): //unref()에 의해 보류된 콜백함수를 제개한다.
```

setTimeout, setImmediate 둘다 이벤트 루프를 거친다. 특수한 경우에 setImmediate가 먼저 실행된다.  

## process
process객체는 현재 실행되고 있는 노드 프로세스에 대한 정보를 담고 있다.  
일반적으로 운영체제나 실행 환경별로 다른 동작을 하고 싶을 때 사용한다.  

- process.env
    사용자의 환경변수를 담고있는 객체이다.  
    한 애플리케이션 뿐만 아니라 전체 시스템에서 공통적으로 사용하는 값이 있다면 process.env를 사용하면 된다.(ex: DB주소, 포트번호,아이디,패스워드 등...)  

- process.nextTick(callback)
    이벤트 루프가 다른 콜백함수들 보다 nextTick의 콜백함수를 우선으로 처리하도록 만든다.(setImmediate, setTimeout, promise보다 먼저 실행)  

- process.exit(code)
    실행 중인 노드 프로세스를 종료한다.  v                                               v 
    독립적인 프로그램에서는 수동으로 노드를 멈추게하기위해 사용한다.  
