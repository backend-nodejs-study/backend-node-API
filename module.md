# Modules

node 에서는 코드를 모듈로 만들 수 있다는 점에서 브라우저의 자바스크립트와 다르다. 모듈은 특정 기능이나 변수들의 집합으로 일반적으로 한 파일이 하나의 모듈이 된다. 아래 foo.js 라는 파일이 있다. require 함수를 통해 circle.js 모듈을 불러온다.

```javascript
const circle = require('./circle.js');
console.log(`The area of a circle of radius 4 is ${circle.area(4)}`);
```

circle.js 파일은 아래와 같다. exports 객체에 area와 circumference를 property로 추가한다. 이 모듈을 실행할때 node 는 function으로 한번 더 싸주기 때문에 이 모듈에서 사용된 PI와 같은 지역변수들은 Private이 된다.

```javascript
const { PI } = Math;

exports.area = (r) => PI * r ** 2;

exports.circumference = (r) => 2 * PI * r;
```

 Exports 객체의 property로 함수 혹은 변수들을 붙일 수 있지만 module.exports의 새로운 값으로 할당할 수도 있다. 아래는 square.js 모듈을 require하고 있다.

```javascript
const Square = require('./square.js');
const mySquare = new Square(2);
console.log(`The area of mySquare is ${mySquare.area()}`);
```

square.js 는 Square class를 export하고 있다.

```javascript
// assigning to exports will not modify module, must use module.exports
module.exports = class Square {
  constructor(width) {
    this.width = width;
  }

  area() {
    return this.width ** 2;
  }
};
```

### Accessing the main module

 file이 실행되면 require.main이 module로 설정된다. 이를 통해 main module 접근이 가능하다. 예를 들어 node foo.js 로 foo 모듈이 실행됐다면 require.main은 module이 되지만 require('./foo.js')로 실행된다면 require.main은 module이 아니다. require.main.filename은 main module의 파일이름을 가지고 있으므로 이를 통해 현재 실행중인 application의 이름을 확인할 수 있다. 

### caching

 require가 한번 일어나면 node 는 해당 모듈을 cache하고 다시 같은 file을 require하면 file을 resolve 하는 것이 아니라 cache에서 빼온다. 즉, 다시 require를 하면 정확히 동일한 모듈을 require한다는 뜻이다. cache한 모듈들은 require.cache로 확인할 수 있다.

### Core module

NodeJs는 내장 core module들을 lib 디렉토리에 가지고 있다. 이 core module 이름을 require하면 core module을 우선으로 가지고 온다. 예를 들어 require('http') 면 core module인 http를 우선으로 가지고 온다.

### Cycles

require가 순환하면서 호출하는 상황. 아래 예제를 보자.

`a.js`:

```js
console.log('a starting');
exports.done = false;
const b = require('./b.js');
console.log('in a, b.done = %j', b.done);
exports.done = true;
console.log('a done');
```

`b.js`:

```js
console.log('b starting');
exports.done = false;
const a = require('./a.js');
console.log('in b, a.done = %j', a.done);
exports.done = true;
console.log('b done');
```

`main.js`:

```js
console.log('main starting');
const a = require('./a.js');
const b = require('./b.js');
console.log('in main, a.done = %j, b.done = %j', a.done, b.done);
```

main이 a를 require하면 a는 b를 require하고 b는 다시 a를 require하는 무한루프에 걸려버린다. 이와 같은 상황에서 node는 아직 완료되지않은 a를 b로 리턴하고 b를 완료한 후에 이를 a.js로 리턴하고 a를 마무리 한다.

```txt
$ node main.js
main starting
a starting
b starting
in b, a.done = false
b done
in a, b.done = true
a done
in main, a.done = true, b.done = true
```

### File Modules

 만약 해당 이름을 가진 file이 없으면 node는 먼저 js 와 json을 확장자로 붙인 것을 찾고 없으면 node를 찾는다.

### Folders as Modules

Folder를 모듈로 받아올 수 있다. 첫번째는 package.json을 root directory에 두는 것이다. 형식은 아래와 같다.

```json
{ "name" : "some-library",
  "main" : "./lib/some-library.js" }
```

이 package.json이 ./some-library에 있다면 require('./some-library') 이렇게 하면 node는 ./some-library/lib/some-library.js를 가지고 오는 것을 시도한다.

 만약 package.json이 없으면 node는 index.js 나 index.json을 찾아서 load하려고 시도하고 이것도 없으면 Error가 발생한다.

### Loading from node_modules Folders

 require()에 파일 이름이 core module이 아니고 / 나 ./ ../ 등이 안붙었으면 node는 require한 모듈의 디렉토리에 node_modules를 붙이고 찾아본다. 예를 들어 만약  '/home/ry/projects/foo.js' 가 require('bar.js')를 하고 있다면  다음의 순서로 bar.js를 찾아본다.

- `/home/ry/projects/node_modules/bar.js`
- `/home/ry/node_modules/bar.js`
- `/home/node_modules/bar.js`
- `/node_modules/bar.js`

### Loading from the global folders

NODE_PATH라는 global variable을 설정하면 파일이 어디에도 없을떄 NODE_PATH를 찾는다.

### The module wrapper

module code가 실행되기 전에 node는 먼저 module code를 function으로 wrap한다.

```js
(function(exports, require, module, __filename, __dirname) {
// Module code actually lives in here
});
```

이렇게 함으로써 변수들의 스코프를 유지할 수 있다. 

### __dirname

- string
- 현재 모듈의 디렉토리를 출력, path.dirname(__filename)과 같다.

### __filename

- string
- 현재 모듈의 파일이름을 출력한다. 절대 경로로 출력한다.

### exports

- module.exports의 참조

### module

- 현재 모듈의 참조
- module.exports 를 통해 현재 모듈에서 exports할 것을 정의한다.

### require()

- module을 import할때 사용

### require.cache

- module들은 한번 Require되면 이 객체에 cache된다. 이 객체에서 원하는 모듈을 삭제하면 다시 require할떄 reload한다.

### require.main

- Accessing the main module 볼 것.

- Example : entry.js가 다음과 같다.

- ```javascript
  console.log(require.main);
  ```

  ```text
  node entry.js
  ```

  ```text
  Module {
    id: '.',
    exports: {},
    parent: null,
    filename: '/absolute/path/to/entry.js',
    loaded: false,
    children: [],
    paths:
     [ '/absolute/path/to/node_modules',
       '/absolute/path/node_modules',
       '/absolute/node_modules',
       '/node_modules' ] }
  ```

### require.resolve(request[,options])

- Request : resolve할 module path.
- module을 loading 하지는 않고 filename을 찾아보기만 한다.

### require.resolve.paths(request)

- request : module path
- request를 찾기위해 node가 찾아본 곳들을 배열로 리턴한다. core module은 null을 리턴한다.

### Module.chilidren

- require된 모듈들이 배열로 리턴됨

### module.filename

- Module의 file name

### module.id

- Module id. 일반적으로 filename

### module.loaded

- module이 로드 됐는가?

### module.parent

- 이 모듈을 call한 module
