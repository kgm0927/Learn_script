
우리는 어떠한 문제를 해결하고자 프로젝트를 시작한다. 하지만 문제의 해결에만 집중하다 보면 중요한 것을 놓칠 수 있다. 보안이 그 중 하나로 정말 중요한 주제이지만 가볍게 취급된다.

보안을 고려한 솔루션은 구현하기 훨씬 복잡하지만, 사람들이 실제로 사용하는 서비스라면 보안은 빠뜨릴 수 없다. 그리고 보안 기능을 넣으려면 처음부터 함께 구현해야 한다. 프로젝트를 다 만든 다음에 추가하기란 매우 어렵다. ==이 책에서도 데이터 스트림을 예로 들자면 단순한 구현이 아닌, 보안을 적용한 데이터 스트림을 구현하려 한다.==

소프트웨어 엔지니어 커리어를 막 시작했다면 보안 기능이 그리 달갑진 않을 것이다. 제대로 구현해도 알아주는 사람도 없고, 잘못 구현할까 조심스러우면서 한편으로는 지겹기도 하다. <mark style="background: #ADCCFFA6;">하지만, 필자는 서비스형 소프트웨어(software-as-a-service, SaaS) 스타트업을 몇 개 만들며 경력을 쌓는 과정에서 보안에 대한 생각이 많이 바뀌었다.</mark> 서비스의 보안은 프로젝트에서 해결하려는 문제만큼 중요하다. 그 이유는 다음과 같다.

- ==**해킹을 막아준다**==: 보안의 모범 사례들을 따르지 않으면 놀랄 만큼 자주 서비스가 깨지고 중요한 데이터가 새어 나간다. 이런 사고에 관한 뉴스를 종종 보았을 것이다. 서비스를 구현할 때면, 필자는 안전하게 지키려는 데이터가 온 세상에 공개되는 상상을 하고 한다. 그리고 그런 일이 절대로 일어나서는 안된다고 다짐한다. 다행이 아직은 그런 일이 없었다.
- ==**보안이 우수해야 팔린다.**==: 고객이 내가 만든 소프트웨어를 구매하려 할 때, 가장 중요한 요소는 결국 보안 요구사항을 만족하였는가이다.
- ==**보안 기능을 나중에 넣기는 어렵다.**==: 기본적인 보안 마저  부족한 서비스에 보안을 더 추가하는 작업은 힘들고 어렵다. 처음부터 보안 기능을 넣는 것이 훨씬 쉽다.

---
# 5.1 서비스의 보안의 세 단계

분산 서비스의 보안은 다음 세 단계로 나눌 수 있다.

1. 주고받는 데이터는 암호화하여 중간자 공격(man-in-the-middle-attack,MITM)에 대비한다.
2. 클라이언트를 인증한다.
3. 인증한 클라이언트의 권한을 결정한다.

이러한 단계로부터 얻을 수 있는 이점을 알아보고 서비스에 단계별로 구현해보자.



#### 5.1.1 주고받는 데이터의 암호화

데이터를 암호화하여 주고받으면 중간자 공격을 막을 수 있다. 대표적인 중간자 공격으로는 능동 도청(active eavesdropping)이 있다. 공격자는 피해자들이 서로 직접 연결되었다고 생각되게 하면서, 중간에서 대화를 가로채거나 제어한다. 그뿐만 아니라 둘 사이의 메시지를 악의적으로 변경할 수 있다. 예를 들어 밥(Bob)이 앨리스(Alice)에게 페이팔(PayPal)을 이용해 송금하려 할 때 맬러리(Mallory)가 수신 계좌를 자신의 계좌로 바꿀 수 있다.

>[!tip] 암호학에서 관습적으로 쓰이는 이름
>밥(Bob), 앨리스(Alice), 맬러리(Mallory)는 암호학에서 흔히 쓰이는 이름이다. 앨리스와 밥은 메시지를 교환하려 하며 맬러리는 악의를 가진, 공격자이다. 이때 이름의 첫 문자를 역할에 맞게 라임을 주어 기억하기 쉽게 했다. 예를 들어 맬러리는 악의적인 공격자(malicious attacker), 이브(Eve)는 도청자(eavesdropper), 크레이그(Craig)는 비밀번호 크래커(password cracker)이다.


주고받는 데이터를 중간자 공격으로 막아주는, 가장 널리 쓰이는 암호화 방법은 TLS이다. SSL을 계승한 TLS는 한때 온라인 은행처럼 보안이 매우 중요한 웹사이트에만 필요하다고 여겨졌으나, 이제는 모든 사이트가 TLS를 사용해야 한다는 인식이 자리 잡았다. <mark style="background: #ADCCFFA6;">모던 브라우저들은 TLS를 쓰지 않는 사이트에 대해 안전하지 않으니 주의하라 알리며 사용하지 말 것을 권한다.
</mark>

<mark style="background: #FF5582A6;">클라이언트와 서버의 통신의 TLS의 핸드셰이크(handshake)부터 시작한다.</mark> 핸드셰이크 과정에서 크라이언트와 서버는 다음 과정을 거친다.

1. **사용할 TLS버전을 명시한다.**
2. **사용할 암호화 스위트(cipher suite, 암호화 알고리즘들의 모음)을 결정한다.**
3. **서버의 개인 키와 인증 기관의 서명으로 서버를 인증한다.**
4. **핸드셰이크가 끝나면 대칭 키 암호화를 위해 세션 키를 생성한다.**

핸드셰이크가 끝나면 클라이언트와 서버는 안전하게 통신할 수 있다.

TLS 핸드셰이크는 TLS가 알아서 처리해준다. 우리는 클라이언트와 서버의 인증서만 준비하면 된다. 이 인증서를 이용해서 gRPC over TLS를 할 수 있다.

이제부터 TLS 지원을 구현하여 서비스가 주고받는 데이터를 암호화하고 서버를 인증할 것이다.


#### 5.1.2 클라이언트 인증

