
어느 날 아침, 바지를 입는데 허리 벨트의 마지막 구멍도 맞지 않는다고 상상해보자. 얼른 체중계에 올라갔더니 밤사이 체중이 엄청나게 늘었다. 전격적으로 다이어트와 건강 요법을 해 본다. 몇 주 뒤에 다시 체중을 재어보았더니 체중이 더 늘어버렸다. 도대체 어떻게 된 것일까?

몸에서 무슨 일이 일어나고 있는지 알아내야 한다. 만약 몸을 관측(observe)할 수 있다면, 몸의 매트릭을 얻어서 대시보드에 호르몬 레벨을 그래프로 나타낼 수 있을 것이다. 별다른 변화가 없는데 호르몬의 갑작스러운 불균형이 보인다면 호르몬 불균형이 주요 원인일 가능성이 높다. 하지만 무엇이 바뀌었는지 볼 수 없다면, 문제를 찾기 위해 여러 가지로 수정해보아야 하고 그에 따른 영향을 받게 된다.

우리가 시스템을 관측할 수 있으면, 시스템의 상황을 확인하거나 예상치 못한 문제에 관에 디버깅할 수 있다. 여기서 '예상치 못한'이라는 표현이 중요하다. 시스템을 관측하면 처음 발생하는 알 수 없는 문제도 해결할 수 있다. 6장에서는 내부의 작동과 상황을 파악할 수 있도록 서비스를 관측 가능하게(observable)하게 구현해보자.



---
# 6.1 세 종류의 원격 측정 데이터

관측 가능 여부는 시스템의 내부 작동과 상태를 외부 출력에서 얼마나 알 수 있는지를 측정하는 척도이다. 메트릭, 구조화한 로그, 트레이스가 관측을 위한 대표적인 외부 출력이다. 세 종류의 원격 측정(telemetry)은 각각의 용도가 있으며 같은 이벤트에서 비롯한다. 예를 들어 웹 서비스가 요청을 처리할 때 '요청 처리됨' 메트릭을 증가시키고 로그를 발생시키며 트레이스 한다.


### 6.1.1 메트릭


**메트릭**(metric)은 시간의 경과에 따른 데이터 수치를 측정한다. <mark style="background: #ADCCFFA6;">얼마나 많은 수의 요청이 실패했는지, 요청마다 처리 시간은 얼마나 소모되었는지와 같은 수치 데이터</mark>이다. 이러한 메트릭은 **서비스 수준 지표**(service-level indicator,SLI), **목표** (service-level objective, SLO), **계약**(service-level agreement,SLA)을 정의할 때 도움이 된다. 메트릭을 활용하면 시스템의 건강 상태를 보고하고, 내부 경고를 트리거하고, 대시보드의 그래프로 표시하여 한 눈에 시스템 상태를 파악할 수 있다.


메트릭은 수치 데이터이므로 저장 공간과 쿼리 시간을 줄이려고 해상도를 조금씩 줄일 수 있다. 예를 들어 어느 출판사에 각 도서에 관한 판매량 메트릭을 가지고 있다고 가정하자. 고객에게 책을 배송하려면 고객의 주문 정보를 알아야 하지만, 배송 후 반송할 수 있는 날짜가 지난 뒤에는 더 신경 쓰지 않아도 된다. 사업상 회계나 분석을 한다면 이러한 세세한 메트릭까지는 필요가 없다. 세금 계산을 위한 분기별 수익, 연간 성장률, 그리고 사업을 확장하기 위해 더 많은 편집자나 작가를 고용할 수 있는지가 궁금할 것이다.

메트릭은 다음 세 종류로 구분한다.

