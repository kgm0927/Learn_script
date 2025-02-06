
고(go)의 특징은 다음과 같다.

- 루비 같은 인터프리터 언어보다 빠른 컴파일 언어이다.
- 동시성 프로그래밍을 쉽게 작성할 수 있다.
- 하드웨어에서 바로 실행할 수 있다.
- 패키지와 같은 최신 기능을 사용할 수 있으면서도 클래스 등 불필요한 기능은 제외했다.

고의 장점은 이 외에도 많다. 그런 만큼 단점도 있지 않을까 싶지만, 필자에게는 거슬리는 문제점이 전혀 없다. 고를 설계한 사람이 필자를 귀찮게 하는 다른 언어들의 문제점들을 모두 없애준 것이 아닐까 싶은 정도다. 고는 필자가 프로그래밍을 사랑하게 되었을 때를 떠올리게 했다. 만약 프로그램의 무엇인가가 잘못되었다면, 언어에 수많은 기능에 압도되어 생긴 문제가 아니라 바로 필자의 잘못일 것이다. 자바가 푸주 칼, 배시가 과도라면 고는 일본도(katana)라 하겠다. 사무라이는 일본도를 자기 몸이 확장된, 자신의 일부로 본다고 한다. 자신이 평생을 바쳐 완전하게 다룰 가치가 있는 대상, 그것이 고를 바라보는 필자의 감정이다.

