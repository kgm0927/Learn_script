
지금까지 프로젝트와 프로토콜 버퍼를 세팅하고 로그 라이브러리를 만들었다. 구현한 로그 라이브러리는 한 사람이 한순간에 한 대의 컴퓨터만 사용할 수 있다. 게다가 그 사용자는 라이브러리의 API를 배우고 코드를 실행하며 자신의 디스크에 로그를 저장해야 한다. 이렇게 번거로운 작업을 감내할 사용자가 거의 없다고 본다면, 우리의 고객은 매우 제한적이다. 하지만 로그 라이브러리를 웹 서비스로 제공한다면 어떨까? 이러한 문제들을 해결할 수 있을 뿐만 아니라 더 많은 고객의 관심을 끌 수 있을 것이다. 하나의 컴퓨터에서 작동하는 프로그램과 비교했을 때, 네트워크로 제공하는 서비스는 다음과 같은 세 가지 이점을 제공한다.

- 가용성과 확장성을 위해 여러 컴퓨터에 걸쳐 실행할 수 있다.
- 여러 사람이 같은 데이터로 소통할 수 있다.
- 사람들이 쉽게 접근할고 사용할 수 있는 인터페이스를 제공한다.

이러한 이점을 활용할 수 있는 서비스로는 어떤 것이 있을까? 프런트엔드에 퍼블릭 API를 만들거나 인프라스트럭처 도구를 만드는 경우가 있다. 사업을 위한 서비스를 구축할 수도 있다(사람들이 라이브러리 사용에 돈을 지불하는 경우는 드물다).

==4장에서는 로그 라이브러리를 기반으로 여러 사람이 같은 데이터로 소통하는 서비스를 만들어보겠다.== 서비스는 여러 컴퓨터에 걸쳐서 작동한다. 클러스터에 대한 구현은 8장에서 구현할 예정이다. 현재 분산 서비스에 대한 요청을 처리하는 최고의 도구는 gRPC이다.

---
# 4.1 gRPC에 관하여

과거 분산 서비스를 만들 때 가장 어려웠던 두 가지 문제는 호환성을 유지하는 문제와 서버- 클라이언트 사이의 성능을 관리하는 문제였다.

서버와 클라이언트가 호환성을 유지함으로써 클라이언트가 보내는 요청을 서버가 이해하고, 서버의 응답 역시 클라이언트가 이해할 수 있다는 것을 보장하고자 했다. 서버의 호환성을 깨는 변경사항을 만들더라도 기존 고객들에게는 아무 문제가 없도록 만들려 했다. 이러한 문제는 API 버저닝(API versioning)으로 해결했다.

좋은 성능을 유지하기 위해 가장 중요한 것은 데이터베이스 쿼리의 최적화와, 비즈니스 로직의 구현에 사용하는 알고리즘의 최적화이다. 이렇게 최적화한 다음에는 서버가 요청을 역직렬화하고, 응답을 직렬화하는 부분을 가능한 한 빠르게 만들고, 클라이언트와 서버 간 통신에서의 오버헤드를 줄여야 한다. 요청마다 새로운 연결을 사용하기보다는 하나의 연결을 오래 사용하는 것도 오버헤드를 줄이는 방법의 하나이다.

그런 점에서 필자는 구글이 뛰어난 성능의 RPC 오픈 소스 프레임워크인 gRPC를 발표했을 때 정말 기뻤다. gRPC는 분산 시스템을 만드는 과정에서 문제 해결에 큰 도움이 되었으며, 여러분의 업무도 쉽게 만들어 주었다. 그렇다면 서비스를 만들 때 gRPC는 어떻게 도움이 될까?

---
# 4.2 서비스를 만들 때의 목표

네트워크로 제공하는 서비스를 만들 대 지향할 목표가 무엇인지, 그리고 이러한 목표에 다가설 때 gRPC 어떤 도움이 되는지를 정리해본다.

- **단순화**: 네트워크 통신은 기술적이며 복잡하다. 서비스를 만든다면 요청-응답의 직렬화 같은 세부적인 기술보다는 서비스로 풀어내려는 문제 자체에 집중하고, 기술적인 세부 사항은 추상화되어 쉽게 가져다 쓸 수 있는 API를 사용하고 싶을 것이다. 그러면서도 때로는 이러한 추상화 아래의 세부사항에 접근해야 할 때도 있다.

단순한 추상화 수준의 프레임워크 중에서 gRPC의 추상화는 중,상급 수준이라 할 수 있다. 익스프레스(Express)보다는 높은 추상화이다. gRPC는 어떻게 직렬화할지, 엔드포인트(endpoint)를 어떻게 구성할지 정해져 있으며 양방향 스트리밍을 제공한다. 하지만 **루비 온 레일즈**(Ruby on rails)보다는 낮다고 할 수 있는데, <mark style="background: #ADCCFFA6;">레일즈는 요청 처리에서부터 데이터 저장, 애플리케이션 구조에 이르기까지 모두 처리하기 때문이다.</mark> gRPC는 미들웨어를 확장할 수 있다. 서비스를 만들다 보면 로깅, 인증, 속도 제한(rate limiting), 트레이싱(tracing)과 같은 많은 문제를 만나는데, gRPC 커뮤니티는 이러한 문제를 해결할 수 있는 많은 미들웨어를 만들어왔다.