- **카운터**(counter): <mark style="background: #ADCCFFA6;">실패한 요청의 개수와 같은 특정 이벤트가 발생한 횟수나, 시스템이 처리한 전체 바이트와 같은 숫자를 추적한다.</mark> 카운터는 비율을 얻을 대도 사용한다. 예를 들어 일정 시간 단위에 발생한 이벤트 수와 같은 값이다. 수신한 요청 수는 서비스가 얼마나 인기 있는지 자랑하는 것 외에는 의외로 쓸모가 없다. 초당 혹은 본당 얼마나 많은 요청을 처리했는지 등의 지표가 중요하다. 지표가 갑자기 크게 떨어진다면 시스템의 레이턴시를 확인해봐야 한다. 요청 실패율이 치솟는다면 문제를 찾아 수정할 것이다.
- **히스토그램**(histogram):<mark style="background: #ADCCFFA6;">데이터 분포를 보여준다.</mark> 주로 요청의 기간과 크기를 백분위로 측정한다. 
- **게이지**(gauge): <mark style="background: #ADCCFFA6;">어떠한 대상의 현재 값을 추적한다.</mark> 그리고 값을 완전히 바꿀 수도 있다. 호스트의 디스크 사용 비율이나 클라우드의 최대 허용 개수 대비 로드 밸런서의 개수와 같은 포화 유형(saturation -type) 메트릭에 유용하다.

측정할 수 있는 대상 중 실제로 어떠한 데이터를 측정해야 할까? 어떤 메트릭이 시스템에 가치 있는 신호를 제공할 것인가? 다음은 구글이 밝힌 네 가지 중요 신호이다.


- **레이턴시(지연)**(latency): **서비스가 요청을 처리하는 데 걸리는 시간**이다. 레이턴시가 치솟으면 시스템의 인스턴스를 좀 더 고사양으로 바꾸거나(스케일 업 scale up), 로드 밸런서에 인스턴스를 추가(스케일 아웃 scale out) 해야 한다.
- **트래픽**(traffic): **서비스 요청량**으로, <mark style="background: #ADCCFFA6;">일반적인 웹 서비스라면 보통 초당 요청 처리 개수를 말한다.</mark> <mark style="background: #ADCCFFA6;">온라인 비디오 게임 또는 비디오 스트리밍 서비스라면 보통 동시 접속 사용자 수이다.</mark> 이 메트릭은 서비스가 얼마나 인기가 많은지를 보여주기도 하지만, 현재 어떤 규모로 작업하고 있으며 언제쯤 확장하고 새로운 디자인을 적용해야 할지를 알려준다는 점에서 더 중요하다.
- **에러**: **서비스의 요청 처리 실패율**이다. 내부 서버 에러(internal server error)가 특히 중요하다.
- **포화도**(saturation): <mark style="background: #ADCCFFA6;">서비스의 용량을 측정</mark>한다. 예를 들어 서비스가 데이터를 디스크에 저장한다면 데이터 입력 속도를 기준으로 하드 디스크 용량이 곧 부족해질 것인지, 디스크가 아닌 인 메모리(in-memory) 저장소라면 사용 가능한 메모리에 비해 서비스가 사용하고 있는 메모리는 얼마인지를 알 수 있다.


<mark style="background: #ADCCFFA6;">대부분의 디버깅은 메트릭에서 출발한다.</mark> <mark style="background: #FFB86CA6;">경고가 발생하거나 대시보드에서 이상한 지점을 발견하는 것이다. 그 다음 로그를 들여다보고 트레이스에서 문제를 더 자세히 들여다본다.</mark> 이 과정을 살펴보자.


### 6.1.2 구조화 로그


<mark style="background: #ADCCFFA6;">**로그**(log)는 시스템에 발생한 이벤트를 기록한 것이다.</mark> <mark style="background: #FFB86CA6;">서비스에 관한 의미 있는 정보를 제공한다면 어떠한 이벤트라도 로그로 저장해야 한다.</mark> 로그는 무엇이 잘못되었고 누가 어떤 이유로 무슨 작업을 했으며 시간은 얼마나 걸렸는지를 알아서 **문제 해결**(troubleshooting), **감사**(audit), **프로파일링**(profile)에 도움이 되어야 한다. 예를 들어 gRPC 서비스의 로그는 RPC 요청 하나에 다음과 같은 로그를 남긴다.