TLS로 클라이언트와 서버 통신의 보안을 강화했으니, 다음은 인증이다. <mark style="background: #ADCCFFA6;">여기서 인증이란 클라이언트가 누구인지 확인하는 것이다</mark>(참고로, 서버는 TLS가 이미 인증했다). 예를 들어 트위터는 여러분이 트위터에 포스팅할 때마다 트윗(tweet)을 포스팅하는 사람이 본인인지 확인하다.

<mark style="background: #ADCCFFA6;">대부분의 웹 서비스는 TLS를 단방향 인증으로 서버만 인증한다.</mark> 클라이언트 인증은 애플리케이션에서 구현할 몫이며, 보통을 사용자명-비밀번호와 토큰(token)의 조합으로 구현한다. TLS 상호 인증(mutual authentication, 양방향 인증(two-way authentication)이라고도 한다)은 기계 간 통신(machine-to-machine communication)에 많이 사용한다. 분산 시스템이 대표적이다. 이 경우에는 서버와 클라이언트 모두 인증서를 이용해 자신을 인증해야 한다. TLS 상호 인증은 효과적이고 간단하며 이미 많이 적용되었다(이용자도 많고 지원하는 기술도 많다). 많은 회사가 내부의 분산 서비스 사이의 통신 보안에 TLS 상호 인증을 사용한다. TLS 상호 인증의 사용자가 많은 많큼, 새로운 서비스를 만든다면 이를 지원해야 한다. 이제 서비스에 TLS 상호 인증을 구현하자.


#### 5.1.3 클라이언트 권한

인증(authentication)과 권한 결정(인가(authorization))은 깊계 연관되며 둘 다 'auth'라 줄여 불리고는 한다. 또한 인증과 권한은 요청의 생명주기에서 거의 동시에 이루어지며 서버 코드에서도 서로 가까운 곳에 있다. 사실 특정 리소스의 소유자가 하나인 대부분의 웹 서비스에서는 인증과 권한이 하나의 프로세스이다. 예를 들어 트위터 계정은 소유자가 하나이다. 그래서 클라이언트에서 인증이 되면, 그 계정에서 할 수 있는 활동은 다 할 수 있다.

인증과 권한의 구분은 리소스의 접근을 공유하고 소유권의 레벨이 다양할 때 필요하다. 우리가 만든 로그 서비스를 예를 들면, 앨리스는 로그의 소유자이면서 읽고 쓰기 접근 권한을 가지고 있지만 밥은 읽을 권한만 가지는 식이다. 이런 경우에 세분화한 접근 제어 권한이 필요하다.

우리 서비스에서는 목록에 기반한 제어 권한을 구현하겠다. 접근 제어 권한으로 클라이언트에 로그를 읽거나 쓸 권한을 줄지를 제어할 것이다.

분산 시스템 보안의 핵심 세 가지를 모두 들여다보았으니, 서비스에 보안을 구현해보자.


---
# 5.2 TLS로 서버 인증하기

TLS가 필요한 이유와 작동하는 원리를 간단한 살펴보았다. 이제 주고받는 데이터를 암호화하고 서버를 인증할 때 TLS를 사용해보자. 인증서를 얻고 사용할 때 더 쉽게 관리하는 법도 다루겠다.


#### 5.2.1 CFSSL로 나만의 CA 작동하기