- **유지보수**: 서비스의 첫 버전을 만들기까지 걸리는 시간은 서비스에 쏟을 전체 시간의 일부에 부과하다. 서비스가 운영을 시작하고 고객이 사용하게 되면 하위 호환성에 신경 써야 한다. <mark style="background: #ADCCFFA6;">요청-응답 형태의 API에서 가장 간단한 해법은 버전으로 구분하여 인스턴스를 두는 것이다.</mark>

gRPC에서는 작은 변경일 때는 protobuf의 필드 버저닝으로 충분하며, 메이저(major) 변경이 있을 때는 서비스의 여러 버전을 쉽게 작성하고 실행할 수 있다. 여러분이나 동료가 서비스를 만들 때 모든 요청과 응답의 자료형을 체크하면 실수로라도 하위 호환성을 깨는 일을 막을 수 있다.

- **보안**: 네트워크에 서비스를 공개하면, 네트워크 혹은 인터넷을 사용하는 모든 이용자에게 서비스를 공개하는 셈이다. 누가 서비스에 접근하는지, 무엇을 할 수 있는지 제어하는 것은 중요하다.

gRPC는 **보안 소켓 계층**(Secure Sockets Layer, SSL)과 **전송 계층 보안**(Transport Layeer Security,TLS)를 지원하여 클라이언트와 서버 사이를 오가는 모든 데이터를 암호화한다. 또한 요청에 인증서를 첨가하게 하여 누가 요청을 보냈는지 알 수 있다. 5장에서 보안을 좀 더 다루겠다.

- **사용성**: 서비스를 만든다는 건 결국 사용자들이 서비스를 사용해서 문제를 해결하도록 하는 것이다. 서비스가 사용하기 쉬울수록 더 많은 사용자가 찾는다. API에 잘못된 요청을 보내는 것처럼 사용자가 실수하면 무엇이 잘못되었는지를 알려주는 식으로, 사용자가 점점 더 서비스에 익숙해지게 만들어야 한다.
<mark style="background: #ADCCFFA6;">gRPC에서는 서비스 메서드, 요청-응답과 그 본문(body) 모두를 자료형으로 정의한다.</mark> <mark style="background: #ADCCFFA6;">컴파일러는 protobuf에서 코드로 모든 코멘트를 복사하여 사용자가 자료형 정의만으로 이해하기 어려운 부분을 보완한다.</mark> 사용자는 자신이 만든 코드를 자료형 검사해서 API를 제대로 사용하는지 확인할 수 있다. 요청, 응답, 모델, 직렬화까지 모든 것을 자료형 검사하는 것은 서비스 사용하는 법을 배울 때 큰 도움이 된다. 또한 godoc을 이용하면 gRPC API의 상세한 정보를 알 수 있다. 이처럼 유용한 두 기능을 제공하는 프레임워크는 흔치 않다.


- **성능**: 누구든지 서비스가 리소스는 적게 사용하면서 성능은 좋기를 바란다. 예를 들어 구글 클라우드 플랫폼(Google Cloud Platform, GCP)에서 N1 머신 계열의 표준 머신 유형으로 n1-standard-2 대신 n1-standard-1를 해 사용할 수 있다면 예상 비용은 월 71달러 정도에서 월 35달러 정도로 절반 이상 줄어든다.

GRPC는 protobuf와 HTTP/2를 기반으로 만들어졌다. protobuf는 직렬화에 유리하고 HTTP/2는 연결을 오래 유지할 수 있는 이점이 있다. 덕분에 gRPC를 사용하는 서비스는 효율적으로 구동하며 서버 비용을 아낄 수 있다.


- 확장성: 확장성은 부하를 여러 컴퓨터에 골고루 분산하는 것, 또는 여러 사람이 함께 프로젝트를 개발하는 것을 의미한다. gRPC는 두 경우 쉽게 확장할 수 있다.

gRPC를 사용하면 필요에 따라 다양한 로드 밸런싱을 쓸 수 있다. 클라이언트(thick client-side)로드 밸런싱, 프록시 로드 밸런싱, 색인(lookside) 로드 밸런싱, 그리고 서비스 메시(service mesh)등이 있다. 또한 gRPC를 사용하면 gRPC가 지원하는 다양한 언어로 서비스를 클라이언트 및 서버로 컴파일할 수 있다. 이처럼 다양한 언어로 서로 통신하는 서비스를 만들 수 있으므로 다양한 언어를 사용하는 개발자가 프로젝트에 참여하기 쉽다. 즉, 확장성이 좋다.

