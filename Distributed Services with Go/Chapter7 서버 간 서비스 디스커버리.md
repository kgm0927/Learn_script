
지금까지 보안을 독립형(stand-alone) gRPC 웹 서비스를 만들었다. 이제 독립형을 넘어서서 분산 서비스를 만들어보자. <mark style="background: #ADCCFFA6;">서비스 디스커버리를 사용하면 클러스터에 노드를 자동으로 추가하고 삭제할 수 있다.</mark>

서비스 디스커버리를 처음 접하는 독자라도 7장을 다 읽고 나면 익숙해질 것이다. 서비스 디스커버리는 분산 서비스에서 가장 멋진 기능으로, 머신이 다른 머신을 자동으로 찾아준다. 스카이넷(Skynet)이 자신을 인식하고 제어하게 된다면, 분명 서비스 디스커버리가 중요한 역할을 했을 것이다. 서비스 디스커버리의 주요 이점부터 하나씩 알아보자.


---
# 7.1 서비스 디스커버리를 사용하는 이유

서비스 디스커버리는 서비스에 연결하는 방법을 알아내는 과정이다. 서비스 디스커버리는 **레지스트리**(registry) 목록을 항상 최신 상태로 유지해야 한다. 레지스트리는 서비스와 서비스의 위치, 그리고 서비스 이상 유무를 가진다. 다운스트림 서비스가 레지스트리를 질의하여 업스트림 서비스의 위치를 알아내고 연결한다. 예를 들자면, 웹 서비스가 데이터베이스를 찾아서 연결하는 것이다. 이렇게 하면 업스트림 서비스와 스케일 업, 스케일 아웃, 또는 교체되더라도 다운스트림 서비스는 여전히 이들과 연결할 수 있다.

클라우드 서비스가 없던 시절에는 '서비스 디스커버리'를 일일이 관리하고 고정된 주소로 설정할 수 있었다. 특정 하드웨어에서 애플리케이션이 돌아가기에 가능한 일이었다. 하지만, 작동하는 노드가 수시로 바뀌는 모던 클라우드 애플리케이션에서는 서비스 디스커버리가 중요한 역할을 한다.

서비스 디스커버리 대신 로드 밸런서를 서비스 앞쪽에 두는 개발자도 있다. 이때 로그 밸런서는 고정 IP를 가진다. 하지만 서버 간 통신에서는 본인이 서버를 제어하고, 로드 밸런서가 클라이언트와 서버 사이의 신뢰 범위(trust boundary) 역할을 할 필요가 없으므로 로드 밸런서 대신 서비스 디스커버리를 사용하는 편이 낫다. 로드 밸런서는 비용이 추가되고, 레이턴시가 증가하며, 단일 장애점(single failure)이 생기고, 스케일 업/스케일 다운을 할 때 업데이트해야 하는 단점이 있다. 수십~수백 개의 마이크로서비스를 관리할 때 서비스 디스커버리를 사용하지 않는다면 수십~수백 개의 로드 밸런서와 DNS 레코드를 관리해야 한다. 우리가 만드는 분산 서비스에 로드 밸런서를 사용한다면 엔진엑스(Nginx) 같은 로드 밸런서나 클라우드 로드 밸런서인 AWS의 ELB, GCP의 로드 밸런서를 사용해야 한다. 결국에는 운영 부담이 커지고 인프라스트럭처 비용과 레이턴시가 늘어난다.


우리 시스템에는 해결해야 할 두 가지 서비스 디스커버리 문제가 있다.

- 클러스터 내의 서버들이 어떻게 서로를 찾아낼 것인가?
- 클라이언트들은 어떻게 서버를 찾아낼 것인가?

7장에서는 서버 디스커버리를 구현하고, 8장에서는 합의 부분을 구현한 다음, 9장에서 클라이언트 디스커버리를 알아본다.

서비스 디스커버리가 무엇을 할 수 있는지 알아보았다. 이제부터 우리 서비스에 구현해보자.



---
# 7.2 서비스 디스커버리 넣어주기

서비스와 통신해야 하는 애플리케이션이라면, 서비스 디스커버리를 위한 도구에는 다음과 같이 기능이 필요하다.