소프트웨어 분야에서 고 언어가 가장 큰 영향을 끼친 분야라면 역시 분산 시스템이라 하겠다. 도커(Docker)나 쿠버네티스(Kubernetes), [etcd](https://www.ibm.com/kr-ko/topics/etcd), 프로메테우스(Prometheus)와 같은 프로젝트들은 고로 개발되었다. 구글은 직면했던 소프트웨어적인 문제들을 풀고자 고 언어와 go의 표준 라이브러리를 개발했다. 멀티코어 프로세서와 네트워크 시스템, 대량의 연산 클러스터, 다시 말해 분산 시스템과 큰 규모의 코드 줄 수, 프로그래머들, 머신들을 고려해서 고를 개발한 것이다. go 프로그래머라면 이런 시스템을 사용해보았을 것이다. 어떤 식으로 작동하고, 디버깅하고, 기여할 수 있는지 궁금할 것이고 나아가 유사한 프로젝트를 만들고 싶을 것이다. 필자가 그랬다. 회사에서는 도커와 쿠버네티스를 사용했고, 개인적으로 카프카(kafka, 분산 커밋 로그)를 go 언어로 구현한 Jocko를 만들었다.

고 언어로 이 모든 걸 만들려면 어디서부터 시작해야 할까? 분산 서비스를 만드는 프로젝트는 결코 작거나 쉬운 일이 아니다. 모든 기능을 한 번에 구현하려 한다면 코드들은 엉망진창이 되고 머릿속은 터져 나갈 것이다. 하나씩 차근히 프로젝트를 만들자. ==우선 HTTP 서비스를 이용하여 커밋 로그 JSON을 구현해보는 게 좋다.== go를 이용해 HTTP 서버를 구현해본 적이 없더라도 문제없다. API를 만들어 클라이언트가 네트워크를 통해 호출하는 방법을 알려줄 것이다. 커밋 로그 API에 관해 먼저 배운 다음, 코드를 작성할 환경 설정을 해보자. 이후 이 책을 따라가며 프로젝트를 만들어보자.


---

# 1.1 분산 시스템의 HTTP의 JSON 서비스 적용하기

HTTP상의 JSON API는 웹에서 가장 일반적으로 사용하는 API이다. 대부분의 언어가 JSON을 기본적으로 지원하므로 만들기 쉽기 때문이다. 또한 JSON은 사람이 읽기 쉽고, HTTP API는 터미널에서 curl로 호출하거나 브라우저로 사이트를 열기만 하면 된다. HTTP 클라이언트도 많다. 시도해보고 싶은 좋은 웹 서비스 아이디어가 있고 사람들에게 빨리 소개하고 싶다면 JSON/HTTP로 바로 만들 수 있다.

JSON/HTTP는 작은 웹 서비스에만 적합한 것이 아니다. 웹 서비스를 제공하는 대부분의 기술 업체가 적어도 하나의 JSON/HTTP API를 제공하는데, 회사 내 프런트엔드 엔지니어가 사용하거나 외부 엔지니어가 서드파티 애플리케이션을 만들 때 사용한다. 회사 내부에서만 사용하는 웹 API라면, protobuf 같은 기술을 이용하여 (JSON/HTTP가 제공하지 않은) 자료형 검사(type checking)나 버전 관리 등의 기능을 넣어주는 이점을 누릴 수도 있겠지만, 외부에 개방할 때문 역시 접근성이 좋은 JSON/HTTP를 사용한다. 필자가 몸담아 온 회사들이 역시 이 아키텍처를 사용한다. 세그먼트(Segment)사에서는 수년간 JSON/HTTP 기반 아키텍처로 월 수천억 개의 API 호출을 처리했고, 이후 내부 서비스는 protobuf/gRPC로 효율성을 개선했다. 베이스캠프(Basecamp)사에서는 (필자가 아는 한 현재까지도) 모든 서비스가 JSON/HTTP 기반으로 제공된다.

JSON/HTTP는 인프라스트럭처 프로젝트의 API에 잘 맞는다. 유명한 오픈소스인 일래스틱서치(Elasticsearch, 분산 검색 엔진)나 etcd(유명한 키-값 저장소, 쿠버네티스를 포함해 많은 프로젝트에서 사용) 같은 프로젝트들도 고객이 사용하는 API로 JSON/HTTP를 사용하여, 내부 노드 간 통신에는 성능 향상을 위해 그들만의 바이너리 프로토콜을 사용한다. JSON/HTTP로 어떠한 서비스든 만들 수 있다는 의미다.

go는 표준 라이브러리만으로 JSON를 사용하는 HTTP 서버를 만들 수 있으며, JSON/HTTP 웹 서비스를 만드는 데 아무 문제가 없다. 필자도 루비, Node.js, 자바, 파이썬 등으로 JSON/HTTP 서비스를 만들어 왔지만, go가 가장 좋았다. 고의 선언 태그와 표준 라이브러리의 encoding/json 패키지를 사용하면 직렬화(marshaling)가 한결 간편하다. 이제부터 만들어보자.

---
# 1.2 프로젝트 환경 설정하기

프로젝트 디렉터리부터 만들자. 의존성에 대해서는 GOPATH를 이용하지 않고 Go 1.13+ 버전부터 지원하는 modules를 사용하겠다. 프로젝트명은 proglog로 지정한다. 터미널을 열고 다음 명령을 실행하면 프로젝트 기본 설정이 이루어진다.

``` shell
$ mkdir proglog
$ cd proglog
$ go mod init github.com/.../proglog
```

이 책에서는 임포트 경로(import path)로 `github.com/trasvisjeffery/proglog`를 사용한다는 것을 잊지 말자.


---
# 1.3 커밋 로그 프로토타입 만들기

**커밋 로그**에 관해서는 지속적인 커밋 로그 라이브러리를 만들 3장에서 자세히 다루겠다. <mark style="background: #ADCCFFA6;">당장은 커밋 로그가 추가만 할 수 있는 데이터 구조체라는 점만 기억하자.</mark> 연속한 레코드로 이루어지며 시간순으로 정렬된다. 이처럼 간단한 커밋 로그는 슬라이스로 만든다.

프로젝트 폴더 아래에 `internal/server` 디렉터리를 만들고 다음 코드로 log.go 파일을 만들자.

```go

// LetsGo/internal/server/log.go

package server

import (
	"fmt"
	"sync"
)

type Log struct {
	mu      sync.Mutex
	records []Record
}

type Record struct {
	Value  []byte `json:"value"`
	Offset uint64 `json:"offset"`
}

func NewLog() *Log {
	return &Log{}
}

func (c *Log) Append(record Record) (uint64, error) {
	c.mu.Lock()
	defer c.mu.Unlock()
	record.Offset = uint64(len(c.records))
	c.records = append(c.records, record)
	return record.Offset, nil
}

func (c *Log) Read(offset uint64) (Record, error) {
	c.mu.Lock()
	defer c.mu.Unlock()
	if offset >= uint64(len(c.records)) {
		return Record{}, ErrOffsetNotFound
	}
	return c.records[offset], nil
}

var ErrOffsetNotFound = fmt.Errorf("offset not found")

```

로그에 레코드를 추가하려면 슬라이스에 추가한다. ==인덱스로 레코드를 읽는다는 것은 슬라이스의 해당 인덱스의 레코드를 찾는 다는 것이다.== 클라이언트가 찾는 오프셋이 없으면, 오프셋을 찾을 수 없다는 에러를 리턴한다. 아직은 프로토타입이기에 구현이 단순하다. 여기서부터 살을 붙여나가자.

>[!caution] 예제 코드 파일 경로의 네임스페이스는 무시하자.
> 예제 코드의 파일 경로가 `internal/server/log.go`가 아니라 `LetsGo/internal/server/log.go`로 되어 있다. 앞으로 살펴볼 예제 코드들도 장(chapter)마다 서로 다른 네임스페이스를 추가할 것이다. 책을 쓰면서 필요에 따라 추가한 것이니 실제 경로는 이를 빼로 생각하자.


---
# 1.4 HTTP의 JSON 만들기

JSON/HTTP 웹 서버를 만들어보자. <mark style="background: #ADCCFFA6;">고 웹 서버는 API 하나의 엔드포인트마다 하나의 함수로 구성된다.</mark> 바로 `net/HTTP` 패키지의 `HandleFunc(pattern string, handler(ResponseWriter,*Request)) `함수다. 두 개의 엔드포인트를 만들어보자. ==로그에 쓰는 생산(Produce)과 로그에서 읽는 소비(Consume)이다.== JSON/HTTP 웹 서버를 만들 때 각 핸들러(리스너)는 다음 세 단계로 구성한다.


1. **요청**(request)의 JSON **본문**(body)을 구조체로 역직렬화하기
2. 요청에 관한 엔드포인트의 로직을 실행해서 결과 얻기
3. 결과를 직렬화하여 **응답**(reponse)에 쓰기

핸들러가 좀 더 복잡해지면 ==코드를 분리==하고, ==요청-응답 처리를 미들웨어로 옮기고==, ==비즈니스 로직을 스택의 좀 더 아래쪽으로 옮겨야 한다.==

HTTP 서버에 사용자를 위한 함수를 추가해보자. server 디렉터리에 http.go 파일을 만들어 다음 코드를 넣는다.

``` Go
// LetsGo/internal/server/http.go
package server

import (
	"encoding/json"
	"net/http"

	"github.com/gorilla/mux"
)

func NewHTTPServer(addr string) *http.Server {
	httpsrv := newHTTPServer()
	r := mux.NewRouter()
	r.HandleFunc("/", httpsrv.handleProduce).Methods("POST")
	r.HandleFunc("/", httpsrv.handleConsume).Methods("GET")
	return &http.Server{
		Addr:    addr,
		Handler: r,
	}
}

```


`NewHTTPServer(addr string)`함수는 서버의 주소를 매개변수(parameter)로 받아서 `*http.Server`를 리턴한다. 많이들 쓰는 gorilla/mux 패키지를 사용해 요청(request)을 대응하는 핸들러에 짝지어 주었다. `/` 엔드포인트를 호출하는 HTTP GET 요청은 consume 핸들러가 처리하여 로그에서 레코드를 읽도록 했다. ==생성한 httpServer는 `*net/http.Server`로 다시 싸서 사용자는 ListenAndServe()를 이용해 요청을 처리할 수 있다.==
이어서 서버를 정의하고 요청과 응답 구조를 정의해보자. `NewHTTPServer(addr string)`에 이어서 다음 코드를 추가하자.

```Go

// LetsGo/internal/server/http.go

type httpServer struct {
	Log *Log
}

func newHTTPServer() *httpServer {
	return &httpServer{
		Log: NewLog(),
	}
}

// 호출자가 로그에 추가하길 원하는 레코드를 담는다.
type ProduceRequest struct {
	Record Record `json:"record"`
}

// 호출자에게 저장한 오프셋을 알려준다.
type ProduceResponse struct {
	Offset uint64 `json:"offset"`
}

// 원하는 레코드의 오프셋을 담는다.
type ConsumeRequest struct {
	Offset uint64 `json:"offset"`
}

// 오프셋에 위치하는 레코드를 보내준다.
type ConsumeResponse struct {
	Record Record `json:"record"`
}

```

==서버는 로그를 참조하고, 참조하는 로그를 핸들러에 전달한다.== ==`ProduceRequest`==는 호출자가 로그에 추가하길 원하는 레코드를 담고, ==`ProduceResponse`==는 호출자에게 저장한 **오프셋**(offset)을 알려준다. ==`ConsumeRequest`==는 원하는 레코드의 오프셋을 담고,==`ConsumeResponse`==는 오프셋에 위치하는 레코드를 보내준다. 30여 줄의 코드만으로 이 정도의 서버를 만들어냈다.

이제 서버의 핸들러를 만들자. 앞의 코드에 이어서 다음 내용을 추가하자.

``` Go
// LetsGo/internal/server/http.go

func (s *httpServer) handleProduce(w http.ResponseWriter, r *http.Request) {
	var req ProduceRequest
	err := json.NewDecoder(r.Body).Decode(&req)
	if err != nil {
		http.Error(w, err.Error(), http.StatusBadRequest)
		return
	}

	off, err := s.Log.Append(req.Record)
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	res := ProduceResponse{Offset: off}
	err = json.NewEncoder(w).Encode(res)

	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

}

```

==**produce** 핸들러는 앞에서 이야기한 세 단계로 구현했다. 요청을 구조체로 역직렬화하고, 로그에 추가한 다음, 오프셋을 구조체에 담아 직렬화하여 응답하다.== **consume** 핸들러도 유사하다. 다음 코드를 produce 핸들러에 이어서 넣어주자.



```Go
// LetsGo/internal/server/http.go

func (s *httpServer) handleConsume(w http.ResponseWriter, r *http.Request) {
	var req ConsumeRequest
	err := json.NewDecoder(r.Body).Decode(&req)
	if err != nil {
		http.Error(w, err.Error(), http.StatusBadRequest)
		return
	}

	record, err := s.Log.Read(req.Offset)
	if err == ErrOffsetNotFound {
		http.Error(w, err.Error(), http.StatusNotFound)

		return
	}

	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	res := ConsumeResponse{Record: record}
	err = json.NewEncoder(w).Encode(res)
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}
}
```

==consume 핸들러는 produce 핸들러와 비슷한 구조이지만 `Read(offset uint64)`를 호출하여 로그에서 레코드를 읽어낸다.== 이 핸들러는 좀 더 많은 에러 체크를 하여 정확한 상태 코드(status code)를 클라이언트에 제공한다. 서버가 요청을 핸들링할 수 없다는 에러도 있고, 클라이언트가 요청한 레코드가 존재하지 않는다는 에러도 있다.

서버 라이브러리는 모두 만들었다. 이 라이브러리를 사용하여 실행하는 프로그램으로 만들어보자.

---

# 1.5 서버 실행하기

서버를 실행하려면 `main()` 함수를 포함한 main 패키지를 만들어야 한다. 프로젝트 디렉터리에 `cmd/server` 디렉터리를 만들고 main.go 파일에 다음 코드를 넣어주자.

``` go

// LetsGo/cmd/main.go

package main

import (
	"log"

	"github.com/proglog/internal/server"
)

func main() {
	srv := server.NewHTTPServer(":8080")
	log.Fatal(srv.ListenAndServe())
}

```

`main()` 함수는 서버를 생성하고 실행하는 것이 전부이다. `(localhost:8080) `이라는 주소(address)정보만 넣어주고, 요청을 받아서 처리하겠다는 `ListenAndServe()` 메서드를 실행한다. 직접 만든 `httpServer`를 `*net/http.Server`의 `NewHTTPServer()` 함수로 감싸서 추가적인 관련 코드 작성을 줄였다.

---
# 1.6 API 테스트하기

서버를 실행하면 JSON/HTTP 커밋 로그 서비스가 활성화되고 curl 명령으로 테스트할 수 있다. 먼저 다음 명령으로 서버를 실행하자.

```shell
$ go run main.go
```

터미널에 또 하나의 탭을 열어서 다음 curl 명령으로 레코드들을 로그에 추가해보자.

```shell
$ curl -X POST localhost:8080 -d \
'{"record":{"value":"TGV0J3MgR28gIzEK"}}'

$ curl -X POST localhost:8080 -d \
'{"record":{"value":"TGV0J3MgR28gIzIK"}}'

$ curl -X POST localhost:8080 -d \
'{"record":{"value":"TGV0J3MgR28gIzMK"}}'
```

go의 encoding/json 패키지는 `[]byte`를 base64-encoding string으로 인코딩한다. 그래서 앞의 curl 명령에 이미 base64로 인코딩된 "value"를 넣었다. 다음 curl 명령을 실행하면 이번에는 로그에서 각각의 오프셋에 해당하는 레코드를 서버로부터 응답받는다.

```shell
$ curl -X POST localhost:8080 -d '{"offset":0}'
$ curl -X POST localhost:8080 -d '{"offset":1}'
$ curl -X POST localhost:8080 -d '{"offset":2}'

```

축하한다. 이제 막 간단한 JSON/HTTP 서비스를 만들고 작동을 확인한다.

---
# 1.7 마치며

1장에서는 JSON/HTTP 커밋 로그 서비스를 만들어보았다. JSON으로 요청을 받고 응답을 보내며, 메모리 로그에 저장했다. 다음에는 프로토콜 버퍼를 사용해서 API 자료형을 관리하고 코드를 생성한 다음 gRPC 서비스를 만들어보자. gRPC는 오픈 소스 고성능 RPC(remote procedure call)프레임워크로, 분산 서비스를 만들기에 매우 좋다.

