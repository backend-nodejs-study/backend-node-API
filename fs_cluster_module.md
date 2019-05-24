# Node.js 공식문서 읽어보기

## File System

fs 모듈은 표준 POSIX 기능을 중심으로 밀접하게 모델링된 방식으로 파일 시스템과 상호작용하기 위한 API를 제공한다.

> POSIX(**p**ortable **o**perating **s**ystem **i**nterface)란 이식 가능한 운영 체제 인터페이스의 약자로, 서로 다른 UNIX OS 의 공통 API를 정리하며 이식성이 높은 유닉스 응용 프로그램을 개발하기 위한 목적으로 IEEE가 책정한 애플리케이션 인터페이스 규격이다.

**모든 파일 시스템 작업에는 동기식/비동기식 메소드가 있다.**

### 동기

```js
const fs = require('fs');

try {
	fs.unlinkSync('/tmp/hello');
	console.log('successfully deleted /tmp/hello');
} catch (err) {
	// handle the error
}
```

동기식 작동을 사용하여 발생하는 예외는 즉시 던져지며, try/catch를 사용하여 처리할 수 있다.

### 비동기

```js
const fs = require('fs');

fs.unlink('tmp/hello', error => {
	if (error) throw err;
	console.log('successfully deleted /tmp/hello');
});
```

비동기 형식은 항상 **완료 콜백을 마지막 인수로 사용합니다.** 완료 콜백의 인수는 메서드에 따라 다르지만 첫 번째 인수는 항상 예외를 위해 예약되어 있습니다. 만약 성공적으로 작업이 완료되면 error는 **null** 또는 **undefind** 가 됩니다.

비동기 메서드를 사용할 때는 **보장 된 순서가 없습니다.** 따라서 다음은 `fs.rename()` 이 조작되기 전에 `fs.stat()`조작이 완료 될 수 있기 때문에 오류가 발생하기 쉽습니다 .

```js
fs.rename('/tmp/hello', '/tmp/world', err => {
	if (err) throw err;
	console.log('renamed complete');
});
fs.stat('/tmp/world', (err, stats) => {
	if (err) throw err;
	console.log(`stats: ${JSON.stringify(stats)}`);
});
```

원하는 결과값을 얻으려면 아래와 같이 fs.stat() 호출을 fs.rename() 의 콜백으로 주어야 합니다.

```js
fs.rename('/tmp/hello', '/tmp/world', err => {
	if (err) throw err;
	fs.stat('/tmp/world', (err, stats) => {
		if (err) throw err;
		console.log(`stats: ${JSON.stringify(stats)}`);
	});
});
```

nodejs 에서는 많은 프로세스가 실행되어야 할 때, 이러한 비동기 버전을 사용하도록 강하게 권장한다. 동기식 버전은 한 개의 작업이 완료될 때까지 프로세스가 중단되기 때문에 비효율적일 수 있기 때문이다. fs모듈의 fs.rename, fs.renameSync 메소드 이름에서도 유추해 볼 수 있듯이 nodejs 에서는 **비동기 방식을 지향**하기 때문에 메소드 이름에 **Sync를 명시하지 않을 경우 비동기**로 실행되는 것을 알 수 있다.

권장되지는 않지만, 대부분의 fs모듈 비동기 메소드에서 콜백 인수를 생략할 수 있으며, 이 경우 기본 콜백이 사용된다. 원래 호출 사이트로 추적(오류)을 가져오려면 NODE_DEBUG 환경 변수를 설정해야 한다. 하지만 향후에 오류가 발생할 수 있기 때문에 사용하지 않는 것이 좋다.

```js
$ cat script.js
function bad() {
  require('fs').readFile('/');
}
bad();

$ env NODE_DEBUG=fs node script.js
fs.js:88
        throw backtrace;
        ^
Error: EISDIR: illegal operation on a directory, read
    <stack trace.>
```

## cluster

싱글스레드인 Node.js가 cpu 코어를 모두 사용할 수 있게 해주는 모듈이다.

(Node.js 에서 손쉽게 멀티 스레드를 구현하기 위해 cluster 모듈을 사용한다.)

클러스터 모듈은 모든 서버 포트를 공유하는 하위 프로세스를 쉽게 생성할 수 있도록 한다.

```js
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length; //cpu의 수

if (cluster.isMaster) {
	//초기에 실행되는 인스턴스는 master
	console.log(`Master ${process.pid} is running`);

	// Fork workers.
	for (let i = 0; i < numCPUs; i++) {
		cluster.fork(); //워커(프로세스) 생성
	}

	cluster.on('exit', (worker, code, signal) => {
		console.log(`worker ${worker.process.pid} died`);
	});
} else {
	// Workers can share any TCP connection
	// In this case it is an HTTP server
	http
		.createServer((req, res) => {
			res.writeHead(200);
			res.end('hello world\n');
		})
		.listen(8000);

	console.log(`Worker ${process.pid} started`);
}
```

이제 node.js 를 실행하면 작업들 간에 8000번 포트가 공유됩니다.

```js
$ node server.js
Master 3596 is running
Worker 4324 started
Worker 4520 started
Worker 6056 started
Worker 5644 started
```

node.js 는 V8 엔진이 가진 제한으로 한 프로세스당 512MB~1.5GB 메모리만 사용하도록 설정되어 있다. 하지만 클러스터를 사용하면 서버의 리소스를 최대한 낭비없이 활용할 수 있을 것이다.