``` json
{
"request_id":"f47ac10b-58cc-0372-8567-0e02b2c3d479",
"level":"info",
"ts":1600139560.3399575,
"caller":"zap/server_interceptors.go:67",
"msg":"finished streaming call with code OK",
"peer.address":"127.0.0.1:54304",
"grpc.start_time":"2020-09-14T22:12:40-50:00",
"system":"grpc",
"span.kind":"server",
"grpc.service":"log.v1.Log",
"grpc.method":"ConsumeStream",
"peer.address":"127.0.0.1:54304",
"grpc.code":"OK",
"grpc.time_ns":197740
}
```

로그를 보면 메서드를 호출한 시간, 호출한 IP 주소, 호출한 서비스와 메서드, 호출의 성공 여부, 처리 시간을 알 수 있다. 분산 시스템에서 request ID는 여러 서비스에서 처리되는 요청들을 모아 하나의 그림을 그려보는데 도움이 된다.

gRPC 로그는 JSON 형식의 **구조화 로그**(structured log)이다. 구조화 로그는 키와 값으로 된 쌍들이 일관된 스키마와 형식으로 인코딩되어 프로그램에서 읽기 쉽다. <mark style="background: #ADCCFFA6;">구조화 로그를 통해 로그를 수집하고, 전달하고, 저장하고, 질의하는 작업을 분리할 수 있다.</mark> <mark style="background: #FFB86CA6;">예를 들어 프로토콜 버퍼 형식으로 로그를 수집하고 전송한 뒤에 **파케이**(Parquet) 형식으로 다시 인코딩하여 열 지향 데이터베이스에 저장할 수 있다.</mark>

구조화 로그를 수집한다면 카프카 같은 이벤트 스트림 플랫폼을 추천한다. 로그를 원하는 대로 처리하거나 전송하기 좋다. 예를 들어 카프카를 빅쿼리(BigQuery) 같은 데이터베이스에 연결하여 로그 질의에 사용하고, **구글 클라우드 스토리지**(Google Cloud Storage, GCS)와 같은 저장소에 연결하여 기록용 복사본으로 저장한다.

로그가 적으면 문제를 디버깅하기 어렵고, 로그가 많으면 무엇이 중요한지 찾기 어렵다. <mark style="background: #ADCCFFA6;">그 사이의 적절한 균형을 찾아야 한다. 우선은 조금 과하다 싶게 로깅하고, 필요 없는 로그를 조금씩 줄여가는 것을 추천한다.</mark> 이렇게 하면 문제 해결이나 검사를 할 때 정보가 부족하지 않다.


### 6.1.3 트레이스

**트레이스**(trace)는 요청의 라이프사이클을 수집해서, 요청이 시스템 내부를 흘러가는 것을 추적한다. 사용자 인터페이스를 추적하는 데에는 예거(jaeger), 스택드라이버(Stackdriver), 라이트스텝(Lightstep) 같은 도구를 사용한다. 이 도구들은 요청이 시스템의 어디에서 얼마나 시간을 보냈는지 시각화해서 보여준다. 분산 시스템에서는 요청이 여러 서비스에서 수행되므로 특히 유용하다. 다음 스크린숏은 Jocko 요청을 트레이스한 것을 예거에서 처리한 예이다.







트레이스에 태그와 세부 정보를 추가하면 요청에 대해 좀 더 알 수 있다. 대표적으로, 각각의 트레이스에 사용자 ID를 태그하면 사용자에게 문제가 발생했을 때 해당 사용자의 요청을 빠르게 찾을 수 있다.

트레이스는 하나 이상의 **스팬**(span)으로 구성된다. 스팬들은 서로 부모/자식 또는 형제의 관계를 가지며 각각의 스팬은 요청 처리의 일부를 의미한다. 얼마나 잘게 나눌지는 여러분이 판단하겠지만 처음에는 넓게 트레이스할 것을 추천한다. 요청이 서비스들을 흘러가는 처음부터 끝까지 하나의 스팬으로 트레이스하는 것이다. 그다음 각각의 서비스, 더 나아가 중요한 메서드 호출로 더 세부화해 나눈다.