- 서비스들에 대한 레지스트리(서비스의 IP나 포트 정보를 담고 있음) 관리하기
- 레지스트리를 이용해 서비스가 다른 서비스를 찾을 수 있도록 돕기
- 서비스 인스턴스가 잘 작동하는지 수시로 체크하고, 문제가 있다면 제거하기
- 서비스가 오프라인이면 등록 취소하기


지금까지 분산 서비스를 만든 사람들은 서비스 디스커버리를 위해 (콘술(Consul), 주키퍼(ZooKeeper), etcd 등) 별도의 독립적인 서비스를 사용해왔다. 이러한 아키텍처는 두 개의 클러스터를 가지는데, 하나는 **서비스 자체를 위한 클러스터**이고 다른 하나는 **서비스 디스커버리를 위한 클러스터**이다. 이렇게 했을 대 장점은 직접 서비스 디스커버리를 만들 필요가 없다는 것이다. 하지만, 서비스 사용자 입장에서는 서비스 디스커버리 클러스터에 관해 배우고 실행하며 작동시켜야 한다는 장점이 있다. 다시 말해, 직접 만들어야 하는 부담을 덜어서 서비스 사용자에게 지우는 셈이다. 이러한 부담 때문에 사용자는 줄어들고, 사용자들 역시 주위에 서비스를 추천하지 않게 된다.

그런데도 독립적인 서비스 디스커버리를 사용하여 분산 서비스를 만드는 사람이나 그러한 서비스를 사용하는 이들이 많다. 분산 서비스를 만들 때 서비스 디스커버리를 서비스에 넣어줄 수 있는 라이브러리가 없고, 사용자는 선택의 여지가 없기 때문이다.

다행스럽게도 고 언어 개발자들은 **서프**(serf) 라이브러리를 쓸 수 있다. 분산 클러스터 멤버십, 실패 감지, 오케스트레이션(orchestration)을 지원하여 분산 서비스에 서비스 디스커버리를 쉽게 추가할 수 있다. 실제로 하시코프 사는 서프를 만들어 자사 제품인 콘술에 대해 사용한다.

>[!note] 언제 독립형 서비스 디스커버리 설루션을 사용해야 할까?
>때로는 서비스 디스커버리를 위한 독립형 서비스가 필요하다. 예를 들어 여러 플랫폼에 서비스 디스커버리를 포함해야 할 때가 있다. 엄청난 노력과 시간이 필요한 작업으로, 콘술과 같은 서비스를 사용하면 손쉽게 해결할 수 있다. 이때 서브로 시작해보기를 추천한다. 서비스를 개발하여 풀려는 문제를 해결하고, 어느 정도 안정적으로 작동하게 되면 서비스 디스커버리 서비스를 사용해야 할지 판단할 수 있을 것이다.



서프로 서비스를 만들면 다음과 같이 추가 이점이 있다.

- 서비스 개발 초기에는 별도 서비스를 준비하는 것보다 빠르게 만들 수 있다.
- 독립형 서비스에서 서프로 바꾸기보다는 서프에서 독립형 서비스로 바꾸는 편이 훨씬 간단하므로 나중에 선택을 바꾸기도 쉽다.
- 서비스를 훨씬 쉽고 유연하게 배포할 수 있다. 덕분에 서비스 접근이 편리해진다.

이러한 이유로 서프를 사용하여 서비스 디스커버리를 만들어보려 한다.

서프의 이점은 알아보았으니 서프가 어떻게 작동하는지 알아보자.

---

# 7.3 서프를 이용한 서비스 디스커버리

서프는 클러스터 멤버십을 관리하는데, 서비스 노드 간 통신을 위해 가볍고 효율적인 가십 프로토콜(gossip protocol)을 사용한다. 주키퍼나 콘술 같은 서비스 레지스트리 프로젝트와는 달리, 서프는 중앙 레지스트리 아키텍처가 아니다. 대신 각각의 서비스 인스턴스가 서프 노드로서 작동한다. 노드들은 마치 세기말 좀비들처럼 메시지를 교환하는데, 감염된 좀비가 다른 이들을 전염시키는 것과 같다. 서프를 사용하면 좀비 바이러스가 아닌 노드에 대한 정보가 클러스터에 퍼진다. 서프를 통해 클러스터 내의 변동사항을 들으면 그에 맞게 처리하는 것이다.