서비스의 목표를 알아보았으니 이제 그 목표를 만족하는 gRPC 서비스를 만들어보자.

---
# 4.3 gRPC 서비스 정의하기

==gRPC란 관련이 있는 RPC 엔드포인트들을 묶은 그룹이다.== 어떤 관련이 있는지는 개발자가 판단한다. 흔한 예로는 RESTful에서 같은 자원에 대해 작업하는 엔드포인트들을 그룹으로 묶는 경우가 있다. 다만, 반드시 서로 관련되어야 하는 것은 아니다. 좀 더 일반적으로 말하자면 어떠한 문제를 해결하는 데 필요한 엔드포인트이다. 우리가 만드는 서비스의 목적은 사용자가 자신들의 로그에 읽고 쓸 수 있게 하는 것이다.

gRPC 서비스를 만든다는 것은 서비스를 protobuf로 정의하고, 프로토콜 버퍼를 클라이언트와 서버로 된 코드로 컴파일하여 구현하는 것이다. 레코드 메시지를 정의했던 log.proto 파일을 열어서 다음과 같은 정의를 기존 메시지 위에 추가하자.

``` proto
// ServeRequestsWithgRPC/api/v1/log.proto
```

**service**키워드는 컴파일러가 생성해야 할 서비스라는 의미이며, rpc로 시작하는 각각의 줄은 서비스의 엔드포인트이고, 요청과 응답의 자료형을 명시했다. 요청과 응답은 컴파일러가 Go 구조체가 변환해 줄 메시지이다.

스트리밍 엔드포인트는 다음 두 종류가 있다.

- **ConsumeStream**: ==서버 측 스트리밍 RPC이다.== 클라이언트가 서버에 요청을 보내면, 서버는 연속한 메시지들을 읽을 수 있는 스트림을 보내준다.
- **ProduceStream**: 양방향 스트리밍 RPC이다. 클라이언트와 서버 양쪽이 읽고 쓸 수 있는 스트림(read-write stream)을 이용해 서로 연속한 메시지들을 보낸다. 두 스트림은 서로 영향을 주지 않고 독립적으로 작동하므로 서버와 클라이언트는 어떠한 순서로든 원하는 대로 읽고 쓸 수 있다. 예를 들어 서버가 클라이언트의 모든 요청을 다 받은 다음에 응답을 보낼 수 있다. 서버가 요청을 한꺼번에 처리해야 하거나 여러 요청의 처리 결과를 모아야 하는 경우가 있겠다. 필요하다면 하나의 요청마다 그에 대한 하나의 응답을 보낼 수도 있다.

``` proto

// ServeRequestWithgRPC/api/v1/log.proto

message ProduceRequest{
    Record record=1;
}

message ProduceResponse{
    uint64 offset=1;
}

message ConsumeRequest{
    uint64 offset=1;
}

message ConsumeResponse{
    Record record=1;
}
```

로그 서비스에 정의한 대로 클라이언트 측과 서버 측 코드를 생성하려면 protobuf 컴파일러에 gRPC 플러그인을 사용해야 한다.

---
# 4.4 gRPC 플러그인으로 컴파일하기

컴파일은 그리 어렵지 않다. 다음과 같이 gRPC 패키지부터 설치하자.

``` shell
┌──(kali㉿kali)-[~/…/src/github.com/distributed_service_go/Part4-ch4-ServerRequestWithgRPC]
└─$ go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
                                                                                                                                                         
                                                                                                             
┌──(kali㉿kali)-[~/…/src/github.com/distributed_service_go/Part4-ch4-ServerRequestWithgRPC]
└─$ go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
go: downloading google.golang.org/grpc/cmd/protoc-gen-go-grpc v1.5.1
go: downloading google.golang.org/protobuf v1.34.1
```

그리고 **Makefile** 파일의 compile 타깃을 다음과 같이 수정하여 gRPC 플러그인을 활성화하고 gRPC 서비스를 컴파일하게 하자.

``` proto

// ServeRequestWithgRPC/Makefile

compile:

protoc api/v1/*.proto \

--go_out-. \

--go_grpc_out=. \

--go_opt=paths=source_relative \

--go-grpc_out=paths=source_relative \

--proto_path=.
```

`$ make compile`을 실행하고 `api/v1` 디렉터리의 log_grpc.pb.go 파일을 열어서 생성한 코드를 확인하자. 컴파일러가 gRPC 로그 클라이언트를 만들고 로그 서비스 API를 구현할 준비를 해둔 것을 알 수 있다.

---
# 4.5 gRPC 서버 구현하기

컴파일러가 서버 측 로그 서비스 구현 API를 생성했으므로, 서버를 구현하려면 구조체를 만들고 protobuf에 정의해둔 서비스에 맞은 구조체를 메서드에 구현한다.

