# Console

### Console.assert(value[,…message])

- Value 가 true 인지 false인지 확인

### Console.clear()

- Stdout은 TTY다. OS마다 다른데 리눅스에서는 그냥 clear랑 똑같이 동작한다.

### console.count([label])

- label이 몇번 console.count했는가

### console.countReset([label])

- label의 count reset

### console.debug(data[…args])

- console.log()의 alias

### console.dir(obj[, options])

- util.inspect()를 obj를 하고 출력한다.
- options
  - showHidden : default는 false, true 면 hidden propery를 보여준다. (Non-enumerable, symbol)
  - depth : default 는 2, util.inspect를 몇번 돌릴것인가
  - Color : default 는 false, true면 예쁘게 나온다.

### console.dirxml

- console.log랑 똑같음

### console.group

- 들여쓰기

### console.groupEnd

- 들여쓰기 한칸 뒤로

### console.table

- table화 할 수 있는 데이터를 테이블로 보여준다

### console.time

- timer 시작

### console.timeEnd

- Timer 끝내고 경과시간 출력

### console.timeLog

- console.time() 하고 난뒤 시간 출력, timer는 계속 동작.