서프를 이용해 서비스 디스커버리를 구현하려면 다음과 같은 작업이 필요하다.

1. 각 서버에 서프 노드를 생성한다.
2. 각 서프 노드에 다른 노드들의 요청을 듣고 연결을 받아들일 주소를 설정한다.
3. 각 서프 노드에 다른 노드들의 주소를 설정하고 그들의 클러스터에 조인한다.
4. 서프의 클러스터 디스커버리 이벤트를 처리한다. 노드가 클러스터에 조인하거나 실패하는 이벤트가 있다.

이제 코딩해보자.

서프는 가벼운 도구이기에 다양한 유스케이스에 사용할 수 있다. 하지만 특정한 문제를 해결해야 한다면 서프의 API는 지나치게 상세할 수 있다. 우리 디스커버리 계층에서는 서버가 클러스터에 조인하거나 떠날 때 해당 서버의 ID와 주소만 알려는 것이다. 이를 위해 서버에서 사용할 최소한의 API만 사용하는 discovery 패키지를 만들어보자.

우선 서프 패키지를 가져오자.

``` bash
┌──(kali㉿kali)-[~/…/src/github.com/distributed_service_go/Part7-ServerSideServiceDiscovery]
└─$ go get github.com/hashicorp/serf@latest
go: downloading github.com/hashicorp/serf v0.10.2
go: added github.com/hashicorp/serf v0.10.2
```

그리고 `internal/discovery` 디렉터리를 만들고 `membership.go` 파일을 생성해 다음 코드를 넣어주자.


[Part7-ServerSideServiceDiscovery/internal/discovery/membership.go]

```go
package discovery

  

import (

"github.com/hashicorp/serf/serf"

"go.uber.org/zap"

)

  

type Membership struct {

Config

handler handler

serf *serf.Serf

events chan serf.Event

logger *zap.Logger

}

  

func New(handler Handler, config Config) (*Membership, error) {

c := &Membership{

Config: config,

handler: handler,

logger: zap.L().Named("membership"),

}

  

if err := c.setupSerf(); err != nil {

return nil, err

}

  

return c, nil

}
```

Membership 구조체로 서프를 감쌌다. 서비스에 디스커버리와 클러스터 멤버십을 제공한다. 사용자는 `New()` 함수를 호출해서 설정과 핸들러를 반영한 `Membership`을 생성한다.


이어서 다음 코드를 추가하여 설정 자료형을 정의하고 서프를 설정하자.

[ServerSideSeriveDiscovery/internal/discovery/membership.go]
``` go


type Membership struct {
Config
handler handler
serf *serf.Serf
events chan serf.Event
logger *zap.Logger

}


  

func (m *Membership) setupSerf() (err error) {

addr,err:=net.ResolveTCPAddr("tcp",m.BindAddr)

if err != nil {

return err

}

config:=serf.DefaultConfig()

config.Init()

config.MemberlistConfig.BindAddr=addr.IP.String()

config.MemberlistConfig.BindPort=addr.Port

m.events=make(chan serf.Event)

config.Tags=m.Tags

config.NodeName=m.Config.NodeName

m.serf,err=serf.Create(config)

if err != nil {

return err

}

  

go m.eventHandler()

if m.StartJoinAddrs!=nil{

_,err=m.serf.Join(m.StartJoinAddrs,true)

if err != nil {

return err

}

}

return nil

}
```

서프는 다양한 매개변수를 설정할 수 있는데, 가장 많이 쓰는 매개변수 다섯 개의 다음과 같다.