`internal` 패키지에는 go에서 마법과 같은 패키지로, 오직 가까온 코드에서만 임포트(import)할 수 있다. 예를 들어 `/a/b/c/internal/d/e/f`의 코드는 `/a/b/c` 디렉터리와 그 아래 코드에서만 임포트할 수 있다. `/a/b/g` 디렉티리의 코드는 임포트할 수 없다. 이 파일에 **server**라는 패키지명으로 서버를 구현할 것이다. 가장 먼저 서버 자료형을 정의하고, 서버 인스턴스를 만드는 팩토리 함수를 만들자.

``` go

// ServeRequestsWithgRPC/internal/server/server.go

package server

  

import api_v1 "github.com/distributed_service_go/Part4-ch4-ServerRequestWithgRPC/api/v1"

  

type Config struct {

CommitLog CommitLog

}

  

var _ api_v1.LogServer = (*grpcServer)(nil)

  

type grpcServer struct {

api_v1.UnimplementedLogServer

*Config

}

  

func newgrpcServer(config *Config) (srv *grpcServer, err error) {

srv = &grpcServer{

Config: config,

}

return srv, nil

}
```

log_grpc.pb.go 파일의 API를 구현하려면 `Consume()`과 `Produce()` 핸들러를 구현해야 한다. gRPC 계층은 구현이 쉽지 않다. 로그 라이브러리를 호출하고 에러를 처리하는 것이 전부이다. 다음 코드를 추가하자.

``` go
// ServeRequestsWithgRPC/internal/server/server.go

func (s *grpcServer) Produce(ctx context.Context,req *api_v1.ProduceRequest) (*api_v1.ProduceResponse,error) {

offset,err:=s.CommitLog.Append(req.Record)

if err != nil {

return nil, err

}

return &api_v1.ProduceResponse{Offset: offset},nil

}
```

서버의 메서드인 `func (s *grpcServer) Produce(ctx context.Context,req *api_v1.ProduceRequest) `와 `Consume`을 구현했다. 이 두 메서드는 서버의 로그에 생산(produce)하고 소비(consume)해달라는 클라이언트의 요청을 처리한다. 이제 스트리밍 API를 구현한다.


``` go
// ServeRequestsWithgRPC/internal/server/server.go

func (s *grpcServer) ProduceStream(

stream api_v1.Log_ProduceStreamServer,

) error {

for {

req, err := stream.Recv()

if err != nil {

return err

}

  

res, err := s.Produce(stream.Context(), req)

if err != nil {

return err

}

  

if err = stream.Send(res); err != nil {

return err

}

}

}

  

func (s *grpcServer) ConsumeStream(

req *api_v1.ConsumeRequest,

stream api_v1.Log_ConsumeStreamServer,

) error {

  

for {

select {

case <-stream.Context().Done():

return nil

default:

res,err:=s.Consume(stream.Context(),req)

switch err.(type){

case nil:

case api_v1.ErrOffsetOutOfRange:

continue

default:

return err

}

if err=stream.Send(res);err != nil {

return err

}

req.Offset++

}

}

}
```

`func (s *grpcServer) ProduceStream(stream api_v1.Log_ProduceStreamServer,)`메서드는 양방향 스트리밍 RPC이다. 클라이언트 서버의 로그로 데이터를 스트리밍할 수 있고, 서버는 각 요청의 성공 여부를 회신할 수 있다. `func (s *grpcServer) ConsumeStream(req *api_v1.ConsumeRequest,stream api_v1.Log_ConsumeStreamServer,)`메서드는 서버 측 스트리밍 RPC이다. 클라이언트가 로그의 어느 위치의 레코드를 읽고 싶은지를 밝히면, 서버는 그 위치로부터 이어지는 모든 레코드를 스트리밍한다. 나아가 로그 끝까지 스트리밍하고 나면, 누군가가 레코드를 추가할 때까지 기다렸다가(정확히는 요청과 에러를 반복하다가) 레코드가 들어오는 대로 클라이언트에 스트리밍 한다.


gRPC 서비스를 구성하는 코드를 짧고 간단하다. 네트워크 관련 코드와 로그 코드가 깔끔하게 분리된 덕분이지만, 한편으로는 가장 기본적인 에러 처리만 하기 때문이다. 로그 라이브러리가 리턴하는 에러를 그대로 클라이언트에 전달한다.

클라이언트가 메시지를 소비(consume)하려고 한 요청이 실패한다면, 개발자는 이유가 궁금할 것이다. 서버가 메시지를 찾지 못했을까? 서버가 예상하지 못한 실패일까? ==서버는 이러한 정보를 상태 코드(status code)로 전달한다. 또한 고객 역시 어떻게 애플리케이션이 실패했는지도 알아야 한다.== ==따라서 서버는 에러를 사람이 읽을 수 있는 형태로 전달하여 클라이언트가 사용자에게 보여줄 수 있게 한다.==