---
# 6.2 서비스를 관측 가능하게 만들기

메트릭과 구조화 로그, 트레이스를 추가하여 서비스를 관측 가능(observable)하게 만들어보자. 서비스를 실제로 배포하면 보통은 이들을 프로메테우스, 일래스틱서치, 예거와 같은 외부 서비스로 보내도록 설정한다. 하지만 여기서는 간단히 파일에 기록해서 데이터를 들여다보겠다.

오픈텔레메트리(OpenTelemetry)는 클라우드 네이티브 컴퓨팅 재단(Cloud Native Computing Foundation, CNCF)의 오픈소스 프로젝트로, 서비스에 메트릭과 분산 트레이스 기능을 넣어주는 견고하고 포터블한 API와 라이브러리들이다. 오픈텔레메트리는 오픈센선스(OpenCensus)와 오픈트레이싱(OpenTracing)을 합친 것이며 오픈 센서스에 대해 하위 호환성을 제공한다. Go gRPC에 들어간 오픈텔레메트리라는 트레이스만 지원하므로, 이 책에서는 메트릭과 트레이스를 모두 지원하는 오픈센서스를 사용한다. 아쉽게도 오픈텔레메트리와 오픈센서스 모두 로그는 지원하지 않는다. 한 특수이익집단이 오픈텔레메트리의 로깅 스펙을 계획 중이니 언젠가는 지원할 것이다. 그러니 이 책에서는 우버(Uber)의 Zap 로그 라이브러리를 쓰도록 하자.


대부분의 고 네트워킹 API는 미들웨어를 지원하여 요청 처리를 원하는 로직으로 감쌀 수 있다. 모든 요청을 메트릭, 로그, 트레이스로 감쌀 수 있다는 뜻이다. 오픈센서스와 Zap의 인터셉터를 사용하면 된다.


프로젝트 디렉터리 안에서 다음 명령을 실행해서 오픈센서스와 Zap 패키지를 사용하자.
```make
$ go get go.uber.org/zap@latest
$ go get go.opencensus.io/zap@latest

```

`internal/server/server.go` 파일을 열고 import에 다음과 같이 코드를 추가하자.


[Part6-ObserveYourService/internal/serve/server.go]

``` go
import (

"context"

  

api_v1 "github.com/distributed_service_go/Part6-ObserveYourService/api/v1"
grpc_middleware "github.com/grpc-ecosystem/go-grpc-middleware"
grpc_auth "github.com/grpc-ecosystem/go-grpc-middleware/auth"
grpc_zap "github.com/grpc-ecosystem/go-grpc-middleware/logging/zap"
grpc_ctxtags "github.com/grpc-ecosystem/go-grpc-middleware/tags"

"go.opencensus.io/plugin/ocgrpc"
"go.opencensus.io/stats/view"
"go.opencensus.io/trace"
"go.uber.org/zap"
"go.uber.org/zap/zapcore"

"time"

"google.golang.org/grpc"
"google.golang.org/grpc/codes"
"google.golang.org/grpc/credentials"
"google.golang.org/grpc/peer"
"google.golang.org/grpc/status"

)
```

이번에는 NewGRPCServer() 함수에 Zap 설정을 추가하자.

[ObserveYourServices/internal/server/server.go]

```go
logger := zap.L().Named("server")

zapOpts := []grpc_zap.Option{

grpc_zap.WithDurationField(func(duration time.Duration) zapcore.Field {

return zap.Int64(

"grpc.time_ns",

duration.Nanoseconds(),

)

}),

}
```

로거명을 "server"라고 명시하여 서버 로그를 서비스의 다른 로그와 구분하고자 했다. 그리고 "grpc.time_ns" 필드를 구조화 로그에 추가해서 각 요청의 초리시간을 나노초로 기록하게 했다.

이어서 오픈센서스가 메트릭과 트레이스를 어떻게 수집할지 설정하는 코드를 추가하자.