- `NodeName`: 노드명은 서프 클러스터 안에서 노드의 고유한 ID 역할을 한다. 따로 설정하지 않으면 서프는 호스트명(hostname)을 사용한다.
- `BindAddr`와 `BindPort`: 가십을 듣기 위한 주소와 포트이다.
- `Tags`: 서프는 클러스터 내의 노드들에 태그 공유하는데, 클러스터에 이 노드를 어떻게 다룰지 간단히 알려준다. 예를 들어 콘술은 각 노드의 RPC 주소와 서프 태그를 공유하는데, 노드들이 서로 RPC를 요청할 수 있다. 콘술은 노드가 투표자인지 여부 또한 공유하는데, 이 정보로 래프트 클러스터에서 노드의 역할을 바꾼다. 이 부분은 다음 장에서 래프트로 클러스터의 합의에 관해 다룰 때 좀 더 설명한다. 여기 코드에서는, 콘술처럼 각 노드에서 사용자가 RPC 주소와 서프 태그를 공유하여 노드들이 어떤 주소로 RPC를 보낼지 알게 한다.
- `EventCh`: 이벤트 채널은 노드가 클러스터에 조인할 때와 떠날 때의 서프 이벤트를 어떻게 수신할지를 설정한다. 특정 시점의 멤버들 스냅숏이 필요하다면 `Members()` 메서드를 호출하자.
- `StartJoinAddrs`: 새로운 노드를 만들어 클러스터에 추가한다면, 새 노드는 이미 클러스터에 있는 노드 중 하나 이상을 가리켜야 한다. 새 노드가 기존 노드에 연결되면 새 노드는 클러스터의 나머지 노드를 알게 되고, 기존 노드들 역시 새 노드를 알게 된다. `StartJoinAddrs` 필드는 새 노드가 기존의 클러스터에 어떻게 조인할지를 설정한다. 클러스터에 있는 노드이 주소를 설정하기만 하면 서프의 가십 프로토콜이 클러스터 조인에 필요한 나머지 작업을 처리한다. 프로덕션 환경에서는 최소한 세 개의 주소를 넣어서 네트워크가 몇몇 노드에 장애가 발생해도 문제가 없도록 하자.


`setupSerf()` 메서드는 서프 인스턴스를 설정대로 생성하며, `eventsHandler()` 고루틴을 시작해서 서프 이벤트를 처리하게 한다.

다음의 같이 `Handler` 인터페이스를 정의하자.


[ServerSideServiceDiscovery/internal/discovery/membership.go]
```go
type Handler interface {

Join(name,addr string) error

Leave(name string) error

}
```

Handler 인테이스는 서비스 내의 컴포넌트를 의미하며, 어떤 서버가 클러스터에 조인하거나 떠나는 것을 알 수 있어야 한다. 이번 장에서 클러스터에 조인하는 서버의 데이터를 복제하는 컴포넌트를 만들고, 다음 장에서는 합의하는 부분을 만드는데, 래프트는 클라이언트에 서버가 조인하는 것을 알아차리고 조율해야 한다.

`eventHandler()` 메서드를 정의하는 다음 코드를 추가하자.


[ServerSideServiceDiscovery/internal/discovery/membership.go]

``` go
  

func (m *Membership) eventHandler() {

for e := range m.events {

switch e.EventType() {

case serf.EventMemberJoin:

for _, member := range e.(serf.MemberEvent).Members {

if m.isLocal(member) {

continue

}

m.handleJoin(member)

}

case serf.EventMemberLeave, serf.EventMemberFailed:

for _, member := range e.(serf.MemberEvent).Members {

if m.isLocal(member) {

return

}

m.handleLeave(member)

}

}

}

}

  

func (m *Membership) handleJoin(member serf.Member) { // 입장

if err := m.handler.Join(

member.Name,

member.Tags["rpc_addr"],

); err != nil {

m.logError(err, "failed to join", member)

}

}

  

func (m *Membership) handleLeave(member serf.Member) { // 탈퇴

if err := m.handler.Leave(member.Name); err != nil {

m.logError(err, "failed to leave", member)

}

}

```

<mark style="background: #ADCCFFA6;">`eventHandler()` 메서드는 서프가 보낸 이벤트를 읽고, 자료형에 따라 적절한 이벤트 채널로 보내는 루프를 실행한다.</mark> <mark style="background: #ADCCFFA6;">노드가 클러스터에 조인하거나 떠나면, 서프는 해당 노드까지 포함한 모든 노드에 이벤트를 보낸다.</mark> 따라서 이벤트가 로컬 서버 자신이 발생시킨 것이라면 처리하지 않도록 한다. 예를 들어 서버가 그 자신에게 시도하거나 복제하지 않도록 하는 것이다.

서프는 여러 멤버의 업데이트를 하나의 이벤트로 보낼 수도 있다. 예를 들어 열 개의 노드가 거의 동시에 조인한다면, 서프는 열 개의 멤버가 조인했다는 하나의 이벤트를 보낼 것이다. 이벤트의 멤버들을 순회하는 이유이다.