#### 4.5.1 gRPC의 에러 처리

gRPC는 에러 처리를 잘 지원한다. 앞서 구현한 코드에서는 고의 표준 라이브러리처럼 에러를 리턴한다. 코드는 서로 다른 컴퓨터를 사용하는 사용자 사이의 호출을 처리하지만, 마치 하나의 컴퓨터에서 작동하는 프로그램처럼 느껴진다. 네트워크 구현부를 추상화하는 gRPC 덕분이다. 기본적으로 에러는 단지 문자열로 표현된다. 하지만 상태 코드나 임의의 데이터 같은 정보를 추가할 수 있다.

<mark style="background: #ADCCFFA6;">go의 gRPC의 구현은 status 패키지 덕분에 에러에 상태 코드를 표현한 그 어떤 데이터도 추가할 수 있다.</mark> <mark style="background: #ADCCFFA6;">상태 코드를 포함한 에러를 생성하려면 status 패키지의 Error() 함수에 codes 패키지의 코드를 전달한다.</mark> 이 상태 코드는 각자의 에러 자료형과 매칭된다. 에러에 첨부한 상태 코드는 code 패키지에 정의된 코드여야 한다. gRPC가 지원하는 모든 프로그래밍 언어는 이 상태 코드가 같다. 예를 들어 특정 ID의 레코드를 찾을 수 없다면 NotFound 코드를 사용해야 한다.

```go
err:=status.Error(codes.NotFound, "id was not found")
return nil,err
```

==클라이언트 측에서는 **status** 패키지의 FromError() 함수를 이용하여 에러에서 코드를 추출한다.== 목표는 가능한 한 많은 에러에 상태 코드를 추가하는 것이다. 상태 코드로 에러의 원인을 파악하면 정상 처리할 수 있다. 하지만, 예측하지 못한 에러나 서버 내부에서 발생한 에러일 경우 상태 코드가 없을 수 있다. 다음과 같이 FromError()을 이용해 gRPC에러에서 상태 코드를 추출한다.

``` go
st,ok:=status.FromError(err)
if !ok{
// 상태 코드를 가진 에러가 아니다.
}
// st.Message()와 st.Code()를 사용하여 메시지와 상태 코드를 알 수 있다.
```

에러를 디버깅하거나 로그나 트레이스를 통한 추가 정보가 필요할 때처럼 상태 코드 그 이상이 필요하다면, <mark style="background: #ADCCFFA6;">status 패키지의 WithDetails() 함수를 사용해보자. 에러에 원하는 protobuf 메시지를 첨부할 수 있다.</mark>

`errdetails` 패키지는 서비스를 만들 때 유용한 protobuf를 제공한다. bad request 처리, 디버그 정보, 현지화(localized) 메시지 등이 있다.

`errdetails` 패키지의 **LocalizedMessage()** 함수를 써 보자. 앞선 예시 코드를 바꾸어 사용자에게 회신하기에 좀 더 안전하게 만들자. 다음 코드에서는 먼저 not-found 상태 코드를 생성하고, 현지화된 메시지와 로케일(locale) 정보를 생성한다. 그런 다음 상태의 세부 정보를 추가하고, 마지막으로 Go 에러로 변환하여 상태를 리턴한다.

``` go
st:=status.New(codes.NotFound,"id was not found")

d:=&errdetails.LocalizedMessage{

Locale: "en-US",

Message: fmt.Sprintf("We couldn't find a user with the email address: %s",id),

}

var err error
st,err=st.WithDetails(d)
if err!= nil{
// 여기서 에러가 발생한다면 에러는 항상 발생한다.
// 따라서 단순히 에러를 전달하기보다는
// panic을 발생시켜서 문제의 원인을 파악해야 한다.
panic(fmt.Sprintf("Unexpected error attaching metadata: %v",err))
}

return st.Err()
```

==클라이언트 측에서 세부 정보를 추출하려면 에러를 status로 변환한 다음 Details() 메서드로 추출한다.== 그리고 세부 정보의 자료형을 서버에서 설정한 자료형에 맞게 변환한다. 코드는 다음과 같다.

```go
st:=status.Convert(err)
for _, detail := range st.Details(){
switch t:=detail.(type){
case *errdetails.LocalizedMessage:
}
}
```

서비스로 다시 돌아가서 `ErrOffsetOutOfRange`라는 커스텀 에러를 추가해보자. 클라이언트가 로그 범위를 벗어난 오프셋의 레코드를 소비하려 할 때 회신하는 에러이다. `api/v1` 디렉터리에 error.go 파일을 만들고 다음 코드를 추가하자.


