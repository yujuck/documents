## 문제 상황

>a client request body is buffered to a temporary file /var/cache/nginx/client_temp/,,,,

nginx error log를 확인하는데 위와 같은 로그가 계속 남아있는걸 발견했다.<br />
에러는 아니고 warning 메세지인데,<br />
클라이언트에서 보낸 데이터가 할당된 버퍼에 들어갈 수 없을 만큼 크기 때문에 임시 파일을 만들고 해당 파일에 저장하겠다는 메세지이다.<br />

nginx 설정에 `client_body_buffer_size`를 추가해주면 일정 크기까지는 해당 메세지를 남기지 않을 수 있다.

## client_body_buffer_size, client_max_body_size

### client_body_buffer_size
```
Syntax:	client_body_buffer_size size;
Default: client_body_buffer_size 8k|16k;
```
Sets buffer size for reading client request body. In case the request body is larger than the buffer, the whole body or only its part is written to a temporary file. By default, buffer size is equal to two memory pages. This is 8K on x86, other 32-bit platforms, and x86-64. It is usually 16K on other 64-bit platforms.

client_body_buffer_size는 클라이언트의 요청 바디를 읽기 위한 buffer size를 설정한다.<br />
너무 크게 설정해도 한번에 큰 용량의 데이터를 읽어들이게 되기 때문에 서버 환경에 맞게 설정하는 것이 중요하다.<br />

### client_max_body_size
```
Syntax:	client_max_body_size size;
Default: client_max_body_size 1m;
```
Sets the maximum allowed size of the client request body. If the size in a request exceeds the configured value, the 413 (Request Entity Too Large) error is returned to the client. Please be aware that browsers cannot correctly display this error. Setting size to 0 disables checking of client request body size.

client_body_buffer_size를 설정할 때 같이 등장하는게 `client_max_body_size`인데,
허용할 client request의 최대 body size를 설정한다.<br />
설정한 크기보다 큰 요청이 들어오면 413 오류 코드를 리턴한다.<br />

## application 설정
nginx에서 위처럼 client request의 크기에 대해 설정을 할 수도 있지만,
application 레벨에서도 설정이 가능하다.

우리 플랫폼에서 가격 호출할 때 전달되는 body의 크기가 좀 큰 편이라 그런지 postman으로 요청 body를 그대로 가져와서 로컬로 호출하면 413에러가 난다.<br />
로컬 주소로 호출하기 때문에 nginx를 거치는 게 아니여서 application에서 처리를 해야한다.

```js
import { json } from 'express';
app.use(json({ limit: '10mb' }));
```

Nest에서 app에 대한 설정을 하는 main.ts 파일에서 다음과 같이 설정하면 limit을 조절할 수 있다.

## 둘다 설정해야하나?
application으로 요청이 전달되기 전에 nginx에서 미리 처리가 될텐데 둘다 설정해야하는지 질문을 했는데.. 뭐라고 하셨더라.. 기억에서 또 사라짐..<br />
흠 그냥 생각해보면.. <br />
application마다 허용할 크기가 다르게 관리되어야 하면..? application에도 설정해야하지 않을까?! <br />

## user 설정의 중요성
> 추가적으로 얻게된 지식에 검색 내용 더하기

nginx.conf에서 user를 설정할 수 있다. 이 user는 Nginx worker process의 user를 의미한다.<br />
기본적으로는 nginx으로 설정을 하는데 보안을 위해 임의의 user로 설정하기도 한다고 한다.<br />

만약 새롭게 설정한 user가 기존에 nginx가 가지고 있던 권한들을 동일하게 가져야 특정 상황에 대한 오류가 발생하지 않는다.

위의 문제 상황에서 body 데이터는 `/var/cache/nginx/client_temp` 폴더에 임시로 저장이 된다.<br /> 근데 새롭게 설정한 user가 해당 폴더에 대한 권한이 없으면 body 데이터를 읽어올 수 없기 때문에 요청 데이터에 응답을 제대로 할 수 없는 오류 상황이 생길 수 있다.<br />

`서버에서`라고 하는게 맞는지 `리눅스`라고 하는게 맞는지 모르겠지만 user의 구분과 해당 user가 가지고 있는 권한은 중요한 개념인 것 같다..<br />

<br />
항상 추가적인 지식 전달해주시는 팀장님 감사 😀🙏