`Membership`의 나머지 메서드들을 추가하자.


[ServerSideServiceDiscovery/internal/discovery/membership.go]
```go
  

func (m *Membership) isLocal(member serf.Member)bool {

return m.serf.LocalMember().Name==member.Name

}

  

func (m*Membership) Members() []serf.Member {

return m.serf.Members()

}

  

func (m* Membership) Leave() error {

return m.serf.Leave()

}

  

func (m *Membership) logError(err error,msg string,member serf.Member) {

m.logger.Error(

msg,

zap.Error(err),

zap.String("name",member.Name),

zap.String("rpc_addr",member.Tags["rpc_addr"]),

)

}
```

이러한 메서드들은 다음과 같은 역할을 한다.

- `isLocal()`: 해당 서프 멤버가 로컬 멤버인지 멤버명을 확인한다.
- `Members()`: 호출 시점의 클러스터 서프 멤버들의 스냅숏을 리턴한다.
- `Leave()`: 자신이 서프 클러스터를 떠난다고 알려준다.
- `logError()`: 받은 에러와 메시지를 로그로 작성한다.


`Membership` 코드를 테스트해보자. `membership_test.go` 파일을 `internal/discovery` 디렉토리에 만들고 다음 코드를 추가하자.


[ServerSideServiceDiscovery/internal/discovery/membership_test.go]

```go
package discovery_test

  

import (

"fmt"

"testing"

"time"

  

"github.com/hashicorp/serf/serf"

"github.com/stretchr/testify/require"

)

  

func TestMembership(t *testing.T) {

	m, handler := setupMember(t, nil)
	m, _ = setupMember(t, m)
	m, _ = setupMember(t, m)

  

require.Eventually(t, func() bool {

return 2 == len(handler.joins) && 3 == len(m[0].Members()) && 0 == len(handler.leaves)},
3*time.Second, 250*time.Millisecond)

  

require.NoError(t, m[2].Leave())

  

require.Eventually(t, func() bool {

return 2 == len(handler.joins) && 3 == len(m[0].Members()) && serf.StatusLeft == m[0].Members()[2].Status && 1 == len(handler.leaves)

}, 3*time.Second, 250*time.Millisecond)

  

require.Equal(t, fmt.Sprintf("%d", 2), <-handler.leaves)

}
```

테스트는 클러스터와 여러 개의 서버를 준비하고 Membership이 멤버십에 조인한 모든 서버를 리턴하는지, 그리고 서버가 클러스터를 떠나면 업데이트하는지를 체크한다. handler의 joins와 leaves 채널은 어떤 서버들에 대해 각각의 이벤트가 얼마나 많이 발생했는지 말해준다. 각 멤버는 현재의 상태를 가진다.


- **Alive**: 서버가 클러스터에서 잘 작동하고 있다.
- **Leaving**: 서버가 클러스터를 정상적으로 떠나고 있다.
- **Left**: 서버가 클러스터를 정상적으로 떠났다.
- **Failed**: 서버가 예상치 못하게 클러스터를 떠났다.

`TestMembership()` 테스트는 `setupMember()` 도우미 함수를 이용해 매번 멤버를 준비한다. `setupMember()` 도우미 함수를 정의해보자.


[ServerSideServiceDiscovery/internal/discovery/membership_test.go]
```go

func setupMember(t *testing.T, members []*discovery.Membership) ([]*discovery.Membership, *handler) {

id := len(members)

  

ports := dynaport.Get(1)

addr := fmt.Sprintf("%s:%d", "127.0.0.1", ports[0])

tags := map[string]string{

"rpc_addr": addr,

}

  

c := discovery.Config{

NodeName: fmt.Sprintf("%d", id),

BindAddr: addr,

Tags: tags,

}

  

h := &handler{}

if len(members) == 0 {

h.joins = make(chan map[string]string, 3)

h.leaves = make(chan string, 3)

} else {

c.StartJoinAddrs = []string{

members[0].BindAddr,

}

}

m, err := discovery.New(h, c)

require.NoError(t, err)

members = append(members, m)

return members, h

}
```