```go
// ServeRequestsWithgRPC/api/v1/error.go

package log_v1

  

import (

"fmt"

  

"google.golang.org/genproto/googleapis/rpc/errdetails"

"google.golang.org/grpc/status"

)

  

type ErrOffsetOutOfRange struct {

Offset uint64

}

  

func (e ErrOffsetOutOfRange) GRPStatus() *status.Status {

st := status.New(

404,

fmt.Sprintf("offset out of range: %d", e.Offset),

)

  

msg := fmt.Sprintf(

"The requested offset is outside the log's range: %d",

e.Offset,

)

  

d := &errdetails.LocalizedMessage{

Locale: "en-US",

Message: msg,

}

  

std, err := st.WithDetails(d)

if err != nil {

return st

}

  

return std

  

}

  

func (e ErrOffsetOutOfRange) Error() string {

return e.GRPStatus().Err().Error()

}
```

그다음에는 로그가 이 에러를 사용하도록 하자. `internal/log/log.go` 파일의 `Read(offset uint64)` 메서드에서 다음 부분을 찾아보자.


```go
if s==nil || s.nextOffset <= Off{
return nil, fmt.Errorf("offset out of range: %d",off)
}
```

이 코드를 다음과 같이 변경하자.

``` go

// ServeRequestWithgRPC/internal/log/log.go

if s == nil || s.nextOffset <= off{
return nil, api.ErrOffsetOutOfRange{Offset:off}
}
```

마지막으로 `internal/log/log_test.go` 파일의 `testOutOfRange(t *testing.T,log *log)` 테스트를 수정하자.

``` go

// ServerRequestsWithgRPC/internal/log/log_test.go
func testOutofRangeErr(t *testing.T, log *Log) {

read, err := log.Read(1)

require.Nil(t, read)

apiErr := err.(log_v1.ErrOffsetOutOfRange)

require.Equal(t, uint64(1), apiErr.Offset)

}
```

==클라이언트가 로그 범위 바깥의 오프셋을 소비하려 하면, 로그는 현지화 메시지, 상태 코드, 에러 메시지 등의 유용한 정보를 담은 커스텀 에러를 회신할 것이다.== 커스텀 에러가 구조체 자료형이기 때문에 `Read(offset uint64)` 메서드의 리턴 값을 자료형 변환하여 분석할 수 있다. `func (s *grpcServer) ConsumeStream(req *api_v1.ConsumeRequest,stream api_v1.Log_ConsumeStreamServer,)`메서드에서 이 방법을 사용했다. 서버가 로그의 마지막을 읽었는지 확인하고, 누군가가 또 다른 레코드를 생산할 때까지(요청과 에러를 무한 반복하며) 기다리는 부분이다.

```go
// ServeRequestsWithgRPC/internal/server/server.go

func (s *grpcServer) ConsumeStream(

req *api_v1.ConsumeRequest,

stream api_v1.Log_ConsumeStreamServer,

) error {

  

for {

select {

case <-stream.Context().Done():

return nil

  

default:

res, err := s.Consume(stream.Context(), req)

switch err.(type) {

case nil:

case api_v1.ErrOffsetOutOfRange:

continue

default:

return err

}

if err = stream.Send(res); err != nil {

return err

}

req.Offset++

}

}

  

}
```

지금까지 서비스의 에러 처리를 개선했다. 상태 코드를 추가했고, 사람이 읽을 수 있으면서 현지화된 에러 메시지를 추가했다. 사용자는 에러가 발생한 원인을 좀 더 자세히 알 수 있다. 다음으로는 여러 로그 구현을 받을 수 있고 서비스의 테스트 코드를 짜기 쉽도록 서비스의 로그 필드를 정의해보자.


#### 4.5.2 인터페이스를 이용한 의존관계 역전

구현한 서버는 추상화한 로그에 의존한다. <mark style="background: #ADCCFFA6;">예를 들어 사용자의 데이터를 잘 보관해야 하는 프로덕션(production) 환경에서 서비스는 로그 라이브러리에 의존성을 가진다.</mark> 하지만 테스트 환경에서는 데이터를 저장해둘 필요 없이 인메모리(in-memory)로그를 사용해도 된다. 인메모리를 사용하면 테스트 속도도 빠르다.


``` go

// ServeRequestsWithgRPC/internal/server/server.go
type CommitLog interface {

Append(*log_v1.Record) (uint64, error)

Read(uint64) (*log_v1.Record, error)

}
```

이 간단한 코드만으로 서비스는 CommitLog 인터페이스를 만족하는 어떠한 로그 구현도 사용할 수 있다.

다음으로 외부로 내보내는(exported) API를 구현해서 사용자가 새로운 서비스를 인스턴스화 할 수 있도록 하자.


---
# 4.6 서버 등록하기