[ObserveYourServices/internal/server/server.go]

```go
trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})

if err := view.Register(ocgrpc.DefaultServerViews...); err != nil {

return nil, err

}
```

아직 서비스를 개발 중이므로 모든 요청을 트레이스하기 위해 항상 샘플링하도록 설정했다.



프로덕션에서는 이렇게까지 하지 않는다. 성능에 영향을 주고, 너무 많은 데이터를 요구하며, 기밀 데이터를 트레이스할 수 있기 때문이다. 트레이스를 지나치게 많이 하는 게 문제라면, 확률 샘플러를 사용해서 일정 비율의 요청만 샘플링할 수 있다. 다만 이렇게 하면 중요한 요청에 대한 트레이스를 놓칠 수가 있다. 이러한 트레이드오프를 해결하기 위해, 중요한 요청의 항상 트레이스하고 나머지 요청은 일정 비율로 샘플링하는 샘플러를 구현하면 다음과 같다.

```go
trace.ApplyConfig(trace.Config{

DefaultSampler: func(sp trace.SamplingParameters) trace.SamplingDecision {

if strings.Contains(sp.Name,"Produce"){

return trace.SamplingDecision{Sample: true}

}

return halfSampler(p)

},

})
```

뷰(view)는 오픈센서스가 어떤 통계를 수집할지 명시한다. 디폴트 서버 뷰는 다음과 같은 통계를 추적한다.

- RPC당 받은 바이트
- RPC당 보낸 바이트
- 지연 시간
- 처리 완료된 RPC 개수

grpcOpts에 다음과 같이 강조한 부분의 코드를 추가해서 수정하자.


[ObserveYourServices/internal/server/server.go]

```go
grpcOpts = append(grpcOpts, grpc.StreamInterceptor(

grpc_middleware.ChainStreamServer(
grpc_ctxtags.StreamServerInterceptor(),
grpc_zap.StreamServerInterceptor(logger, zapOpts...),
grpc_auth.StreamServerInterceptor(authenticate),
)),

grpc.UnaryInterceptor(grpc_middleware.ChainUnaryServer(
grpc_ctxtags.UnaryServerInterceptor(),
grpc_zap.UnaryServerInterceptor(logger, zapOpts...),
grpc_auth.UnaryServerInterceptor(authenticate),
)),

grpc.StatsHandler(&ocgrpc.ServerHandler{}),

)
```

gRPC에 Zap 인터셉터들을 적용했다. gRPC 호출을 로깅하고, 오픈센서스를 서버의 통계 핸들러로 붙여서 서버의 요청 처리 통계를 기록하게 했다.

테스트 설정 부분이 남았다. 메트릭과 트레이스 로그 파일을 설정하면 된다. [internal/server/server_test.go] 파일에 다음 임포트를 추가하자.



[ObserverYourServices/internal/server/server_test.go]

```go
func TestMain(m *testing.M) {

flag.Parse()

if *debug {

logger, err := zap.NewDevelopment()

if err != nil {

panic(err)

}

zap.ReplaceGlobals(logger)

}

os.Exit(m.Run())

}
```


`TestMain(m *testing.M)`을 구현하려면, go는 테스트를 직접 실행하지 않고 TestMain() 함수를 호출한다. 따라서 테스트 파일 내의 모든 테스트에 적용할 설정을 여기에 두면 좋다. 플래그 파싱은 init()가 아닌 `TestMain()`에서 해야 하는데, 그렇지 않으면 플래그를 정의할 수 없게 되어 코드 에러가 나서 종료된다.

`setupTest()` 함수에 다음 코드를 추가하자.


[ObserveYourServices/internal/server/server_test.go]