`setupMember()` <mark style="background: #ADCCFFA6;">도우미 함수는 사용하지 않는 포트에 새로운 멤버를 준비</mark>하고, <mark style="background: #ADCCFFA6;">members의 길이를 노드명으로 사용하여 멤버마다 고유한 이름을 가지게 한다.</mark> members의 길이로 멤버가 초기 멤버인지, 이미 조인한 클러스터가 있는지 알 수 있다.


목(mock) 핸들러를 정의하며 테스트 코드를 마무리하자.


[serverSideServiceDiscovery/internal/discovery/membership_test.go]
``` go

  

type handler struct{
	joins chan map[string]string
	leaves chan string
}

  

func (h*handler) Join(id, addr string) error {

if h.joins!=nil{

h.joins<-map[string]string{
	"id":id,
	"addr":addr,
		}

	}
return nil
}

  

func (h *handler) Leave(id string) error {

	if h.leaves!=nil{
		h.leaves<-id
}

return nil

}
```

목 핸들러는 `Membership`이 핸들러의 `Join()`과 `Leave()` 메서드를 얼마나 호출했는지 ID, 주소와 함께 호출한다.


`Membership` 테스트가 통과하는지 실행해보자.

이제 **discovery**와 **membership** 패키지가 준비되었다. 패키지들을 서비스에 통합하여 복제 기능을 구현해보자.


---
# 7.4 디스커버드 서비스의 요청과 로그 복제

서비스 디스커버리에 복제(replication) 기능을 추가해보자. 클러스터에 서버가 여러 개 있다면 그만큼의 복사본을 저장할 수 있다. 서비스는 복제 기능 덕분에 실패에 대한 회복력을 가진다. 노드의 디스크가 고장나서 데이터가 복구되지 않더라도, 다른 디스크의 복제본을 이용해 해결할 수 있는 것이다.

다음 장에서는 서버들을 조율하여 복제가 리더-팔로워(leader-follower) 관계를 갖게 하겠지만, 일단은 서버들이 서로를 발견(discover)하면 (마치 쥐라기 공원의 과학자들이 그러했듯이) 복제해야 하는지를 판단하지 않고 무조건 복제하도록 만들겠다. 7장의 나머지 부분에서는 서비스 디스커버리를 이용하는 간단한 구현, 그리고 다음 장에서 이야기할 조율한 복제를 위한 준비를 다루겠다.

디스커버리 그 자체만으로는 아무 의미가 없다. 디스커버리 이벤트가 서비스의 다른 프로세스를 트리거하여 복제, 합의와 같은 작업을 하게 만드는 것이 중요하다. 우리가 만드는 서비스에서는 서버가 다른 서버를 찾으면 복제하도록 구현할 것이다. 그러려면 서버가 클러스터에 조인할 때 복제하거나, 떠날 때 복제를 끝낼 컴포넌트가 필요하다.

복제는 **풀**(pull)을 기반으로 한다. <mark style="background: #ADCCFFA6;">복제 컴포넌트가 발견한(discovered) 서버에서 소비하고, 복사본을 로컬 서버에 생산하는 것이다.</mark> 풀 기반 복제에서는 소비하는 측에서 데이터 소스에 소비할 새로운 데이터가 있는지 주기적으로 확인하다. **푸시**(push) <mark style="background: #ADCCFFA6;">기반 복제라면 데이터 소스에서 데이터를 복제하는 쪽으로 보내준다.</mark> 다음 장에서는 래프트를 사용해서 푸시 기반 복제를 구현할 것이다.

풀 기반 시스템의 유연성은 작업과 소비의 방식이나 시점이 다를 수 있는 상황에서의 로그와 메시지 시스템에 적합하다. 예를 들어 한 클라이언트는 데이터를 스트림으로 계속해서 보내고, 또 다른 클라이언트는 24시간마다 배치 프로세스로 데이터를 처리할 수 있다. 서버 간 복제에서는 최신 데이터를 최소한의 레이턴시로 같은 서버에 복제한다. 그래서 풀 베이스와 푸시 베이스 시스템 차이가 없다. 하지만, 풀 베이스 복제를 만들어보면 왜 합의가 필요한지 쉽게 이해할 수 있을 것이다.