지금까지의 서버 구현 과정에 아직 gRPC에 관한 부분은 없다. 서비스가 gRPC로 작동하려면 세 단계가 필요하며, 우리는 그중 두 단계만 해결하면 된다. <mark style="background: #BBFABBA6;">먼저 gRPC 서버를 만들고, 서비스를 등록하는 것이다.</mark> <mark style="background: #BBFABBA6;">마지막 단계는 서버에 연결하려는 요청을 받는 리스너(listener)를 추가하는 것인데, 이 단계는 사용자가 자신이 구현한 리스너를 전달하도록 하는 것이다.</mark> 사용자가 테스트할 용도로 사용할 수도 있다. 세 단계를 모두 마치면 gRPC 서버는 네트워크상에서 요청을 수신하여 처리하고, 서비스를 호출하고, 클라이언트에 결과를 회신할 것이다.

`server.go` 파일에 **NewGRPServer()** 함수를 추가하여 서비스를 인스턴스화하고, gRPC 서버를 생성하여, 서비스를 서버에 등록할 수 있게 한다. 사용자는 연결 요청을 수락하는 리스너만 추가하면 되는 gRPC 서버를 가진다.

```go

// ServeRequestsWithgRPC/internal/server/server.go
func NewGRPCServer(config Config) (*grpc.Server, error) {

gsrv := grpc.NewServer()

srv, err := newgrpcServer(&config)

if err != nil {

return nil, err

}

log_v1.RegisterLogServer(gsrv, srv)

return gsrv, nil

}
```

서비스 구현이 끝났다. 이제 서비스의 작동을 확인하는 테스트를 만들자.


---
# 4.7 gRPC 서버와 클라이언트 테스트하기

gRPC 서버를 모두 구현했다. 이제 기대한 대로 작동하는지 테스트해보자. 로그 라이브러리는 이미 테스트했으니, 여기서는 좀 더 높은 수준의 테스트로서 gRPC와 라이브러리의 연결과, gRPC클라이언트와 서버의 통신을 확인하는 테스트 코드를 짜보자.

grpc 디렉터리에 server_test.go 파일을 만들어 테스트 준비 작업을 추가한다.

``` go

package server

  

import (
"net"
"os"
"testing"


log_v1 "github.com/distributed_service_go/Part4-ch4-ServerRequestWithgRPC/api/v1"

"github.com/distributed_service_go/Part4-ch4-ServerRequestWithgRPC/internal/log"

"github.com/stretchr/testify/require"

"google.golang.org/grpc"

"google.golang.org/grpc/credentials/insecure"

)

  

func TestServer(t *testing.T) {

for scenario, fn := range map[string]func(
t *testing.T,
client log_v1.LogClient,
config *Config,
){
"produce/consume a message to/from the log succeeds": testProduceConsume,
"produce/consume stream succeeds": testProduceConsumeStream,
"consume past log boundary fails": testConsumePastBoundary,
} {
t.Run(scenario, func(t *testing.T) {
client, config, teardown := setupTest(t, nil)
defer teardown()

fn(t, client, config)
	})
}
}
```


`TestServer(t *testing.T)` 테스트 함수는 테스트 케이스들을 정의하고 각각의 케이스에 대해 서브 테스트를 실행한다. `setupTest(t *testing.T, fn func(*Config))` 테스트 함수를 추가하자.


``` go

// ServeRequestsWithgRPC/internal/server/server_test.go

  
func setupTest(t *testing.T, fn func(*Config)) (

client log_v1.LogClient,

cfg *Config,

teardown func(),

) {

  

t.Helper()

  

l, err := net.Listen("tcp", ":0")

  

require.NoError(t, err)

  

clientOption := []grpc.DialOption{grpc.WithTransportCredentials(insecure.NewCredentials())}

cc, err := grpc.NewClient(l.Addr().String(), clientOption...) // 책에 있는 내용과 약간 다름.

require.NoError(t, err)

  

dir, err := os.MkdirTemp("", "server-test")

require.NoError(t, err)

  

clog, err := log.NewLog(dir, log.Config{})

require.NoError(t, err)

  

cfg = &Config{

CommitLog: clog,

}

  

if fn != nil {

fn(cfg)

}

server, err := NewGRPCServer(cfg)

require.NoError(t, err)

  

go func() {

server.Serve(l)

}()

client = log_v1.NewLogClient(cc)

return client, cfg, func() {

server.Stop()

cc.Close()

l.Close()

clog.Remove()

}

}
```
`func setupTest(t *testing.T, fn func(*Config))` 함수는 각각의 테스트 케이스를 위한 준비를 해주는 도우미 함수이다. 테스트는 서버를 실행할 컴퓨터의 로컬 네트워크 주소를 가진 리스너부터 만든다. 0포트로 설정하면 자동으로 사용하지 않는 포트를 할당한다. 다음으로, 리스너에 보안을 고려하지 않은 연결을 수행한다. 클라이언트는 이 연결을 사용할 것이다.