```go
var telemetryExporter *exporter.LogExporter

  

if *debug {

metricsLogFile,err:=os.CreateTemp("","metrics-*.log")
require.NoError(t,err)
t.Logf("metrics log file: %s",metricsLogFile.Name())

  

traceLogFile,err:=os.CreateTemp("","trace-*.log")
require.NoError(t,err)
t.Logf("traces log file: %s",traceLogFile.Name())
telemetryExporter,err=exporter.NewLogExporter(
exporter.Options{
MetricsLogFile: metricsLogFile.Name(),
TracesLogFile: traceLogFile.Name(),
ReportingInterval: time.Second,
})

require.NoError(t,err)
err=telemetryExporter.Start()
require.NoError(t,err)

}
```

이 코드는 설정을 마친 다음에 텔레메트리 출력기(telemetry exporter)를 시작하여 두 파일에 쓸 수 있게 한다. 각각의 테스트에 별도의 트레이스 파일과 메트릭 파일이 생기므로 테스트별 요청을 볼 수 있다.


`setupTest()`의 맨 아래에 `teardown()` 함수에 다음과 같이 강조한 부분의 코드를 추가해서 수정하자.


[ObserveYourServices/internal/server/server_test.go]
```go
return rootClient, nobodyClient, cfg, func() {

server.Stop()

rootConn.Close()

nobodyConn.Close()

l.Close()

  

if telemetryExporter!=nil {

time.Sleep(1500*time.Millisecond)

telemetryExporter.Stop()

telemetryExporter.Close()

}

  

}
```

1.5초 sleep은 텔레메트리가 데이터를 디스크에 플러시할 시간을 주는 것이다. 그러고 나서 출력기를 멈춘 다음 닫아준다.

`internal/server` 디렉터리로 가서 다음 명령으로 서버를 테스트해보자.

``` bash
┌──(kali㉿kali)-[~/…/distributed_service_go/Part6-ObserveYourServices/internal/server]
└─$ go test -v -debug=true
=== RUN   TestServer
=== RUN   TestServer/produce/consume_a_message_to/from_the_log_succeeeds
    server_test.go:54: metrics log file: /tmp/metrics-4152982157.log
    server_test.go:54: traces log file: /tmp/trace-1811475188.log
```

테스트 출력에서 메트릭과 트레이스 파일 로그를 확인하고, 메트릭과 트레이스 데이터 출력을 확인하자.

``` bash
    server_test.go:54: metrics log file: /tmp/metrics-4152982157.log
    server_test.go:54: traces log file: /tmp/trace-1811475188.log
```

다음은 RPC 완료 통계의 예시이다. 서버가 두 개의 생산 호출을 잘 처리한 것을 알 수 있다.

```bash
(다만 여기서 지금 해본 결과 작동이 잘 되지 않는다. 그래서 일단 수기로 작성한다. 나중에 다시 확인해볼 예정이다.)
Metric: name: grpc.io/server/completed_rpcs, types: TypeCumulativeInt64, unit: ms
Labels:[
{grpc_server_method}={log.v1.Log/Produce true}
{grpc_server_status}={OK true}]
Value : value=2
```

그리고 다음은 생산 호출의 트레이스이다.

``` bash


#----------------------------------------------

TraceID:      0b7d158f6265a62c7bc5c9b69713af5b
SpanID:       52812376173ef648

Span:    log.v1.Log.Produce
Status:   [0]
Elapsed: 0s
SpanKind: Server

Attributes:
  - Client=false
  - FailFast=false

MessageEvents:
Received
UncompressedByteSize: 15
CompressedByteSize: 20

(나중에 다시 실험해볼 예정이지만, 여기서 Sent의 기록이 나와있지 않다. 그래서 다시 한번 후에 실험해 볼 예정이다.)

```

이제 서비스를 관측할 수 있다.

---
# 6.3 마치며

관측에 관해서 배웠고, 관측이 시스템을 좀 더 신뢰할 수 있게 해준다는 것을 배웠다. 트레이싱은 분산 시스템에서 특히 유용한데, 여러 서비스에 걸친 요청의 전체 흐름을 알려주기 때문이다. 서비스를 관측할 수 있게 하는 방법도 배웠다. 이제 서버가 클러스터링을 지원하는 법을 배울 차례이다. 클러스터링으로 서비스의 가용성과 확장성을 높여보자.