클러스터에 복제 기능을 넣으려면, 복제 컴포넌트를 만들어서 클러스터에 서버가 조인하거나 떠날 때 멤버십을 처리하는 역할을 맡겨야 한다. 클러스터에 서버가 조인하면 컴포넌트는 서버에 연결하고 연결한 서버에서 반복해 소비하여 로컬 서버에 생산해준다.

`internal/log` 디렉터리에 `replicator.go` 파일을 만들고 다음 코드를 추가하자.


[ServerSideServiceDiscovery/internal/log/replicator.go]
```go
package log

  

import (

"sync"

  

api_v1 "github.com/distributed_service_go/Part7-ServerSideServiceDiscovery/api/v1"

"go.uber.org/zap"

"google.golang.org/grpc"

)

  
  
  

type Replicator struct{

DialOptions []grpc.DialOption
LocalServer api_v1.LogClient

  

logger *zap.Logger
mu sync.Mutex
servers map[string]chan struct{}
closed bool
close chan struct{}

}
```

복제 역할을 하는 레플리케이터(replicator)는 gRPC 클라이언트를 이용해 다른 서버에 연결하는데, 클라이언트가 서버 인증을 할 수 있게 설정해주어야 한다. `DialOptions`에 설정해주면 된다. `servers` 필드는 서버 주소를 키로, 채널을 값으로 하는 맵이다. 레플리케이터가 연결한 서버가 실패하거나 클러스터를 떠나서, 복제하는 것을 멈출 때 사용한다. 레플리케이터가 다른 서버들로부터 소비한 메시지들의 복사본은 생산 함수를 호출하여 저장한다.


`Replicator` 구조체 아래에 `Join()` 메서드를 추가하자.


[ServerSideServiceDiscovery/internal/log/replicator.go]
```go
  

func (r *Replicator) Join(name, addr string) error {

r.mu.Lock()
defer r.mu.Unlock()
r.init()

  

if r.closed{
	return nil
}

  

if _,ok:=r.servers[name]; ok{

	// 이미 복제중이니 건너뛰자.
	return nil
}

r.servers[name]=make(chan struct{})

  

go r.replicator(addr,r.servers[name])
return nil

}
```

`Join(name,addr string)` 메서드는 받은 서버 주소를 복제할 서버 목록에 추가하고, 새로운 고루틴으로 복제를 시작한다.


복제 로직을 구현한 `replicate(addr string)` 메서드를 추가하자.


[ServerSideServiceDiscovery/internal/log/replicator.go]
```go
  

func (r*Replicator) replicator(addr string,leave chan struct{}) {

	cc,err:=grpc.NewClient(addr,r.DialOptions...)

	if err != nil {
	r.logError(err,"failed to dial",addr)
		return
}

defer cc.Close()

client:=api_v1.NewLogClient(cc)

  

ctx:=context.Background()
stream,err:=client.ConsumeStream(ctx,&api_v1.ConsumeRequest{Offset: 0,})

  

if err != nil {

	r.logError(err,"failed to consume",addr)
	return

}

records:=make(chan, *api.Record)

go func() {
	for{
	
	recv,err:=stream.Recv()

if err != nil {
	r.logError(err,"failed to receive",addr)
	return
		}

	records<-recv.Record

	}

}()

}
```

생산자(producer)와 소비자(consumer) 스트림을 테스트할 때와 비슷하다. 클라이언트를 생성하고 스트림을 열어서 서버의 모든 로그를 소비한다.


다음 코드를 추가형 replicate() 메서드를 마무리하자.

[ServerSideServiceDiscovery/internal/log/replicator.go]
```go
  

for {

select {

case <-r.close:

return

case <-leave:

return

case record := <-records:

_, err = r.LocalServer.Produce(ctx,

&api_v1.ProduceRequest{Record: record})

  

if err != nil {

r.logError(err, "failed to produce", addr)

return

}

  

}

}

}
```

찾아낸 서버의 로그를 스트림을 통해 반복하여 소비하면서, 로컬 서버에 생산하면서 복사본을 저장한다. 특정 서버가 실패하거나 클러스터를 떠나서 레플리케이터가 그 서버의 채널을 닫을 때까지 계속해서 메시지를 복제한다. 레플리케이터는 서프가 다른 서버가 클러스터를 떠났다는 이벤트를 받으면 채널을 닫은 다음 Leave() 메서드를 호출한다.