그 다음으로 서버를 생성하고 고루틴(goroutine)에서 요청을 처리한다. Serve() 메서드가 블로킹 호출(blocking call)이므로 고루틴 안에서 호출하지 않으면 이어지는 테스트가 실행되지 않는다.

이제 테스트 케이스를 작성하자.

``` go

// ServeRequestsWithgRPC/internal/server/server_test.go

func testProduceConsume(t *testing.T, client log_v1.LogClient, config *Config) {

  

ctx := context.Background()

want := &log_v1.Record{
Value: []byte("hello world"),
}

  

produce, err := client.Produce(
ctx,

&log_v1.ProduceRequest{
Record: want,
},

)
require.NoError(t, err)

  

consume, err := client.Consume(ctx, &log_v1.ConsumeRequest{
Offset: produce.Offset,
})

require.NoError(t, err)
require.Equal(t, want.Value, consume.Record.Value)
require.Equal(t, want.Offset, consume.Record.Offset)


}

```

`testProduceConsume(t *testing.T, client log_v1.LogClient, config *Config)` 테스트는 클라이언트와 서버를 이용해 생산과 소비가 이루어지는지, 로그에 레코드를 추가하고 다시 소비하는지 확인한다. 그리고 보냈던 레코드가 받은 레코드가 같은지 확인한다.


다음 케이스를 추가하자.

``` go
// ServeRequestsWithgRPC/internal/server/server_test.go

func testConsumePastBoundary(
t *testing.T,
client log_v1.LogClient,
config *Config,
) {

ctx := context.Background()

  

produce, err := client.Produce(ctx, &log_v1.ProduceRequest{
Record: &log_v1.Record{
Value: []byte("hello world"),
},
})

require.NoError(t, err)

  

consume, err := client.Consume(
ctx, &log_v1.ConsumeRequest{
Offset: produce.Offset + 1,
})

  

if consume != nil {
t.Fatal("consume not nil")
}

  

got := status.Code(err)
  

want := status.Code(log_v1.ErrOffsetOutOfRange{}.GRPStatus().Err())

  

if got != want {
t.Fatalf("got err: %v, want: %v", got, want)
}


}

```

`func testConsumePastBoundary(t *testing.T,client log_v1.LogClient,config *Config,)` 테스트는 클라이언트가 로그의 범위를 벗어난 소비를 시도할 때, 서버가 `log_v1.ErrOffsetOutOfRange()`에러를 회신하는지 확인한다.

(여기서는 log_v1 패키지가 api 패키지이다.)


다음은 마지막 테스트 케이스이다.


``` go

// ServeRequestsWithgRPC/internal/server/server_test.go


func testProduceConsumeStream(

t *testing.T,

client log_v1.LogClient,

config *Config,

) {

ctx := context.Background()

  

records := []*log_v1.Record{{

Value: []byte("first message"),

Offset: 0,

}, {

Value: []byte("second message"),

Offset: 1,

}}

  

{

stream, err := client.ProduceStream(ctx)

require.NoError(t, err)

  

for offset, record := range records {

err = stream.Send(&log_v1.ProduceRequest{Record: record})

  

require.NoError(t, err)

res, err := stream.Recv()

require.NoError(t, err)

if res.Offset != uint64(offset) {

t.Fatalf(

"got offset: %d, want: %d",

res.Offset,

offset,

)

}

  

}

}

  

{

stream, err := client.ConsumeStream(

ctx,

&log_v1.ConsumeRequest{Offset: 0},

)

  

require.NoError(t, err)

  

for i, record := range records {

res, err := stream.Recv()

require.NoError(t, err)

require.Equal(t, res.Record, &log_v1.Record{

Value: record.Value,

Offset: uint64(i),

})

}

}

  

}

```

`func testProduceConsumeStream(t *testing.T,client log_v1.LogClient,config *Config,)`테스트는 `testProduceConsume()` 테스트와 비슷한, 스트리밍에 대한 테스트이다. 스트림을 통한 생산과 소비를 확인한다. `$make test` 명령으로 테스트해보면 TestServer 테스트를 통과하는 것을 볼 수 있다.

드디어 첫 번째 gRPC 서비스를 구현하고 테스트까지 마쳤다.


---
# 4.8 마치며

protobuf에서 gRPC 서비스를 정의하고, gRPC protobuf를 컴파일하여 코드를 만든 다음, gRPC 서버 만드는 법을 배웠다. 그리고 이 모든 구현이 클라이언트와 서버를 통해 작동하는 것을 테스트하는 법을 배운다. 이제 gRPC 서버와 클라이언트를 만들고, 네트워크에서 로그 라이브러리를 사용할 수 있다.

이번에는 서비스의 보안을 강화해보자. 클라이언트와 서버가 주고받는 데이터를 SSL/TLS로 암호화하고, 요청을 인증하여 누가 요청을 보내고 권한이 있는지 확인하자.


![[distributed_go_chap4.drawio.png]]