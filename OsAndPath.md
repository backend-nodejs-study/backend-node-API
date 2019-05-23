# nodejs 내장 모듈(core Modules)

파일기반 모듈 사용하는것 처럼 require 함수를 사용한다.

차이점은 파일의 상대 경로 대신 require 함수에 모듈 이름을 넣어준다.

```javascript
var path = require ('path')
```

## Path module

string 으로 이루어진 경로를 읽고 판단하여 각 메소드별 반환값 반환(string,객체)

### windows vs posix(portable operating system interface(unix 인터페이스 규격))

windows 와 posix에서의 반환값이 다르다. win32 or posix 를 이용해서 각각 환경에 맞게 값을 반환이 가능하다.

##### posix

전체 경로 

```javascript
path.basename('C:\\temp\\myfile.html');
// Returns: 'C:\\temp\\myfile.html'

path.win32.basename('C:\\temp\\myfile.html');
// Returns: 'myfile.html'
```

##### windows

파일 이름

```javascript
path.basename('C:\\temp\\myfile.html');
// Returns: 'myfile.html'

path.posix.basename('/tmp/myfile.html');
// Returns: 'myfile.html'
```



### path.normalize()

- 경로 분리문자가 중복된경우 수정 (//// => /)

  ```javascript
  path.normalize('C:\\temp\\\\foo\\bar\\..\\');
  // Returns: 'C:\\temp\\foo\\'
  ```

### path.join()

- 구분된 경로를 조합해서 하나의 경로로 만들어줌

  ```javascript
  path.join('/foo', 'bar', 'baz/asdf', 'quux', '..');
  // Returns: '/foo/bar/baz/asdf'
  ```

### path.dirname()

- 경로 반환

  ```javascript
  var completePath = '/foo/bar/bas.html';
  console.log(path.dirname(completePath)); 
  // Logs : /foo/bar
  ```

### path.basename()

- 파일명 반환

  ```javascript
  var completePath = '/foo/bar/bas.html';
  console.log(path.basename(completePath));
  // Logs : bas.html
  ```

### path.extname()

- 확장자 반환

  ```javascript
  var completePath = '/foo/bar/bas.html';
  console.log(path.extname(completePath));
  // Logs : .html
  ```

### path.parse()

- 경로,파일명,확장자 객체  반환

  ```javascript
  path.parse('/home/user/dir/file.txt');
  // Returns:
  // { root: '/',
  //   dir: '/home/user/dir',
  //   base: 'file.txt',
  //   ext: '.txt',
  //   name: 'file' }
  ```

  

## OS module

운영체제 관련 유틸리티 기능 및 속성

### os.type()

- 운영체제 type 반환 string

  ```javascript
  console.log(os.type ());
  //Linux
  ```

### os.totalmem()

- total memory  반환 int

  ```javascript
  var gigaByte = 1 / (Math.pow(1024, 3));
  console.log('Total Memory', os.totalmem() * gigaByte, 'GBs');
  //Total Memory 15.373516082763672 GBs
  ```

### os.freemem()

- 사용 가능한 시스템 메모리 반환 int

  ```javascript
  console.log('Available Memory', os.freemem() * gigaByte, 'GBs');
  //Available Memory 9.391571044921875 GBs
  ```

### os.cpus()

- 각 cpu 코어에 대한 정보가 들어있는 객체의 배열 반환

  ```javascript
  console.log(os.cpus());
  console.log('This machine has', os.cpus().length, 'CPUs');
  
  // [ { model: 'Intel(R) Core(TM) i7-8565U CPU @ 1.80GHz',
  //    speed: 2987,
  //    times: 
  //     { user: 10025000,
  //       nice: 43600,
  //       sys: 2455800,
  //       idle: 191018600,
  //       irq: 0 } },
         
         ...
         
  //]
  
  //This machine has 8 CPUs
  ```

### os.homedir()

- home 디렉토리 문자열 반환

### os.tmpdir()

- 임시파일 디렉토리 문자열 반환

### os.EOL

- 운영체제 별 개행문자 문자열

### os.networkInterfaces ()

- 네트워크 인터페이스를 포함하는 객체를 반환한다.

  ```javascript
  console.log(os.networkInterfaces());
  //{ lo: 
  //   [ { address: '127.0.0.1',
  //       netmask: '255.0.0.0',
  //       family: 'IPv4',
  //       mac: '00:00:00:00:00:00',
  //       internal: true,
  //       cidr: '127.0.0.1/8' },
  //     { address: '::1',
  //       netmask: 'ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff',
  //       family: 'IPv6',
  //       mac: '00:00:00:00:00:00',
  //       scopeid: 0,
  //       internal: true,
  //       cidr: '::1/128' } ],
  //  wlp0s20f3: 
  //   [ { address: '192.168.1.79',
  //       netmask: '255.255.255.0',
  //       family: 'IPv4',
  //       mac: '98:2c:bc:50:a9:9c',
  //       internal: false,
  //       cidr: '192.168.1.79/24' },
  //     { address: 'fe80::9ebb:759c:b4bc:56bc',
  //       netmask: 'ffff:ffff:ffff:ffff::',
  //       family: 'IPv6',
  //       mac: '98:2c:bc:50:a9:9c',
  //       scopeid: 2,
  //       internal: false,
  //       cidr: 'fe80::9ebb:759c:b4bc:56bc/64' } ] }
  ```

  