`Leave(name string)` 메서드를 이어서 추가하자.

[ServerSideServiceDiscovery/internal/log/replicator.go]
```go
func (r *Replicator) Leave(name string) error {

	r.mu.Lock()
	defer r.mu.Unlock()
	r.init()

if _,ok:=r.servers[name];!ok {

		return nil
}

close(r.servers[name])
delete(r.servers,name)
return nil

}
```

`Leave(name string)` <mark style="background: #ADCCFFA6;">메서드는 특정 서버가 클러스터를 떠날 때 해당 서버를 복제할 서버 목록에서 제거하며 채널 역시 닫아준다.</mark> 채널을 닫아주면 `replicate()` 메서드의 고루틴에 있는 리시버에 신호가 전달되어 해당 서버의 복제를 멈추게 한다.


`init()` 도우미 메서드를 추가하자.


[ServerSideServiceDiscovery/internal/log/replicator.go]

```go
func (r *Replicator) init() {

if r.logger==nil{

r.logger=zap.L().Named("replicator")

}

  

if r.servers==nil {

r.servers=make(map[string]chan struct{})

}

  

if r.close==nil {

r.close=make(chan struct{})

}

}
```

`init()` 도우미 메서드는 서버 맵을 **게으르게 초기화**(lazily initialize)한다. 지연한 초기화로 구조체에 유용한 제로 값(useful zero value)을 가지게 하는데, 유용한 제로 값을 가지면 같은 기능을 하면서도 API의 크기와 복잡도를 줄일 수 있다. 유용한 제로 값이 없다면, 레플리케이터의 생성자 함수나 `servers` 필드를 엑스포트해서 사용자가 설정할 수 있게 해줘야 하는데, 결국 사용자가 알아야 할 API가 너무 많아지고, 구조체를 사용하기 전에 작성해야 할 코드도 많아진다.

Close() 메서드를 추가하자.


[ServerSideServiceDiscovery/internal/log/replicator.go]
```go
func (r*Replicator)Close()error {

	r.mu.Lock()

	defer r.mu.Unlock()

	r.init()

	if r.closed{

		return nil
		}

r.closed=true

close(r.close)

return nil

}
```

`Close()`메서드는 레플리케이터를 닫아서 새로 조인하는 서버나 기존에 복제하던 서버들의 복제를 멈춘다. 정확히 말하자면 `replicate()` 메서드의 고루틴을 종료한다.

마지막으로, 에러를 처리하는 도우미 함수인 `logError(err error,msg,addr string)` 메서드를 추가하자.


[ServerSideServiceDiscovery/internal/log/replicator.go]
```go
func (r*Replicator)logError(err error,msg,addr string) {

	r.logger.Error(

	msg,
	zap.String("addr",addr),
	zap.Error(err),
)

}

```

이 메서드는 코드를 간결하게 하고자 에러를 로그에 저장하는 것이 전부이다. 사용자가 에러에 접근하기를 원한다면, 에러 채널을 외부에 노출하고 에러를 보내서 사용자가 받아 처리하게 한다.

레플리케이터 구현을 마쳤다. 컴포넌트 개념으로 본다면 **레플리케이터**, **멤버십**, **로그**, **서버**까지 구현한 것이다. 각각의 서비스 인스턴스는 이들 컴포넌트를 설정하고 연결해서 함께 작동하게 해야 한다. 간단하고 잠깐 실행하는 프로그램이라면, `run` 패키지를 만들고 `Run()` 함수를 외부에 노출하여 프로그램을 실행할 수 있도록 한다. 롭 파이크(Rob Pike)의 아이비 프로젝트(Ivy project)가 그렇게 작동한다. 좀 더 복잡하고 오래 실행하는 서비스라면 **agent** 패키지를 만들고 **Agent** 자료형을 외부로 노출하여 서비스를 구성하는 여러 컴포넌트와 프로세스를 관리하게 한다. 하시코프 사의 콘술이 이렇게 작동한다.

여기서 Agent를 작성하고 로그, 서버, 멤버십, 레플리케이터를 전부 끝에서 끝까지 종단 간(end-to-end) 테스트를 해 보자.

`internal/agent` 디렉터리를 만들고 `agent.go` 파일을 생성하여 다음 코드를 추가하자.

```go
```