서버 코드를 바꾸기 전에 인증서부터 준비하자. 서드파티 인증 기관(certificate authority, CA)에서 인증서를 받을 수도 있지만 인증 기관에 따라 돈이 들고 꽤 번거롭다. 우리가 만드는 서비스처럼 내부에서만 사용하는 서비스는 이러한 서드파티 인증기관에서 발급한 인증서까지는 필요 없다. 신뢰할 수 있는 인증서가 필요하다고 해서 반드시 코모도(comodo)나 레츠인크립트(Let's Encrypt)와 같은 회사에서 발급하지 않아도 된다. <mark style="background: #ADCCFFA6;">직접 CA로 인증서를 발급하면 되는 것이다.</mark> 적절한 도구만 쓰면 무료로 발급할 수 있다.

글로벌 보안업체인 클라우드플레어(CloudFlare)가 만든 CFSSL 툴킷은 TLS 인증서를 서명하고, 증명하며, 묶을(bundle) 수 있다. 클라우드플레어는 CFSSL을 자사 내부 서비스의 TLS 인증서를 만들어내는 그들만의 인증 기관처럼 사용한다. 오픈소스에서 누구든 사용할 수 있으며, 심지어 레츠인크립트도 CFSSL을 쓴다. 이처럼 유용한 툴킷을 만든 클라우드플레어에 감사할 따름이다.


- **cfssl**: TLS 인증서를 서명하고, 증명하며, 묶어주고 그 결과를 JSON으로 내보낸다.
- **cfssljson**: JSON 출력을 받아서 키, 인증서, CSR, 번들 파일로 나눈다.


다음 명령으로 설치해보자.

``` shell
┌──(kali㉿kali)-[~]
└─$ go install github.com/cloudflare/cfssl/cmd/cfssl@latest        
go: downloading github.com/cloudflare/cfssl v1.6.5
go: downloading github.com/go-sql-driver/mysql v1.7.1
go: downloading github.com/lib/pq v1.10.9
go: downloading github.com/mattn/go-sqlite3 v1.14.22
go: downloading github.com/jmoiron/sqlx v1.3.5
go: downloading github.com/kisielk/sqlstruct v0.0.0-20201105191214-5f3e10d3ab46
go: downloading github.com/google/certificate-transparency-go v1.1.7
go: downloading golang.org/x/crypto v0.19.0
go: downloading github.com/jmhodges/clock v1.2.0
go: downloading github.com/zmap/zlint/v3 v3.5.0
go: downloading github.com/zmap/zcrypto v0.0.0-20230310154051-c8b263fd8300
go: downloading google.golang.org/protobuf v1.32.0
go: downloading golang.org/x/net v0.20.0
go: downloading k8s.io/klog/v2 v2.100.1
go: downloading github.com/pelletier/go-toml v1.9.3
go: downloading golang.org/x/text v0.14.0
go: downloading github.com/weppos/publicsuffix-go v0.30.0
go: downloading github.com/go-logr/logr v1.2.4
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ go install github.com/cloudflare/cfssl/cmd/cfssljson@latest
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ 

```

CA를 초기화하고 인증서를 생성하려면 cfssl에 다양한 설정 파일을 전달해야 한다. CA 생성과 서버 인증서 생성을 위한 각각의 설정 파일과, CA에 관한 일반적인 정보를 담은 설정 파일이 필요하다. test 디렉터리를 만들고 ca-csr.json 파일에 다음 JSON을 넣어주자.

```json

// SecureYourServices/test/ca-csr.json

{

"CN":"My Awesome CA",

"key":{

"algo":"rsa",

"size":2048

},

"names":[

{ "C":"CA",

"L":"ON",

"ST":"Toronto",

"O":"My Awesome Company",

"OU":"CA Services"

}

]

}
```

cfssl은 이 파일로 CA의 인증서를 설정한다. CN은 Common Name을 뜻하며 "My Awesome CA"라고 이름 붙였다. key는 인증서 서명에 알고리즘과 키의 크기를 담고 있으며, names는 인증서에 추가할 다양한 이름 정보이다. names의 각 객체는 최소한 하나 이상의 "C", "L", "O", "OU", 또는 "ST"가 있어야 하며 하나 이상 조합할 수도 있다. 이들의 의미는 다음과 같다.


- **C**: 나라(country)
- **L**: 지역(locality or municipality)
- **ST**: 주(state or province)
- **O**: 조식(organization)
- **OU**:부서(organizational unit, 예를 들어 key를 소유한 부서)


이번에는 CA의 정책을 정의하는 **test/ca-config.json**을 추가한다.

``` json

// SecureYourServices/test/ca-config.json

{

"signing":{

"profiles":{

"server":{

"usages":[

"signing",

"key encipherment",

"server auth"

]

},

"client":{

"expiry" : "8760h",

"usages":[

"signing",

"key encipherment",

"client auth"

]

}

}

}

}

```

**CA**가 어떤 인증서를 발행할지에 관한 설정이다. <mark style="background: #ADCCFFA6;">**signing** 부분은 서명 정책에 대한 설정이다. 설정대로 만들어지는 CA는 클라이언트와 서버의 인증서를 생성할 수 있고 생성한 인증서는 1년 뒤로 만료되며 디지털 서명, 암호화 키, 인증에 사용할 수 있다.</mark>


**server-csr.json** 파일을 만들고 다음 내용을 추가하자.

``` json

// SecureYourServices/test/server-csr.json

{

"CN":"127.0.0.1",

"hosts":[
"localhost",
"127.0.0.1"
],

	"key":{
	"algo":"rsa",
	"size":"2048",
},

	"names":[

	{

	"C":"CA",

	"L":"ON",

	"ST":"Toronto",

	"O":"My Awesome Company",
	
	"OU":"Distributed Services",

	}

	]

}

```

cfssl은 이 설정 파일로 서버 인증서를 설정한다. hosts 필드는 인증서가 유효한 도메인명을 담고 있으며, 로컬 네트워크에서 서비스를 실행하기에 127.0.0.1와 localhost를 넣어두었다.

**Makefile**을 업데이트하여 cfssl과 cfssljson을 사용해서 인증서를 생성해보자. Makefile은 다음과 같다.


``` Makefile
CONFIG_PATH=${HOME}/.proglog/

  

.PHONY: init

init:

mkdir -p ${CONFIG_PATH}

  

.PHONY: gencert

gencert:

cfssl gencert \

-initca test/ca-csr.json | cfssljson -bare ca

  

cfssl gencert \

-ca=ca.pem \

-ca-key=ca-key.pem \

-config=test/ca-config.json \

-profile=server \

test/server-csr.json | cfssljson -bare server

mv *.pem *.csr ${CONFIG_PATH}

  

.PHONY: test

test:

go test -race ./...

  

.PHONY: compile

compile:

protoc api/v1/*.proto \

--go_out=. \

--go-grpc_out=. \

--go_opt=paths=source_relative \

--go-grpc_opt=paths=source_relative \

--proto_path=.
```

추가한 `CONFIG_PATH` 변수는 생성한 인증서를 저장할 위치이다. init 타깃이 해당 디렉터리를 생성한다. 파일 시스템의 변하지 않는, 알려진 위치에 두면 코드에서 인증서를 찾고 사용하기 편리하다. `gencert` 타깃은 cfssl 도구와 우리가 추가한 설정 파일을 사용하여 CA와 서버에서 쓸 인증서와 개인 키를 생성한다.

테스트에서 설정 파일들을 자주 참조하므로 별도의 패키지에 생성한 파일들의 경로를 변수에 넣어두면 참조하기 더 쉽다. `internal/config` 디렉터리에 `files.go`파일을 만들자.


``` go

// SecureYourServices/internal/config/files.go

package config

  

import (
"os"
"path/filepath"
)

  

var (

CAFile= configFile("ca.pem")
ServerCertFile=configFile("server.pem")
ServerKeyFile=configFile("server-key.pem")

)

  

func configFile(filename string)string {

if dir:=os.Getenv("CONFIG_DIR"); dir!=""{
return filepath.Join(dir,filename)
}

 
homeDir,err:=os.UserHomeDir()
if err != nil {
panic(err)
}

return filepath.Join(homeDir,".proglog",filename)

}
```

생성한 인증서들의 경로를 정의한 이 변수들은 테스트할 때 인증서를 찾고 파싱하고자 사용한다. 만약 go 언어에서 함수 호출의 결과를 상수로 저장할 수 있다면 상수를 썼을 것이다.

인증서와 키 파일들로 `*tls.Configs`들을 만들어보겠다. 이를 위해 도우미 함수와 구조체를 추가해보자. config 디렉터리에 tls.go 파일을 만들고 다음 코드부터 추가하자.


``` go
package config

  

import (

"crypto/tls"

"crypto/x509"

"fmt"

"os"

)

  

func SetupTLSConfig(cfg TLSConfig) (*tls.Config, error) {

var err error

tlsConfig := &tls.Config{}

  

if cfg.CertFile != "" && cfg.KeyFile != "" {

tlsConfig.Certificates = make([]tls.Certificate, 1)

tlsConfig.Certificates[0], err = tls.LoadX509KeyPair(

cfg.CertFile,

cfg.KeyFile,

)

  

if err != nil {

return nil, err

}

}

  

if cfg.CAFile != "" {

b, err := os.ReadFile(cfg.CAFile)

if err != nil {

return nil, err

}

  

ca := x509.NewCertPool()

ok := ca.AppendCertsFromPEM([]byte(b))

  

if !ok {

return nil, fmt.Errorf(

"failed to parse root certificate: %q",

cfg.CAFile,

)

}

  

if cfg.Server {

tlsConfig.ClientCAs = ca

tlsConfig.ClientAuth = tls.RequireAndVerifyClientCert

} else {

tlsConfig.RootCAs = ca

}

tlsConfig.ServerName = cfg.ServerAddress

}

  

return tlsConfig, nil

  

}
```

테스트마다 조금씩 다른 `*tls.Config` 설정을 사용하는데, SetupTLSConfig() 함수는 테스트마다 요청하는 자료형에 맞는 `*tls.Config`를 리턴한다. 각각의 설정은 다음과 같다.

- 클라이언트의 `*tls.Config`는 서버 인증서를 검증하는 설정으로 `*tls.Config`의 `RootCAs`를 설정한다.
- 클라이언트의 `*tls.Config`는 서버 인증서와 함께, 서버가 클라이언트 인증서를 검증할 수 있게 RootCAs와 Certificates를 설정한다.
- 서버의 `*tls.Config`는 클라이언트 인증서의 검증과 함께, 클라이언트가 서버 인증서를 검증할 수 있게 ClientCAs, Certificates를 설정하고 ClientAuth 모드를 `tls.RequireAndVerifyClientCert`로 설정한다.


이번에는 구조체를 추가하자.

``` go
// SecureYourServices/internal/config/tls.go

type TLSConfig struct {

CertFile string
KeyFile string
CAFile string
ServerAddress string
Server bool

}
```

`TLSConfig`는 `SetupTLSConfig()` 함수가 사용할 매개변수이며, 이 설정에 따라 그에 맞는 `*tls.Config`를 리턴한다.

테스트로 돌아가서, 클라이언트가 CA를 사용하여 인증서를 검증하도록 해보자. 서버 인증서가 다른 인증 기관에서 만든 것이라면, 즉, 클라이언트가 가진 CA로 인증서를 검증할 수 없다면, 클라이언트는 서버를 신뢰할 수 없기에 연결하지 않는다. `setup_test.go` 파일에 패키지들을 임포트하자.

``` go
"github.com/distributed_service_go/Part5-SecureYourServices/internal/config"
"google.golang.org/grpc/credentials"
```

그리고 `setupTest()` 함수의 코드를 다음과 같이 바꾸자.

``` go
// SecureYourServices/internal/server/server_test.go

t.Helper()

l, err := net.Listen("tcp", ":0")
require.NoError(t, err)
  

clientTLSConfig,err:=config.SetupTLSConfig(config.TLSConfig{
CAFile: config.CAFile,
})

  

require.NoError(t, err)

  
  

clientCreds:=credentials.NewTLS(clientTLSConfig)

cc,err:=grpc.NewClient(
l.Addr().String(),
grpc.WithTransportCredentials(clientCreds),
)

require.NoError(t, err)

  

client=log_v1.NewLogClient(cc)

```

이 코드에서는 클라이언트의 TLS 인증서가 우리가 만든 CA를 클라이언트의 Root CA로, 다시 말해 서버의 인증서를 검증할 때 사용하도록 설정했다. 그리고 클라이언트는 이 인증서를 사용해 연결한다.

이번에는 서버에 인증서를 넣고 TLS 연결을 처리하도록 하자. 다음 코드를 추가하자.

``` go

  

serverTLSConfig, err:=config.SetupTLSConfig(config.TLSConfig{

CertFile: config.ServerCertFile,

KeyFile: config.ServerKeyFile,

CAFile: config.CAFile,

ServerAddress: l.Addr().String(),

})

  

require.NoError(t,err)

serverCreds:=credentials.NewTLS(serverTLSConfig)

  

dir,err:=os.MkdirTemp("","server-test")

require.NoError(t,err)

  

clog, err := log.NewLog(dir, log.Config{})

require.NoError(t, err)

  

cfg = &Config{

CommitLog: clog,

}

  

if fn != nil {

fn(cfg)

}

server, err := NewGRPCServer(cfg,grpc.Creds(serverCreds))

require.NoError(t, err)

  

go func() {

server.Serve(l)

}()

return client, cfg, func() {

server.Stop()

cc.Close()

l.Close()

clog.Remove()

}

}
```

이 코드에 먼저 서버의 인증서와 키를 파싱했다. 그리고 서버의 인증서를 설정할 대 사용했다. 이렇게 만든 인증서를 `NewGRPCServer()` 함수의 gRPC 서버 옵션으로 전달해서 gRPC 서버를 만들었다. gRPC 서버 옵션으로 gRPC 서버의 여러 기능을 활성화할 수 있다. 여기서는 서버 옵션을 위한 인증서 설정에 사용했는데, 그 외에도 연결의 타임아웃이나 keep alive 정책 등 다양한 서버 옵션을 설정할 수 있다.

마지막으로 server.go 파일의 `NewGRPCServer()` 함수를 수정하여 gRPC 서버 옵션을 받아서 서버를 생성하도록 하자. 함수를 다음과 같이 수정한다.

``` go
func NewGRPCServer(config *Config, opts ...grpc.ServerOption) (*grpc.Server, error) {

gsrv := grpc.NewServer()

srv, err := newgrpcServer(config)

if err != nil {

return nil, err

}

log_v1.RegisterLogServer(gsrv, srv)

return gsrv, nil

}
```

여기까지 코드를 `$ make test`로 테스트하면 무사히 통과할 것이다. 이제 서버는 인증되고 연결은 암호화로 보호된다. 테스트 코드의 클라이언트 연결을 `grpc.WithInsecure()` 다이얼 옵션(dial option)으로 되돌려 테스트하면 실패할 것이다. 서버는 클라이언트가 TLS를 사용하지 않으면 연결하지 않기 때문이다.

클라이언트가 서버 인증을 하면, 중간의 공격자가 아닌 진짜 서버이기에 안심하고 통신할 수 있다. 이제는 TLS 상호 인증으로 클라이언트 역시 신뢰할 수 있는지 검증하자.

---
# 5.3 TLS 상호 인증으로 클라이언트 인증하기

TLS를 이용해 연결을 암호화하고 서버를 인증해보았다. 여기서 더 나아가 TLS 상호 인증(또는 양방향 인증)을 구현해보자. 서버 역시 CA를 사용하여 클라이언트를 검증할 것이다.

먼저 클라이언트 인증서가 필요하다. cfssl과 cfssljson으로 생성할 수 있다. 다음 JSON을 test 디렉터리의 client-csr.json 파일에 넣어주자.

[./test/client-csr.json]
``` json
{

"CN":"client",

"hosts":[""],

"key":{

"algo":"rsa",

"size":2048

},

"names":[

{"C":"CA",

"L":"ON",

"ST":"Toronto",

"O":"My Company",

"OU":"Distributed Service"

}

]

}
```

CN 필드가 필요하다. 클라이언트의 ID 또는 사용자명으로, 권한을 줄 때 사용하는 ID이다. 이 부분은 뒤에서 구현하겠다.

Makefile 파일의 getcert 타깃을 수정해서 다음 부분을 추가하자. 서버 인증서를 생성하는 부분 아래에 추가한다.

[.ch05/Makefile]
``` makefile
cfssl gencert \

-ca=ca.pem \

-ca-key=ca-key.pem \

-config=test/ca-config.json \

-profile=client \

test/client-csr.json | cfssljson -bare client
```



추가한 다음에 `$ make gencert` 명령으로 클라이언트 인증서를 생성하자.

`internal/config/files.go` 파일에 클라이언트 인증서를 위한 설정 파일 변수를 추가하자(다음 코드의 강조한 부분이다).


[./ch05/internal/config/files.go]

``` go

var (

CAFile = configFile("ca.pem")

ServerCertFile = configFile("server.pem")

ServerKeyFile = configFile("server-key.pem")

ClientCertFile = configFile("client.pem")

ClientKeyFile = configFile("client-key.pem")

)
```


다음으로, 서버가 클라이언트 인증서가 우리 CA로 서명되었는지 검증하도록 수정하자. `server_test.go` 파일의 서버 설정 부분을 다음과 같이 수정하자.

``` go
clientTLSConfig, err := config.SetupTLSConfig(config.TLSConfig{

CertFile: config.ClientCertFile, // 추가

KeyFile: config.ClientKeyFile, // 추가

CAFile: config.CAFile,

})

require.NoError(t, err)

clientCreds := credentials.NewTLS(clientTLSConfig)

  

cc, err := grpc.NewClient(

l.Addr().String(),

grpc.WithTransportCredentials(clientCreds),

)

require.NoError(t, err)

  

client = log_v1.NewLogClient(cc)

  

serverTLSConfig, err := config.SetupTLSConfig(config.TLSConfig{

CertFile: config.ServerCertFile,

KeyFile: config.ServerKeyFile,

CAFile: config.CAFile,

ServerAddress: l.Addr().String(),

Server: true,

})
```
테스트는 통과할 것이다. <mark style="background: #ADCCFFA6;">클라이언트 인증서를 우리 CA로 만들었고, 그 CA로 서버에서 검증하기 때문이다.</mark> 재미 삼아 다른 CA로 클라이언트 인증서를 만들고 사용해보자. 테스트는 실패할 것이다.


서버와 클라이언트는 서로의 인증서에 대해 CA로 TLS 상호 인증했다. 서버는 중간자의 도청 걱정 없이 실제 클라이언트와 안심하고 통신한다.


---
# 5.4 ACL로 권한 부여하기

클라이언트 뒤에 누가 있는지를 **인증**하면, 인증한 누군가의 특정 행위에 대한 **권한**을 확인하게 된다. 권한이란 누군가가 무엇인가에 접근할 수 있을지를 확인하는 것이다.

권한을 구현하는 가장 간단한 방법은 **접근 제어 목록**(access control list,ACL)이다. ACL 은 규칙 테이블이라 할 수 있는데 각 행은 'A는 B라는 행위를 C라는 대상에 할 수 있다'는 형식의 규칙을 담는다. 예를 들어 이런 규칙이 있다고 하자. 앨리스는 `Distributed Services with Go` 책을 읽을 권한이 있다. 여기에서 앨리스는 행위의 주체, 읽는 것은 행위, `Distributed Services with Go` 책은 대상이다.

ACL은 만들기가 쉽다. 맵(map)이나 CSV 파일과 같은 테이블이 뿐이며 데이터로 전환할 수 있다. 조금 더 복잡하게 구현한다면 키-값 저장(key-value store) 또는 관계형 데이터베이스에 저장할 수 있다. 그래서 ACL 라이브러리를 처음부터 구현하기란 어렵지 않지만, 여기서는 **Casbin**이라는 라이브러리를 써보자. ACL을 포함한 다양한 제어 모델에 기반하여 권한을 강제한다. Casbin은 여러 서비스에서 많이 사용하고 테스트했으며 확장할 수 있다. 어떻게 사용하고 활용할지 알아보자.

우선 Casbin 패키지를 다음 명령으로 추가하자.

``` bash
                                                                                                                   
┌──(kali㉿kali)-[~/…/src/github.com/distributed_service_go/Part5-SecureYourServices]
└─$ go get github.com/casbin/casbin@latest
go: downloading github.com/casbin/casbin v1.9.1
go: downloading github.com/Knetic/govaluate v3.0.1-0.20171022003610-9aa49832a739+incompatible
go: added github.com/Knetic/govaluate v3.0.1-0.20171022003610-9aa49832a739+incompatible
go: added github.com/casbin/casbin v1.9.1
                                                 
```

우리는 **internal** 패키지에서 Casbin을 사용할 것이다. 이렇게 하면 나중에 Casbin이 아닌 다른 권한 도구로 바꾸더라도 **internal** 패키지 외부의 코드는 수정할 필요가 없다. **internal/auth** 디렉터리부터 만들자.

``` bash
mkdir internal/auth
```

디렉터리에 `authorizer.go` 파일을 만들고 다음 코드를 추가하자.


[internal/auth/authorizer.go]
``` go
package auth

  

import (

"fmt"

  

"github.com/casbin/casbin"

"google.golang.org/grpc/codes"

"google.golang.org/grpc/status"

)

  

func New(model, policy string) *Authorizer {

enforcer := casbin.NewEnforcer(model, policy)

return &Authorizer{

enforcer: enforcer,

}

}

  

type Authorizer struct {

enforcer *casbin.Enforcer

}

  

func (a *Authorizer) Authorize(subject, object, action string) error {

if !a.enforcer.Enforce(subject, object, action) {

msg := fmt.Sprintf("%s not permitted to %s to %s", subject, action, object)

  

st := status.New(codes.PermissionDenied, msg)

return st.Err()

  

}

return nil

}
```

Authorizer 구조체와 Authorizer() 메서드를 정의했는데, 이 메서드는 Casbin의 Enforce() 함수를 사용한다. Enforce() 함수는 특정한 주체가 특정 행위를 특정 대상에 할 수 있는지를 Casbin에 설정한 모델과 정책에 기반해서 확인하여 알려준다. New() 함수의 모델과 정책 매개변수는 이들을 정의한 파일의 경로이다. 모델을 정의하는 파일은 Casbin의 권한 판단 메커니즘을 설정하여 우리가 사용할 모델은 ACL이다. 정책의 경우 ACL 테이블을 담은 CSV 파일이다.

<mark style="background: #ADCCFFA6;">권한을 확인하려면 다른 권한이 허가된 여러 클라이언트가 필요하다.</mark> 따라서 여러 개의 클라이언트 인증서가 필요하다. 다른 권한을 가진 여러 클라이언트 요청을 받는 서버는 ACL에 정의된 규칙에 따라 허용할지를 결정한다. Makefile의 인증서 생성 부분을 수정하여 여러 개의 인증서를 생성하자. gencert 타깃의 클라이언트 인증서 부분을 다음과 같이 수정한다.


[SecureYourServices/Makefile]
``` makefile
# START: multi_client

cfssl gencert \

-ca=ca.pem \

-ca-key=ca-key.pem \

-config=test/ca-config.json \

-profile=client \

-cn="root" \

test/client-csr.json | cfssljson -bare root-client

  

cfssl gencert \

-ca=ca.pem \

-ca-key=ca-key.pem \

-config=test/ca-config.json \

-profile=client \

-cn="nobody" \

test/client-csr.json | cfssljson -bare nobody-client

# END: multi_client
```

`$ make gencert`를 실행하여 인증서들을 만들자.

이번에는 서버 테스트에 권한을 확인하는 테스트를 추가하자. 아직은 서버에 권한 관련 구현이 없으니 테스트에는 실패하겠지만, 구현하고 나면 성공할 것이다. 테스트가 성공한다는 것은 구현이 잘 되었다는 의미이다.


먼저 테스트의 클라이언트 설정 부분을 수정하여 클라이언트를 두 개 만들자. 권한 설정 테스트를 위해 사용할 것이다. `server_test.go` 클라이언트 설정 코드를 다음과 같이 변경한다.

[internal/server/server_test.go]
``` go
newClient := func(crtPath, keyPath string) (

*grpc.ClientConn,

api_v1.LogClient,

[]grpc.DialOption,

) {

tlsConfig, err := config.SetupTLSConfig(config.TLSConfig{

CertFile: crtPath,

KeyFile: keyPath,

CAFile: config.CAFile,

Server: false,

})

  

require.NoError(t, err)

tlsCreds := credentials.NewTLS(tlsConfig)

opts := []grpc.DialOption{grpc.WithTransportCredentials(tlsCreds)}

conn, err := grpc.NewClient(l.Addr().String(), opts...)

  

require.NoError(t, err)

  

client := api_v1.NewLogClient(conn)

return conn, client, opts

}

  

var rootConn *grpc.ClientConn

  

rootConn, rootClient, _ = newClient(

config.RootClientCertFile,

config.RootClientCertFile,

)

  

var nobodyConn *grpc.ClientConn

nobodyConn, nobodyClient, _ = newClient(

config.NobodyClientCertFile,

config.NobodyClientKeyFile,

)

```

Teardown() 함수 부분도 클라이언트 연결을 끊도록 수정하자.


[./internal/server/server_test.go]
``` go
return rootClient, nobodyClient, cfg, func() {

server.Stop()

rootConn.Close()

nobodyConn.Close()

l.Close()

  

}
```

이렇게 두 개의 클라이언트를 만들었다. `superuser` 클라이언트는 root라고도 부르며, 생산과 소비를 할 수 있다. `nobody` 클라이언트는 아무런 권한이 없다. 두 클라이언트를 생성하는 코드 자체는 인증서와 키를 제외하고는 다르지 않으므로 클라이언트 생성코드를 별도의 도우미 함수의 `newClient(crtPAth, keyPath string)`로 리팩토링했다. 서버는 이제 Authorizer 인스턴스를 받는데 서버의 권한 로직을 맡는다. root와 nobody 클라이언트를 테스트 함수에 전달하면 권한이 있거나 없는 클라이언트로 서버를 테스트할 때 사용한다. 마지막 수정사항은 테스트 코드 역시 수정해야 한다. 다음과 같이 수정해보자.


[./internal/server/server_test.go]

``` go
func TestServer(t *testing.T) {

for scenario, fn := range map[string]func(

t *testing.T,

rootClient api_v1.LogClient,

nobodyClient api_v1.LogClient,

config *Config,

){

"produce/consume a message to/from the log succeeds": testProduceConsume,

"produce/consume stream succeeds": testProduceConsumeStream,

"consume past log boundary fails": testConsumePastBoundary,

} {

t.Run(scenario, func(t *testing.T) {

rootClient, nobodyClient, config, teardown := setupTest(t, nil)

defer teardown()

fn(t, rootClient, nobodyClient, config)

})

}

}
```

두 번째 클라이언트를 처리하도록 테스트를 수정해야 한다. 테스트 함수의 매개변수를 다음과 같이 변경한다.

```
t *testing.T, client _ api_v1.LogClient, cfg *Config
```

nobody클라이언트의 인증서와 키의 위치, 그리고 Casbin에 사용할 설정 파일의 위치를 저장할 변수들도 추가하자. `internal/config/files.go` 파일에 다음과 같이 추가한다.


```go
var (

CAFile = configFile("ca.pem")

ServerCertFile = configFile("server.pem")

ServerKeyFile = configFile("server-key.pem")

RootClientCertFile = configFile("root-client.pem")

RootClientKeyFile = configFile("root-client-key.pem")

NobodyClientCertFile = configFile("nobody-client.pem")

NobodyClientKeyFile = configFile("nobody-client-key.pem")

ACLModelFile = configFile("model.conf")

ACLPolicyFile = configFile("policy.csv")

)
```

ACL 정책은 명확히 정의되었고 테스트 전반에 사용되므로 Casbin 설정을 test 디렉터리에 넣어주자. `test/model.conf` 파일을 만들고 다음 설정을 넣는다.

``` conf
# 요청 정의

  

[request_definition]

r=sub,obj,act

  

# 정책 정의

[policy_definition]

p=sub,obj,act

  

# 정책 효과

[policy_effect]

e=some(where(p.eft==allow))

  

# 매칭

[matcher]

m=r.sub==p.sub && r.obj==p.obj && r.act==p.act
```

이 설정은 Casbin이 ACL을 권한 판단 메커니즘으로 사용하게 한다.

`model.conf` 파일과 함께 `policy.csv` 파일을 다음 내용을 담아 추가한다.

[./test/policy.scv]
``` csv
p, root, *, produce
p, root, *, consume

```

이것이 ACL 테이블이다. 두 항목은 root 클라이언트가 모든(`*`) 대상에 대해 생산과 소비 권한이 있다는 의미이다. nobody를 포함한 다른 모든 클라이언트는 거부된다.

이제 정책과 모델 파일을 **CONFIG_PATH**에  저장해서 테스트에서 찾을 수 있게 하자. Makefile의 test 타깃을 다음과 같이 수정하자.


[./Makefile]
``` makefile

$(CONFIG_PATH)/model.conf:

cp test/model.conf $(CONFIG_PATH)/model.conf

$(CONFIG_PATH)/policy.csv:

cp test/policy.csv $(CONFIG_PATH)/policy.scv

  

# START: begin

.PHONY: test

# END: auth

  

test:

# END: begin

# START:auth

test: $(CONFIG_PATH)/policy.csv $(CONFIG_PATH)/model.conf
```

다시 테스트할 수 있게 되었다. `$make test`로 테스트하면 통과한다(하지만 실제로는 되지 않는다). 현재 테스트는 생산과 소비 권한이 있는 root 클라이언트만 사용하기도 하고, 클라이언트는 권한이 있다고 가정하기 때문이다.

권한이 없는 클라이언트는 거부하는지 확인하는 테스트를 추가해보자. `server_test.go`에 다음 패키지들을 추가해보자.

[/internal/server/server_test.go]
```go
"google.golang.org/grpc/codes"
"google.golang.org/grpc/status"
```

testProduceConsumeStream() 테스트 아래에 testUnauthorized() 테스트를 추가하자.

[/internal/server/server_test.go]
```go
func testUnauthorized(

t *testing.T,

_,

client api_v1.LogClient,

config *Config,

) {

ctx := context.Background()

produce, err := client.Produce(

ctx,

&api_v1.ProduceRequest{

Record: &api_v1.Record{

Value: []byte("hello world"),

},

},

)

  

if produce != nil {

t.Fatalf("produce response should be nil")

}

  

gotCode, wantCode := status.Code(err), codes.PermissionDenied

if gotCode != wantCode {

t.Fatalf("got code: %d, want: %d", gotCode, wantCode)

}

consume, err := client.Consume(ctx, &api_v1.ConsumeRequest{

Offset: 0,

})

if consume != nil {

t.Fatalf("consume response should be nil")

}

gotCode, wantCode = status.Code(err), codes.PermissionDenied

if gotCode != wantCode {

t.Fatalf("got code: %d, want:%d", gotCode, wantCode)

}

  

}
```

테스트에서는 nobody 클라이언트를 사용한다. 아무런 권한이 없는 nobody 클라이언트로 생산과 소비를 시도하므로 서버는 거부한다. 리턴 에러를 보고 서버가 거부한 것을 확인할 수 있다.

테스트 테이블의 `TestServer(t *testing.T)` 테스트에 권한이 없을 때의 테스트를 다음 코드의 강조한 부분처럼 한 줄 추가하자.

[internal/server/server_test.go]
``` go
"produce/consume a message to/from the log succeeds": testProduceConsume,

"produce/consume stream succeeds": testProduceConsumeStream,

"consume past log boundary fails": testConsumePastBoundary,

"unauthorized fails": testUnauthorized,
```


**make test**를 해 보면 테스트는 실패한다. 서버가 아직 권한 관련 구현을 하지 않았기에 에러가 나는 것이다. 서버에 권한 관련 코드를 추가하자.

**server.go** 파일의 **Config**와 패키지 임포트 부분을 수정하자.


[internal/server/server.go]

``` go

```

Config 구조체에 Authorizer 필드를 추가하고, 권한에 사용할 상수들도 추가하자.

[/internal/server/server.go]
```go
const(

objectWildcard="*"

produceAction="produce"

consumeAction="consume"

)

  

type Config struct {

CommitLog CommitLog

Authorizer Authorizer

}
```

상수들은 ACL 정책 테이블 값과 매칭된다. 이 파일에서 여러 번 참조할 것이기에 상수로 만들었다. Config의 Authorizer 필드는 인터페이스다. 다음과 같이 정의하자.

[internal/server/server.go]
```go
type Authorizer interface{

Authorize(subject,object,action string) error

}
```

Authorizer 인터페이스에 의존성을 가지므로 권한 구현 부분은 언제든지 바꿀 수 있다. 4.6절에서 다룬 CommitLog와 같다. Produce() 메서드에 다음과 같이 코드를 추가해서 수정하자.


[/internal/server/server.go]
```go

  

func (s *grpcServer) Produce(ctx context.Context, req *api_v1.ProduceRequest) (*api_v1.ProduceResponse, error) {

if err := s.Authorizer.Authorize(

subject(ctx), objectWildcard, produceAction,

); err != nil {

return nil, err

}

  

offset, err := s.CommitLog.Append(req.Record)

if err != nil {
return nil, err
}

return &api_v1.ProduceResponse{Offset: offset}, nil

  

}
```


`Consume()` 메서드도 마찬가지로 수정한다.

[internal/server/server.go]
```go
func (s* grpcServer)Consume(ctx context.Context,req *api_v1.ConsumeRequest) (*api_v1.ConsumeResponse,error) {

if err :=s.Authorizer.Authorize(

subject(ctx),

objectWildcard,

consumeAction,

); err!=nil{

return nil,err

}

  

record,err:=s.CommitLog.Read(req.Offset)

if err != nil {

return nil, err

}

return &api_v1.ConsumeResponse{Record: record},nil

}
```

이제 서버는 클라이언트는 인증서의 주체(subject)로 인증하여, 생산과 소비 권한이 있는지를 확인한다. 만약 권한이 없다면 허가가 거부되었다는 에러를 클라이언트에 회신한다. 생산을 요청하는 클라이언트가 권한이 있다면 메서드는 레코드를 로그에 추가할 것이다. 소비 요청 시 클라이언트가 권한이 있다면 로그에서 레코드를 소비할 것이다. 클라이언트 인증서에서 주체를 얻어내려면 두 개의 도우미 함수가 필요하다. `server.go` 파일에 다음 코드를 추가하자.

```go
  

func authenticate(ctx context.Context) (context.Context, error) {

peer, ok := peer.FromContext(ctx)

if !ok {

return ctx, status.New(

codes.Unknown,

"couldn't find peer info",

).Err()

}

  

if peer.AuthInfo == nil {

return context.WithValue(ctx, subjectContextKey{}, ""), nil

}

tlsInfo := peer.AuthInfo.(credentials.TLSInfo)

subject := tlsInfo.State.VerifiedChains[0][0].Subject.CommonName

ctx = context.WithValue(ctx, subjectContextKey{}, subject)

  

return ctx, nil

}

  

func subject(ctx context.Context) string {

return ctx.Value(subjectContextKey{}).(string)

}

  

type subjectContextKey struct{}
```

`authenticate(ctx context.Context)` 함수는 클라이언트 인증서에서 주체를 읽어서 RPC의 콘텍스트(context)에 쓴다. 이러한 함수를 인터셉터(interceptor)라 하는데, 각각의 RPC 호출을 가로채고 변경해서 요청 처리를 작고 재사용 가능한 단위로 나눈다. 다른 프레임워크에서는 이러한 개념을 미들웨어(middleware)라 부르기도 한다. `subject(ctx context.Context)`함수는 클라이언트 인증서의 주체를 리턴하여 클라이언트를 인식하고 접근 가능 여부를 확인한다.

`NewGRPCServer(config *Config,opts ...grpc.ServerOption)`함수를 다음과 같이 수정하자.

[/internal/server/server.go]
```go
func NewGRPCServer(config *Config, opts ...grpc.ServerOption) (*grpc.Server, error) {

  

opts = append(opts, grpc.StreamInterceptor(grpc_middleware.ChainStreamServer(grpc_auth.StreamServerInterceptor(authenticate))),

grpc.UnaryInterceptor(grpc_middleware.ChainUnaryServer(grpc_auth.UnaryServerInterceptor(authenticate))))

gsrv := grpc.NewServer(opts...)

srv, err := newgrpcServer(config)

if err != nil {

return nil, err

}

api_v1.RegisterLogServer(gsrv, srv)

return gsrv, nil

}
```

`authenticate()` 인터셉터를 gRPC 서버에 연결해서 서버가 각각의 RPC의 주체를 확인하고 권한을 확인하게 했다.

테스트 서버의 설정을 변경해서 `authorizer`를 전달하게 하자. `setup_test.go`의 `setuptest` 테스트에서 `auth` 패키지를 임포트하고 서버의 설정 부분을 다음과 같이 수정하자.

[internal/server/server_test.go]
```
authorizer := auth.New(config.ACLModelFile, config.ACLPolicyFile)

cfg = &Config{

CommitLog: clog,

Authorizer: authorizer,

}

```