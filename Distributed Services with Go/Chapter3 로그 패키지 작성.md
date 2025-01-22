

우리는 분산 서비스를 구축하는 법을 go 언어로 만들며 배우는 중이다. 그런데 왜 로그 패키지를 작성하는 것일까? 필자는 로그가 분산 서비스를 만드는 가장 중요한 도구라고 생각한다. **미리 쓰기 로그**(로그 선행 기입,write-ahead log, WAL), **트랜잭션 로그**(transaction log), **커밋 로그**(commit log) 등으로 부르는 로그는 스토리지 엔진(storage engine), 메시지 큐, 버전 컨트롤, 복제(replication)와 합의 알고리즘(consensus algorithm)의 핵심이다. 분산 시스템을 만들며 마주치는 많은 문제는 로그로 해결할 수 있다. 직접 로그를 만들어보면 다음과 같은 내용을 배울 수 있다.



- **로그**를 이용해 문제를 해결하거나, 어려운 문제를 좀 더 쉽게 만드는 방법
- 기존의 로그 기반 시스템을 변경하거나, 새로운 로그 기반 시스템을 만드는 방법
- 데이터를 효율적으로 읽고 쓰는 스토리지 엔진을 만드는 방법
- 시스템 오류에도 데이터 손실을 막는 방법
- 데이터를 디스크에 저장하기 위해 인코딩하거나 자신만의 와이어 프로토콜(wire protocol)을 만들고 애플리케이션 간에 전송하는 방법


차세대 분산 로그 서비스를 만들어내는 주인공은 여러분이 될 수도 있다.


---
# 3.1 로그는 강력한 도구

파일 시스템이나 데이터베이스의 스토리지 엔진 개발자들은 시스템의 무결성을 높이고자 로그를 사용한다. ==`ext` 파일 시스템을 예로 들면, 로그는 직접적으로 디스크의 데이터 파일을 바꾸기보다는 저널(journal)을 변경한다.== 파일 시스템이 저널에 로그를 안전하게 쓰고 나면, 이 변경사항을 데이터 파일에 적용한다. 저널에 로그를 쓰는 건 단순하면서도 빠르므로 데이터를 잃을 가능성은 적다. ==ext가 디스크의 파일을 업데이트하지 못하고 컴퓨터가 꺼지더라도 다음번 부팅에서 파일 시스템이 다시 저널을 보고 파일 업데이트를 완료한다.== PostgreSQL과 같은 데이터베이스의 개발자들 역시 이런 기법을 사용해서 시스템을 견고하게 만든다. 레코드가 변경되면 WAL에 우선 적은 다음, WAL을 데이터베이스의 데이터 파일에 반영한다.

==WAL을 복제에도 사용한다. 로그를 디스크에 쓰는 대신 네트워 크 너머의 복제본(replica)에 쓰는 것이다.== 복제본은 이를 다시 데이터 카피에 반영하고, 둘은 같은 상태가 된다. 래프트(Raft)와 같은 분산 합의(consensus) 알고리즘은 이러한 아이디어를 분산 서비스가 클러스터 전반의 상태에 대해 합의할 때 사용한다. 래프트의 노드(node)들은 로그를 입력으로 하는 상태 머신(상태 기계,state machine)을 가진다. 클러스터의 리더는 변경사항을 팔로워들의 로그로 확장한다. 상태 머신이 로그를 입력으로 받으므로 같은 레코드가 같은 순서로 로그에 쌓이고, 모든 서비스는 같은 상태가 된다.

웹 프런트엔드 개발자들은 애플리케이션의 상태를 관리하고자 로그를 사용한다. 리액트(react)와 함께 많이 사용하는 리덕스(Redux) 자바스크립트 라이브러리는 변경사항을 객체(Object)로 로그에 저장하고, 이러한 변경사항을 순수 함수(pure function)로 처리하여 애플리케이션의 상태를 업데이트한다.

이러한 예시에서 보듯이 <mark style="background: #ADCCFFA6;">로그는 순서가 있는 데이터를 저장, 공유, 처리할 때 사용한다.</mark> 하나의 도구로 데이터베이스를 복제하고, 분산 서비스를 조율하며, 프런트엔드 애플리케이션의 상태를 관리할 수 있는 것이다. 시스템의 변경사항을 (분리할 수 없는) 한 단위의 연산까지 쪼개고 나눈 다음에 로그 저장, 공유, 처리할 수 있다면 분산 서비스에서의 문제를 비롯한 많은 문제를 해결할 수 있다.

==대부분의 데이터베이스는 상태를 과거의 특정 시점으로 복원하는, 특정 시점 복구(point-in-time recovery, PITR)라는 기능을 제공한다.== <mark style="background: #ADCCFFA6;">언제든 데이터베이스의 스냅숏을 찍어두었다가, 필요할 때 **WAL**을 스냅숏을 찍은 시점까지 실행한다.</mark> <mark style="background: #FF5582A6;">만약 원하는 시점까지의 모든 로그를 보관한다면 스냅숏을 찍을 필요도 없지만, 데이터베이스의 히스토리가 오래되고 변경사항이 많다면 쉽지 않을 것이다.</mark> 리덕스는 같은 발상 `undo/redo` 액션에 사용한다. 액션마다 애플리케이션의 상태를 로그로 남겼다가, 액션의 실행을 취소하려면 리덕스에 UI의 상태를 로그에 저장된 이전 상태로 되돌리라고 하는 것이다. 깃(Git)과 같은 분산 버전 관리 시스템도 비슷하다. 커밋 로그 히스토리는 말 그대로 커밋 로그를 말한다.

완벽한 로그는 마지막 상태를 포함한, 존재했던 모든 상태를 가지며, 이러한 로그만 있으면 복잡해 보이던 기능도 얼마든지 만들 수 있다. 단순하다는 게 로그의 좋은 점이다.


---
# 3.2 로그의 작동 원리

로그는 추가만 할 수 있는 레코드의 연속이다. 레코드는 로그의 끝에 추가하며, 보통은 최근의 로그들을 오래된 레코드부터 읽는다. 파일을 읽을 때 쓰는 `tail -f` 명령과 비슷하다. 어떠한 데이터라도 로그를 저장할 수 있다. 우리는 로그라는 표현을 사람이 읽을 문자열이라는 의미로 써왔다. ==하지만 로그 시스템 사용자가 많아지면서 다른 프로그램이 읽을 수 있는, 바이너리로 인코딩된 메시지라는 의미로 바뀌었다.== 이 책에서 사용하는 로그 또는 레코드라는 표현은 특정 자료형의 데이터를 의미하지 않는다. 로그에 레코드를 추가하면, 로그는 레코드에 고유하면서 순차적인 오프셋 숫자를 할당하는데, <mark style="background: #ADCCFFA6;">이는 레코드의 ID와 같다. 로그는 레코드의 오프셋과 생성 시간으로 정렬된 데이터베이스 테이블과 같다.
</mark>

로그는 실제로 구현하며 마주치는 첫 번째 문제는 무한한 용량의 디스크는 없다는 점이다. 파일 하나에 끝없이 추가할 수는 없으므로 로그를 여러 개의 **세그먼트**(segment)로 나눈다. 로그가 지나치게 커지면, 이미 필요한 처리를 마쳤거나 다른 공간에 별도로 보관한 오래된 세그먼트부터 지우면서 디스크 용량을 확보한다. 이러한 작업을 백그라운드로 처리하면서 서비스는 정상 작동하게 할 수도 있다. 그러면 서비스는 계속 새로운 세그먼트를 만들어내기도 하고 다른 세그먼트로부터 데이터를 소비하기도 한다. 이때 고루틴(goroutine)들이 같은 데이터에 접근하더라도 충돌이 거의 발생하지 않는다.

세그먼트 목록에는 항상 하나의 **활성 세그먼트**(active segment)가 있다. 유일하게 쓸 수 있는 세그먼트로, 활성 세그먼트가 가득 차면 새로운 세그먼트를 생성하여 활성 세그먼트로 만든다.

세그먼트는 **저장 파일**(store file)과 **인덱스 파일**(index file)로 구성된다. ==저장 파일은 레코드 데이터를 저장하고 추가해 나가며, 인덱스 파일은 그 레코드들의 인덱스를 저장한다.== ==인덱스 파일은 레코드의 오프셋을 저장 파일의 실제 위치로 매핑해서 빠르게 읽을 수 있도록 한다. ==특정 <mark style="background: #FF5582A6;">오프셋의 레코드를 읽으려면 먼저 인덱스 파일에서 원하는 레코드의 저장 파일에서의 위치를 알아내고, 저장 파일에서 해당 위치의 레코드를 읽는다.</mark> 인덱스 파일은 오프셋과 저장 파일에서의 위치라는 두 필드만을 가지므로 저장소 파일보다 훨씬 작다. 따라서 메모리 맵 파일(memory-mapped file)로 만들어 파일 연산이 아닌 메모리 데이터를 다루듯 빠르게 만들 수 있다.

로그의 작동 원리를 알아보았으니 이제 로그를 만들어보자.

---
# 3.3 로그 만들기

저장 파일과 인덱스 파일부터 하나씩 차근차근 만들고 세그먼트를 만든 다음 마지막으로 로그를 만들자. 만들어나가는 단계별로 테스트도 작성하자. **로그**(log)는 레코드, 레코드 저장 파일, 세그먼트라는 추상적인 데이터 자료형을 아우르는 표현이기에 다음과 같이 용어들을 정리한다.

- **레코드**: 로그에 저장한 데이터
- **저장 파일**: 레코드를 저장하는 파일
- **인덱스 파일**: 인덱스를 저장하는 파일
- **세그먼트**: 저장 파일과 인덱스 파일을 묶어서 말하는 추상적 개념
- **로그**: 모든 세그먼트를 묶어서 말하는 추상적 개념


#### 3.3.1 스토어 만들기

로그 패키지를 위한한 **internal/log** 디렉터리를 만들고 **store.go** 파일을 생성하여 다음 코드를 넣어 주자.

``` go

// WriteALogPackage/internal/log/store.go
package log

import (
	"bufio"
	"encoding/binary"
	"os"
	"sync"
)

var (
	enc = binary.BigEndian
)

const (
	lenWidth = 8
)

type store struct {
	*os.File
	mu   sync.Mutex
	buf  *bufio.Writer
	size uint64
}

func newStore(f *os.File) (*store, error) {
	fi, err := os.Stat(f.Name())
	if err != nil {
		return nil, err
	}
	size := uint64(fi.Size())
	return &store{
		File: f,
		size: size,
		buf:  bufio.NewWriter(f),
	}, nil
}


```

store 구조체는 파일의 단순한 래퍼(wrapper)이며 파일에 값들을 추가하거나 읽는 두 개의 메서드를 가진다. `newStore(f *os.File)` 함수는 해당 파일의 스토어를 생성한다. 이 함수는 다시 `os.Stat()` 함수를 호출해 파일 크기를 알아두었다가 데이터가 있는 파일로 스토어를 생성할 때 사용한다(예를 들면 서비스를 재시작할 때 필요하다).


스토어에서는 변수인 enc와 상수인 lenWidth를 여러 번 참조하므로 쉽게 찾을 수 있도록 코드 위쪽에 두었다. **enc**는 레코드 크기와 인덱스 항목을 저장할 때의 인코딩을 정의한 것이며, lenWidth는 레코드 길이를 저장하는 바이트 개수를 정의한 것이다.

`newStore()` 함수 아래에 `Append()` 메서드를 추가하자.

``` go
// WriteALogPackage/internal/log/store.go

func (s *store) Append(p []byte) (n uint64, pos uint64, err error) {
	s.mu.Lock()
	defer s.mu.Unlock()
	pos = s.size
	if err := binary.Write(s.buf, enc, uint64(len(p))); err != nil {
		return 0, 0, err
	}
	w, err := s.buf.Write(p)
	if err != nil {
		return 0, 0, err
	}
	w += lenWidth

	s.size += uint64(w)
	return uint64(w), pos, nil
}

```

<mark style="background: #ADCCFFA6;">`Append(p []byte) `메서드는 바이트 슬라이스(byte slice)를 받아서 저장 파일에 쓴다. 나중에 읽을 때 얼마나 읽어야 할지 알 수 있도록 레코드 크기도 쓴다.</mark> 저장 파일에 직접 쓰지는 않고 버퍼를 거쳐 저장하는데, 이는 시스템 호출 횟수를 줄여 성능을 개선한다. 작은 레코드를 많이 쓰는 작업이라면 더욱 효과를 볼 수 있다. 실제로 쓴 바이트 수를 리턴할 때 GO API에서 이런 방식을 많이 쓴다. 저장 파일의 어느 위치에 썼는지도 리턴하는데, 세그먼트는 레코드의 인덱스 항목을 생성할 때 이 위치 정보를 사용한다.


`Append()` 메서드 아래에 `Read()` 메서드를 추가하자.

``` go
// WriteALogPackage/internal/log/store.go
func (s *store) Read(pos uint64) ([]byte, error) {
	s.mu.Lock()
	defer s.mu.Unlock()
	if err := s.buf.Flush(); err != nil {
		return nil, err
	}

	size := make([]byte, lenWidth)
	if _, err := s.File.ReadAt(size, int64(pos)); err != nil {
		return nil, err
	}

	b := make([]byte, enc.Uint64(size))
	if _, err := s.File.ReadAt(b, int64(pos+lenWidth)); err != nil {
		return nil, err
	}
	return b, nil
}

```

<mark style="background: #ADCCFFA6;">`Read(pos uint64)`메서드는 해당 위치에 저장된 레코드를 리턴한다.</mark> 읽으려는 레코드가 아직 버퍼에 있을 때를 대비해서 우선은 쓰기 버퍼의 내용을 **플러시**(flush)해서 디스크에 쓴다. 다음으로 읽을 레코드의 바이트 크기를 알아내고 그만큼의 바이트를 읽어 리턴한다. 함수 내에서 할당하는 메모리가 함수 바깥에서 쓰이지 않으면, 컴파일러는 그 메모리를 스택(stack)할당한다. 반대로 함수가 종료해도 함수 외부에서 계속 쓰이는 값이면 힙(heap)에 할당한다.

`Read()` 메서드 아래에 `ReadAt()` 메서드를 추가하자.

``` go
// WriteALogPackage/internal/log/store.go
func (s *store) ReadAt(p []byte, off int64) (int, error) {
	s.mu.Lock()
	defer s.mu.Unlock()
	if err := s.buf.Flush(); err != nil {
		return 0, err
	}
	return s.File.ReadAt(p, off)
}

```

==`ReadAt(p byte[],off int64)` 메서드는 스토어 파일에서 off 오프셋부터 `len(p)` 바이트만큼 p에 넣어준다.== 이 메서드는 `io.ReaderAt` 인터페이스를 store 자료형에 구현한 것이다.


마지막으로 `Close()` 메서드를 추가하자.

```go

// WriteALogPackage/internal/log/store.go


func (s *store) Close() error {
	s.mu.Lock()
	defer s.mu.Unlock()
	if err := s.buf.Flush(); err != nil {
		return err
	}
	return s.File.Close()
}

```

`Close()` 메서드는 파일을 닫기 전 버퍼의 데이터를 파일에 쓴다.

이제 구현한 스토어를 테스트해보자. log 디렉터리에 store_test.go 파일을 만들고 다음 코드를 넣자.

``` go
package log

import (
	"os"
	"testing"

	"github.com/stretchr/testify/require"
)

var (
	write = []byte("hello world")
	width = uint64(len(write)) + lenWidth
)

func TestStoreAppendRead(t *testing.T) {
	f, err := os.CreateTemp("", "store_append_read_test")
	require.NoError(t, err)
	defer os.Remove(f.Name())

	s, err := newStore(f)
	require.NoError(t, err)

	testAppend(t, s)
	testRead(t, s)
	testReadAt(t, s)

	s, err = newStore(f)
	require.NoError(t, err)
	testRead(t, s)
}

// 테스트에서는 임시 파일로 스토어를 생성하고 두 개의 테스트 도우미(helper) 함수를 호출해 스토어에
// 추가한 뒤 읽어본다. 그리고 임시 파일에서 다시 스토어를 생성해서 읽고, 서비스가 재시작하고 상태가
// 복원되는지 테스트한다.
```

테스트에서는 임시 파일로 스토어를 생성하고 두 개의 테스트 도우미(helper) 함수를 호출해 스토어에 추가한 뒤 읽어본다. 그리고 임시 파일에서 다시 스토어를 생성해서 읽고, 서비스가 재시작하고 상태가 복원되는지를 테스트한다.

다음 도우미 함수를 테스트 코드에 추가하자.

``` go
func testAppend(t *testing.T, s *store) {
	t.Helper()
	for i := uint64(1); i < 4; i++ {
		n, pos, err := s.Append(write)
		require.NoError(t, err)
		require.Equal(t, pos+n, width*i)
	}
}

func testRead(t *testing.T, s *store) {
	t.Helper()
	var pos uint64
	for i := uint64(1); i < 4; i++ {
		read, err := s.Read(pos)
		require.NoError(t, err)
		require.Equal(t, write, read)
		pos += width
	}
}

func testReadAt(t *testing.T, s *store) {
	t.Helper()
	for i, off := uint64(1), int64(0); i < 4; i++ {
		b := make([]byte, lenWidth)
		n, err := s.ReadAt(b, off)
		require.NoError(t, err)
		require.Equal(t, lenWidth, n)
		off += int64(n)

		size := enc.Uint64(b)
		b = make([]byte, size)
		n, err = s.ReadAt(b, off)
		require.NoError(t, err)
		require.Equal(t, write, b)
		require.Equal(t, int(size), n)
		off += int64(n)
	}
}

```

그리고 아래쪽에 `Close()` 메서드를 테스트하는 다음 코드까지 추가하자.

``` go

// WriteALogPackage/internal/log/store_test.go
func TestStoreClose(t *testing.T) {

f, err := os.CreateTemp("", "store_close_test")

require.NoError(t, err)

defer os.Remove(f.Name())

s, err := newStore(f)

require.NoError(t, err)

_, _, err = s.Append(write)

require.NoError(t, err)

  

_, beforeSize, err := openFile(f.Name())

require.NoError(t, err)

  

err = s.Close()

require.NoError(t, err)

  

_, afterSize, err := openFile(f.Name())

require.NoError(t, err)

require.True(t, afterSize > beforeSize)

t.Logf("beforeSize %d, afterSize %d", beforeSize, afterSize)

}

func openFile(name string) (file *os.File, size int64, err error) {

f, err := os.OpenFile(name, os.O_RDWR|os.O_CREATE|os.O_APPEND,

0644,

)

  

if err != nil {

return nil, 0, err

}

  

fi, err := f.Stat()

if err != nil {

return nil, 0, err

}

  

return f, fi.Size(), nil

  

}

```


#### 3.3.2 인덱스 만들기

이제 인덱스를 만들어보자. internal/log 디렉터리에 index.go 파일을 만들고 다음 코드를 넣자.

```go
// WriteALogPackage/internal/index.go

package log

  

import (
"io"
"os"


"github.com/tysonmote/gommap"

)

  

var (
offWidth uint64 = 4
posWidth uint64 = 8
entWidth = offWidth + posWidth
)

  

type index struct {
file *os.File
mmap gommap.MMap
size uint64
}
```

offWidth, posWidth, entWidth 상수들은 인덱스 코드 내에서 많이 사용한다. 스토어에서 변수와 상수들처럼 index.go 파일 위쪽에 두어서 찾기 쉽게 했다. ==이들은 인덱스 항목 내의 바이트 수를 정의한 것이다.==

인덱스 항목은 **'레코드 오프셋'** 과 **'스토어 파일에서의 위치'** 라는 두 필드로 구성된다. 오프셋은 uint32 자료형, 위치는 uint64 자료형을 사용하기에 각각 4바이트와 8바이트를 차지한다. entWidth는 오프셋이 가리키는 위치를 계산(`offset*entWith`)할 때 사용한다.

index는 인덱스를 정의하며 파일과 메모리 맵 파일로 구성된다. size는 인덱스의 크기로, 인덱스에 다음 항목을 추가할 위치를 의미하기도 한다.

newIndex() 함수를 추가하자.

``` go

// WriteALogPackage/internal/log/index.go

func newIndex(f *os.File, c Config) (*index, error) {

idx := &index{
file: f,
}

fi, err := os.Stat(f.Name())

if err != nil {
return nil, err
}

idx.size = uint64(fi.Size())
if err = os.Truncate(f.Name(), int64(c.Segment.MaxIndexBytes)); err != nil {
return nil, err
}

  

if idx.mmap, err = gommap.Map(
idx.file.Fd(),
gommap.PROT_READ|gommap.PROT_WRITE,
gommap.MAP_SHARED,
); err != nil {

return nil, err

}

  

return idx, nil

}
```

<mark style="background: #ADCCFFA6;">`newIndex(f *os.File)` 함수는 해당 파일을 위한 인덱스를 생성한다.</mark> 여기서는 인덱스와 함께 파일의 현재  크기를 저장하는데, <mark style="background: #ADCCFFA6;">인덱스 항목을 추가하며 인덱스 파일의 데이터양을 추적하려는 것이다.</mark>

인덱스 파일은 먼저 최대 인덱스 크기로 바꾼 다음에 메모리 맵 파일을 만들어주며, 생성한 인덱스를 리턴한다.

Close()메서드를 이어서 추가하자.

```go
// WriteALogPackage/internal/log/index.go

func (i *index) Close() error {

if err := i.mmap.Sync(gommap.MS_ASYNC); err != nil {
return err
}

if err := i.file.Sync(); err != nil {
return err
}

if err := i.file.Truncate(int64(i.size)); err != nil {
return err
}

  

return i.file.Close()

}
```

==**Close()** 를 실행하면 메모리 맵 파일과 실제 파일의 데이터가 확실히 동기화되며, 실제 파일 콘텐츠가 안정적인 저장소에 **플러시**된다.== 그런 다음 실제 데이터가 있는 만큼만 잘라내고(truncate, '생략하다'라는 의미)파일을 닫는다.

인덱스를 여닫는 코드를 살펴보았다. 이제 파일을 크게 만들고 잘라낸다는 게 무슨 뜻인지 알아보자.

<mark style="background: #FF5582A6;">서비스를 시작하면, 서비스는 다음 레코드를 로그의 어디에 추가할지 오프셋을 알아야 한다.</mark> 마지막 항목의 인덱스를 찾아보면 다음 레코드의 오프셋을 알 수 있다. 인덱스 파일의 마지막 12바이트를 읽으면 된다. ==하지만 메모리 맵 파일을 사용하기 위해 파일을 최대 크기로 늘리면 이 방법을 사용할 수 없다(메모리 맵 파일은 생성한 다음 크기를 바꿀 수 없기에 미리 필요한 크기로 만들어야 한다).== 파일에 빈 공간을 추가해두므로 파일의 가장 끝부분이 마지막 항목이 아닌 것이다. 빈 공간을 그대로 두면 서비스를 정상적으로 재시작할 수 없다. <mark style="background: #FF5582A6;">따라서 인덱스 파일을 실제 데이터가 있는 부분만 잘라내, 마지막 인덱스 항목이 파일의 끝부분에 있도록 만든 다음 서비스를 종료시킨다</mark>. 이렇게 정상적으로 종료(graceful shutdown)해야 서비스를 적절하고 효율적으로 재시작할 수 있다.

>[!caution] 비정상 종료(ungraceful shutdown) 처리
>서비스가 진행했던 일들을 잘 마무리하고, 데이터 손실이 없도록 하고, 재시작을 준비하면 정상 종료된다. 서비스 충돌이나 하드웨어 오류로 이러한 작업을 하지 못하고 종료할 때가 비정상 종료이다. 예를 들어 인덱스 파일을 잘라내는 작업 도중에 전원이 끊길 수 있다. 이러한 경우를 고려해 서비스를 재시작하면 **온전성 검사**(sanity check)를 한다. 손상된 데이터가 있다면 데이터를 다시 생성하거나, 손상되지 않는 소스에서 복제한다. 이 책에서는 코드가 너무 복잡해지지 않도록 비정상 종료는 구현하지 않는다.

코드 구현으로 되돌아가서 다음 Read() 메서드를 추가하자.

``` go
// WriteALogPackage/internal/log/index.go
  

func (i *index) Read(in int64) (out uint32, pos uint64, err error) {

if i.size == 0 {
return 0, 0, io.EOF
}

if in == -1 {
out = uint32((i.size / entWidth) - 1)
} else {
out = uint32(in)
}

pos = uint64(out) * entWidth

if i.size < pos+entWidth {
return 0, 0, io.EOF
}

out = enc.Uint32(i.mmap[pos : pos+offWidth])
pos = enc.Uint64(i.mmap[pos+offWidth : pos+entWidth])
return out, pos, nil

}
```

==`Read(in int64)`메서드는 매개변수로 오프셋을 받아서, 해당하는 레코드의 저장 파일 내 위치를 리턴한다.== 여기에서 오프셋은 해당 세그먼트의 베이스 오프셋(base offset)의 상댓값이다. 인덱스 첫 항목의 오프셋은 항상 0이며 그다음 1씩 증가하다. 여기서는 상대 오프셋(relative offset)을 사용해서 uint32만으로 표현하여 크기를 줄였다. 절대 오프셋(absolute offset)을 사용한다면 uint64를 사용해야 하므로 항목마다 4바이트가 더 필요하다. 4바이트 정도야 싶겠지만, 분산 서비스에서 사람들이 사용하는 레코드양은 엄청나게 많아서 링크드인(Linkedin)같은 경우는 매일 조 단위의 레코드를 만들어낸다. 상대적으로 작은 회사도 수십억 개의 레코드를 만들어낸다.

이어서 Write() 메서드를 추가하자.

``` go

// WriteALogPackage/internal/log/index.go

func (i *index) Write(off uint32, pos uint64) error {
if uint64(len(i.mmap)) < i.size+entWidth {
return io.EOF
}

enc.PutUint32(i.mmap[i.size:i.size+offWidth], off)
enc.PutUint64(i.mmap[i.size+offWidth:i.size+entWidth], pos)

  

i.size += uint64(entWidth)

return nil

}
```

`Write(off uint32,pos uint32)` 메서드는 ==오프셋과 위치를 매개변수로 받아서 인덱스에 추가한다. 먼저 추가할 공간이 있는지 확인하고, 공간이 있다면 인코딩한 다음 메모리 맵 파일에 쓴다.== 마지막으로 size를 증가시켜 다음에 쓸 위치를 가리키게 한다.

다음 Name() 메서드는 인덱스 파일의 경로를 리턴한다.

``` go
func (i *index) Name() string {

return i.file.Name()

} 
```

구현한 인덱스를 테스트해보자. `internal/log` 디렉터리에 `index_test.go` 파일을 생성하고 다음 코드부터 추가한다.

``` go
package log

  

import (

"os"

"testing"

  

"github.com/stretchr/testify/require"

)

  

func TestIndex(t *testing.T) {

f,err:=os.CreateTemp(os.TempDir(),"index_test")
require.NoError(t,err)
defer os.Remove(f.Name())

  

c:=Config{}
c.Segment.MaxIndexBytes=1024
idx,err:=newIndex(f,c)
require.NoError(t,err)

  

_,_,err=idx.Read(-1)
require.Error(t,err)
require.Equal(t,f.Name(),idx.Name())
entries:=[]struct{
Off uint32
Pos uint64
}{
{Off:0, Pos: 0},
{Off:1, Pos:10},
}
```

여기까지는 테스트를 위한 기본 세팅이다. ==`newIndex()` 함수 내부에서 `Truncate()` 함수를 호출해 테스트 항목을 충분히 담을 만큼 인덱스 파일을 키운다. 파일을 슬라이스로 메모리 매핑하기 때문에, 쓰기 전에 우선 파일을 키워야 한다.== 그렇지 않으면 쓸 때 아웃 오브 바운즈(out-of-bounds)에러가 발생한다.

테스트의 나머지 코드를 추가하자.


``` go

// WriteALogPackage/internal/log/index_test.go
for _, want := range entries {

err=idx.Write(want.Off,want.Pos)

require.NoError(t,err)

  

_,pos,err:=idx.Read(int64(want.Off))

require.NoError(t,err)

require.Equal(t,want.Pos,pos)

}

  

// 존재하는 항목의 범위를 넘어서서 읽으려 하면 에러가 나와야 한다.

_,_,err=idx.Read(int64(len(entries)))

require.Equal(t,io.EOF,err)

_=idx.Close()

  
  

// 파일이 있다면, 파일의 데이터에서 인덱스의 초기 상태를 만들어야 한다.

f,_=os.OpenFile(f.Name(),os.O_RDWR,0600)

idx,err=newIndex(f,c)

require.NoError(t,err)

  

off,pos,err:=idx.Read(-1)

require.NoError(t,err)

require.Equal(t,uint32(1),off)

require.Equal(t,entries[1].Pos,pos)

}
```

==각각의 항목에 대해 인덱스에 쓰고 `Read()` 메서드로 읽을 수 있는지 확인한다.== ==그다음에는 인덱스에 저장한 항목 개수보다 큰 오프셋을 읽으려 하면 인덱스와 스캐너가 에러가 나는지 확인한다.== 서비스를 재시작하면 존재하는 파일에서 인덱스의 현재 상태를 다시 만드는지도 확인한다.

세그먼트는 스토어와 인덱스를 가지는데, 각각 최대 크기를 설정해야 한다. 설정 구조체를 추가하여 로그의 설정을 하나로 모아보자. 그러면 로그를 쉽게 설정할 수 있고 코드 전반에 걸쳐 사용하기 편리하다.

`internal/log/config.go` 파일을 만들고 다음 코드를 추가하자.


#### 3.3.3 세그먼트 만들기

==**세그먼트**는 스토어와 인덱스를 감싸고 둘 사이의 작업을 조율한다.== 예를 들어 로그가 활성 세그먼트에 레코드를 추가할 때, ==세그먼트는 데이터를 스토어에 쓰고 새로운 인덱스 항목을 인덱스에 추가한다.== 읽을 때도 마찬가지이다. 세그먼트는 인덱스에서 인덱스 항목을 찾고 스토어에서 데이터를 가져온다.

`internal/log` 디렉터리에 `segment.go` 파일을 만들고 다음 코드를 넣어주자.

``` go

// WriteALogPackage/internal/log/segment.go
package log

  

type segment struct{

store *store
index *index
baseOffset, nextOffset uint64
config Config

}

```

==세그먼트는 내부의 스토어와 인덱스를 호출해야 하므로 처음 두 필드에 각각의 포인터를 가진다.== 베이스가 되는 오프셋과 다음에 추가할 오프셋 값도 가지는데, 인덱스 항목의 상대 오프셋을 계산하고 다음에 항목을 추가할 때 필요하다. 그리고 설정 필드를 두어서 저장 파일과 인덱스 파일의 크기를 설정의 최댓값과 비교할 수 있으므로 세그먼트가 가득 찼는지 알 수 있다.


`newSegment()` 함수를 이어서 작성하자.

``` go
func newSegment(dir string,baseOffset uint64,c Config) (*segment,error) {

s:=&segment{

baseOffset: baseOffset,

config: c,

}

  

var err error

storeFile,err:=os.OpenFile(

path.Join(dir,fmt.Sprintf("%d%s",baseOffset,".store")),

os.O_RDWR|os.O_CREATE|os.O_APPEND,0644,

)

if err != nil {

return nil, err

}

  

if s.store,err=newStore(storeFile);err!=nil{

return nil,err

}

  

indexFile,err:=os.OpenFile(

path.Join(dir,fmt.Sprintf("%d%s",baseOffset,".index")),

os.O_RDWR|os.O_CREATE, 0644,

)

if err != nil {

return nil, err

}

  

if s.index,err=newIndex(indexFile,c); err!=nil{

return nil,err

}

  

if off,_,err:=s.index.Read(-1) ;err != nil {

s.nextOffset=baseOffset

}else{

s.nextOffset=baseOffset+uint64(off)+1

}

  

return s,nil

  

}
```

활성 세그먼트가 가득 찰 때처럼, 로그에 새로운 세그먼트가 필요할 때는 `newSegment()` 함수를 호출한다. 저장 파일과 인덱스 파일을 `os.OpenFile()`함수에서 `os.O_CREATE`파일 모드로 열고, 파일이 없으면 생성하게 한다. 저장 파일을 만들 때는 `os.O_APPEND` 플래그도 주어서 파일에 쓸 때 기존 데이터에 이어서 쓰게 한다. 저장 파일과 인덱스 파일을 열고 난 뒤 이 파일들로 스토어와 인덱스를 만든다. 마지막으로, 세그먼트의 다음 오프셋을 설정하여 다음에 레코드를 추가할 준비를 한다. 인덱스가 비었다면 다음 레코드는 세그먼트의 첫 레코드가 되고, 오프셋은 세그먼트의 베이스 오프셋이 된다. 인덱스에 하나 이상의 레코드가 있다면, 다음 레코드의 오프셋은 레코드의 마지막 오프셋이 될 것이다. 이 값은 베이스 오프셋과 상대 오프셋에 1을 더하여 구한다. 이제 세그먼트에 메서드 몇 개만 추가하면 로그에서 읽고 쓴 준비가 끝난다.

`Append()` 메서드부터 구현해보자.

``` go

// WriteALogPackage/internal/log/segment.go

  

func (s *segment) Append(record *api.Record) (offset uint64, err error) {

cur := s.nextOffset
record.Offset = cur

  

p, err := proto.Marshal(record)
if err != nil {
return 0, err
}

  

_, pos, err := s.store.Append(p)

if err != nil {

return 0, err

}

  

if err = s.index.Write(

// 인덱스의 오프셋은 베이스 오프셋에서의 상댓값이다.

uint32(s.nextOffset-uint64(s.baseOffset)),
pos,
); err != nil {
return 0, err
}

  

s.nextOffset++
return cur, nil

}

```


==`Append()` 메서드는 세그먼트에 레코드를 쓰고, 추가한 레코드의 오프셋을 리턴한다.== 로그는 API 응답으로 오프셋을 리턴한다. 세그먼트가 레코드를 추가할 때는 먼저 스토어에 데이터를 추가한 다음 인덱스 항목을 추가한다. 인덱스 오프셋은 베이스 오프셋의 상대적인 값이기에 세그먼트의 다음 오프셋에서 베이스 오프셋(둘 다 절댓값)을 빼서 항목의 상대적 오프셋을 알아낸다. 그리고 다음번 추가를 대비해서 다음 오프셋을 하나 증가시킨다.

이어서 `Read()` 메서드를 추가한다.

``` go
  

func (s *segment) Read(off uint64) (*api.Record, error) {

_, pos, err := s.index.Read(int64(off - s.baseOffset))

if err != nil {

return nil, err

}

p, err := s.store.Read(pos)

if err != nil {

return nil, err

}

record := &api.Record{}

err = proto.Unmarshal(p, record)

return record, err

}
```

`Read(off uint64)`메서드는 오프셋의 레코드를 리턴한다. 읽기와 비슷하게, ==세그먼트의 레코드를 읽으려면 먼저 절댓값이 인덱스를 상대적 오프셋으로 변환하고 해당 인덱스 항목을 가져온다.== 그다음 세그먼트는 인덱스 항목에서 알아낸 스토어의 레코드 위치로 가서 해당하는 데이터를 읽는다. 마지막으로, 직렬화한 값이 리턴한다.

다음으로 `IsMaxed()` 메서드를 추가하자.

``` go

// WriteALogPackage/internal/log/segment.go

func (s *segment) IsMaxed()bool {

return s.store.size >=s.config.Segment.MaxStoreBytes || s.index.size+entWidth > s.config.Segment.MaxIndexBytes

}
```

==`IsMaxed()` 메서드는 세그먼트의 스토어 또는 인덱스가 최대 크기에 도달했는지를 리턴한다.== 추가하는 레코드의 저장 바이트는 가변이기에 현재 크기가 저장 바이트 제한을 넘지 않으면 되고, 추가하는 레코드에 대한 인덱스 바이트는 고정적이기에(entWidth) 현재의 크기에 인덱스 하나를 추가했을 때 인덱스 제한을 넘지 말아야 한다. 만약 개수는 적지만 크기가 큰 로그를 썼다면 세그먼트의 저장 바이트의 제한을 넘을 수 있고, 개수는 많지만 크기가 작은 로그를 썼다면 세그먼트의 인덱스 바이트 제한을 넘을 수 있다. 이 메서드를 사용해서 세그먼트 용량이 가득 찼는지 확인하여 로그가 새로운 세그먼트를 만들지 판단한다.


이어서 `Remove()` 메서드를 추가한다.

``` go

// WriteALogPackage/internal/segment.go
func (s *segment) Remove() error {

if err:=s.Close() ;err != nil {

return err

}

if err:=os.Remove(s.index.Name()) ;err != nil {

return err

}

if err:=os.Remove(s.store.Name());err != nil {

return err

}

return nil

}
```

`Remove()` 메서드는 세그먼트를 닫고 인덱스 파일과 저장 파일을 삭제한다.

`Close()` 메서드도 추가하자.

```go
  

func (s *segment) Close() error {

if err:=s.index.Close();err != nil {

return err

}

if err:=s.store.Close(); err != nil {

return err

}

return nil

}
```

세그먼트 구현이 끝났다. 이제 테스트해보자. `internal/log` 디렉터리에 `segment_test.go` 파일을 만들고 다음 테스트 코드를 추가하자.

```go
package log

  

import (

"io"

"os"

"testing"

  

log_v1 "github.com/Part3-WriteALogPackage/api/v1"
"github.com/stretchr/testify/require"

)

  

func TestSegment(t *testing.T) {

dir, _ := os.MkdirTemp("", "segment-test")

defer os.RemoveAll(dir)

  

want := &log_v1.Record{Value: []byte("Hello world")}

  

c := Config{}

c.Segment.MaxStoreBytes = 1024

c.Segment.MaxIndexBytes = 3

  

s, err := newSegment(dir, 16, c)

require.NoError(t, err)

require.Equal(t, uint64(16), s.nextOffset, s.nextOffset)

require.False(t, s.IsMaxed())

  

for i := uint64(0); i < 3; i++ {

off, err := s.Append(want)

require.NoError(t, err)

require.Equal(t, 16+i, off)

  

got, err := s.Read(off)

require.NoError(t, err)

require.Equal(t, want.Value, got.Value)

}

  

_, err = s.Append(want)

require.Equal(t, io.EOF, err)

  

// 인덱스가 가득 참

require.True(t, s.IsMaxed())

  

c.Segment.MaxStoreBytes = uint64(len(want.Value) * 3)

c.Segment.MaxIndexBytes = 1024

  

s, err = newSegment(dir, 16, c)

require.NoError(t, err)

  

// 스토어 가득 참

require.True(t, s.IsMaxed())

  

err = s.Remove()

require.NoError(t, err)

s, err = newSegment(dir, 16, c)

require.NoError(t, err)

require.False(t, s.IsMaxed())

}
```


#### 3.3.4 로그 코딩하기

마지막으로 로그를 구현해보자. 로그는 세그먼트를 관리한다. `internal/log` 디렉터리에 log.go 파일을 생성하고 다음 코드를 추가한다.


``` go
// WriteALogPackage/internal/log/log.go
package log

  

import "sync"

  
  

type Log struct{

mu sync.RWMutex
Dir string
Config Config

activeSegment *segment
segments []*segment

}
```

==로그는 세그먼트 포인터의 슬라이스와 활성 세그먼트를 가리키는 포인터로 구성된다.== 디렉터리는 세그먼트를 저장하는 위치이다.

이어서 NewLog() 함수를 추가하자.

``` go
func NewLog(dir string,c Config) (*Log,error) {

if c.Segment.MaxStoreBytes==0{
c.Segment.MaxStoreBytes=1024
}

if c.Segment.MaxIndexBytes==0 {
c.Segment.MaxIndexBytes=1024
}

  

l:=&Log{
Dir: dir,
Config: c,
}

return l, l.setup()
}

```

==`NewLog(dir string,c Config)` 함수에서는 먼저, 호출자가 설정을 정의하지 않았다면 디폴트 값으로 설정하고 로그 인스턴스를 생성한 다음 setup() 메서드로 인스턴스를 설정한다.==
인스턴스를 설정하는 `setup()` 메서드를 추가하자.

``` go
// WriteALogPackage/internal/log/log.go

func (l *Log) setup()error{

files,err:=os.ReadDir(l.Dir)

if err != nil {

return err

}

  

var baseOffsets []uint64

for _, file := range files {

offStr:=strings.TrimSuffix(

file.Name(),

path.Ext(file.Name()),

)

  

off,_:=strconv.ParseUint(offStr,10,0)

baseOffsets = append(baseOffsets, off)

}

  

sort.Slice(baseOffsets,func(i, j int) bool {return baseOffsets[i]<baseOffsets[j]})

  

for i := 0; i < len(baseOffsets); i++ {

if err=l.newSegment(baseOffsets[i]);err != nil {

return err

}

// 베이스 오프셋은 index와 store 두 파일을 중복해서 담고 있기에

// 같은 값이 하나 더 있다. 그래서 한번 건너뛴다.

i++

}

  

if l.segments==nil {

if err=l.newSegment(

l.Config.Segment.InitialOffset,

); err != nil {

return err

}

}

return nil

}
```

로그를 시작하면 이미 디스크에 존재하는 세그먼트들로 세팅하거나, 세그먼트가 없다면 첫 번째 세그먼트를 부트스트래핑한다. ==디스크에서 세그먼트 목록을 가져와서 파싱하고 베이스 오프셋을 정렬한다. 그리고 newSegment() 메서드로 각각의 베이스 오프셋에 대해 세그먼트를 만들어서 세그먼트 슬라이스가 오래된 것부터 정렬되게 한다.==

`Append()` 메서드를 추가하자.

``` go
func (l *Log) Append(record *log_v1.Record) (uint64,error) {

l.mu.Lock()
defer l.mu.Unlock()

  

if l.activeSegment.IsMaxed() {
off:=l.activeSegment.baseOffset
if err:=l.newSegment(off);err != nil {
return 0,err
}
}

return l.activeSegment.Append(record)

}
```

`Append(record *api.Record)` 메서드는 우선 활성 세그먼트가 가득 찼는지 확인하고, 가득 찼찼다면 새로운 세그먼트를 만들어준 다음, 레코드를 로그의 활성 세그먼트에 추가한다. Append() 메서드 내의 코드들은 뮤텍스(mutex)로 감싸서 접근을 조율한다. RWMutex를 사용하는데 쓰기 록(lock)이 걸려 있지 않으면 얼마든지 읽을 수 있다. 뮤텍스로 감싼 부분(critical section)의 범위가 너무 넓을 때는 세그먼트와 관련한 부분에만 뮤텍스를 쓸 수도 있지만, 여기서는 코드가 간결하도록 메서드 전체를 감쌌다.

이번에는 `Read()` 메서드를 추가하자.

```go
// WriteALogPackage/internal/log/log.go
  

func (l *Log) Read(off uint64) (*log_v1.Record, error) {

l.mu.Lock()
defer l.mu.Unlock()

  

var s *segment
for _, segment := range l.segments {
if segment.baseOffset <= off && off < segment.nextOffset {
s = segment
break
}
}

if s == nil || s.nextOffset <= off {
return nil, fmt.Errorf("offset out of range: %d", off)
}

return s.Read(off)

}
```

`Read(off uint64)` 메서드는 해당 오프셋에 저장된 레코드를 읽는다. ==먼저 레코드가 위치한 세그먼트를 찾는다. 세그먼트들이 오래된 레코드 순서대로 정렬되고, 세그먼트의 베이스 오프셋이 세그먼트에서 가장 작은 오프셋이라는 특성을 이용해서 찾는다.== 세그먼트를 찾고 나면 세그먼트의 인덱스에서 인덱스 항목을 찾아서 세그먼트의 저장 파일에서 데이터를 읽어 리턴한다.


`Close(), Remove(), Reset() `메서드를 추가한다.

``` go

// WriteALogPackage/internal/log/log.go

  

func (l *Log) Close()error {

l.mu.Lock()
defer l.mu.Unlock()

for _, segment := range l.segments {
if err:=segment.Close();err != nil {
return err
}
}

return nil

}

  

func (l *Log)Remove()error {

if err:=l.Close();err != nil {
return err
}
return os.RemoveAll(l.Dir)
}

  

func (l*Log) Reset()error {
if err:=l.Remove();err != nil {
return err
}

return l.setup()
}
```

세 메서드는 서로 연관이 있다.

- **Close()**: 로그의 모든 세그먼트를 닫는다.
- **Remove()**: 로그를 닫고 데이터를 모두 지운다.
- **Reset()**: 로그를 제거하고 이를 대체할 새로운 로그를 생성한다.


이어서 **LowestOffset()**과 **HighestOffset()** 메서드를 추가하자.

``` go
func (l *Log) LowestOffset() (uint64,error) {

l.mu.RLock()
defer l.mu.RUnlock()

return l.segments[0].baseOffset,nil

}

  

func (l*Log) HighestOffset()(uint64,error) {

l.mu.RLock()
defer l.mu.RUnlock()

off:=l.segments[len(l.segments)-1].nextOffset

if off==0 {
return 0,nil
}

return off-1,nil

}
```

이 메서드들은 로그에 저장된 오프셋의 범위를 알려준다. 8장에서 다룰 복제 기능 지원이나 클러스터 조율을 할 때 이러한 정보가 필요하다. ==어떤 노드가 오래된, 또는 가장 새롭게 생성된 데이터를 가지는지, 그리고 어떤 노드가 뒤처져 있어서 복제해야 하는지를 알 수 있다.==

`Truncate()` 메서드를 추가하자.

``` go
  

func (l *Log) Truncate(lowest uint64) error {

l.mu.Lock()
defer l.mu.Unlock()

var segments []*segment

for _, s := range l.segments {
if s.nextOffset<=lowest+1{
if err:=s.Remove();err != nil {
return err
}
continue
}
segments=append(segments, s)
}

l.segments=segments
return nil

}
```

==`Truncate(lowest uint64)` 메서드는 가장 큰 오프셋이 가장 작은 오프셋보다 작은 세그먼트를 찾아 모두 제거한다.== 특정 시점보다 오래된 세그먼트들을 지우는 셈이다. 디스크의 용량이 무한적일 수는 없기에, 주기적으로 Truncate() 메서드를 호출하여 데이터를 모두 처리했거나 더는 필요 없는 세그먼트를 찾아 제거할 것이다.

Reader() 메서드를 포함하는 다음 코드를 추가하자.

``` go
// WriteALogPackage/internal/log/log.go
  

func (l *Log) Reader() io.Reader {

l.mu.RLock()
defer l.mu.RUnlock()

readers := make([]io.Reader, len(l.segments))


for i, segment := range l.segments {
readers[i] = &originReader{segment.store, 0}
}

return io.MultiReader(readers...)
}

type originReader struct{
*store
off int64
}

  

func (o* originReader) Read(p []byte) (int,error) {

n,err:=o.ReadAt(p,o.off)
o.off+=int64(n)
return n,err

}
```

==`Reader()`메서드는 io.Reader 인터페이스 자료형을 리턴하여 전체 로그를 읽게 한다.== 조율한 합의(coordinate consensus)를 구현할 때와 스냅숏, 로그 복원 기능을 지원할 때 필요하다. ==Reader() 메서드는 io.MultiReader()를 호출하여 세그먼트의 스토어들을 하나로 모은다.== 세그먼트의 스토어들을 originReader 자료형으로 감싸는데, 첫 번째 이유는 io.Reader 인터페이스를 만족시켜 io.MultiReader()에 전달하려는 것이다. 두 번째 이유는 스토어의 처음부터 일기 시작해 전체 파일을 읽는 것을 확실히 하려는 것이다.

이제 마지막 메서드로, 새로운 세그먼트를 생성하는 `newSegment()`를 추가한다.

``` go
// WriteALogPackage/internal/log/log.go

func (l*Log) newSegment(off uint64) error {

s,err:=newSegment(l.Dir,off,l.Config)

if err != nil {
return err
}

l.segments=append(l.segments, s)
l.activeSegment=s
return nil

}
```

==`newSegment(off uint64)`는 새로운 새그먼트를 생성해서 로그의 세그먼트 슬라이스에 추가한다.== 그리고 활성 세그먼트로 만들어 이후의 레코드 추가를 여기에 쓰도록 하겠다.

이제 로그를 테스트할 시간이다. `internal/log` 디렉터리에 `log_test.go` 파일을 만들고 다음 코드부터 넣어준다.



``` go

// WriteALogPackage/internal/log/log_test.go

package log

  

import (
"os"
"testing"
  
"github.com/stretchr/testify/require"

)

  

func TestLog(t *testing.T) {

for scenario, fn := range map[string]func(t *testing.T, log *Log){
"append and read a record succeeds": testAppendRead,
"offset out of range error": testOutofRangeErr,
"init with existing segments": testInExisting,
"reader": testReader,
"truncate": testTruncate,

} {

t.Run(scenario, func(t *testing.T) {

dir, err := os.MkdirTemp("", "store-test")
require.NoError(t, err)
defer os.RemoveAll(dir)

  

c := Config{}
c.Segment.MaxStoreBytes = 32
if scenario == "make new segment" {
c.Segment.MaxIndexBytes = 13
}

log, err := NewLog(dir, c)
require.NoError(t, err)
fn(t, log)
})

}

}
```

`TestLog(t *testing.T)`는 로그 테스트를 위한 테스트 테이블을 정의한다. 테스트 테이블을 사용하면 테스트를 위한 로그 생성 코드를 중복해서 작성하지 않아도 된다.

테스트 케이스들을 정의해보자.


``` go
// WriteALogPackage/internal/log/log_test.go

func testAppendRead(t *testing.T,log *Log) {

append:=&log_v1.Record{

Value: []byte("hello world"),

}

off,err:=log.Append(append)

require.NoError(t,err)

require.Equal(t,uint64(0),off)

read,err:=log.Read(off)

require.NoError(t,err)

require.Equal(t,append.Value,read.Value)

}

```

`func testAppendRead(t *testing.T,log *Log)`는 로그에 레코드를 읽고 쓸 수 있는지 테스트한다. ==레코드를 로그에 추가하면, 로그는 레코드의 오프셋을 리턴한다.== 그래서 해당 로그의 해당 오프셋의 레코드를 요청하면, 추가했던 레코드를 읽을 수 있어야 한다.


``` go
// WriteALogPacakge/internal/log/log_test.go

func testOutofRangeErr(t *testing.T,log *Log) {

read,err:=log.Read(1)

require.Nil(t,read)

require.Error(t,err)

}

```

`func testOutofRangeErr(t *testing.T,log *Log) `는 로그가 저장한 범위 밖의 오프셋을 읽으려 하면 에러를 리턴하는지 테스트한다.


``` go
// WriteALogPackage/internal/log/log_test.go



func testInExisting(t *testing.T, o *Log) {

append := &log_v1.Record{

Value: []byte("hello world"),

}

for i := 0; i < 3; i++ {

_, err := o.Append(append)

require.NoError(t, err)

}

require.NoError(t, o.Close())

  

off, err := o.LowestOffset()

require.NoError(t, err)

require.Equal(t, uint64(0), off)

off, err = o.HighestOffset()

require.NoError(t, err)

require.Equal(t, uint64(2), off)

  

n, err := NewLog(o.Dir, o.Config)

require.NoError(t, err)

  

off, err = n.LowestOffset()

require.NoError(t, err)

require.Equal(t, uint64(0), off)

off, err = n.HighestOffset()

require.NoError(t, err)

require.Equal(t, uint64(2), off)

}
```

`func testInExisting(t *testing.T, o *Log)`는 로그를 생성하면 이전 로그 인스턴스가 저장한 데이터를 가져와 서비스할 준비(부트스트래핑)를 마치는지 테스트한다. 세 개의 레코드를 추가하고 로그를 닫은 다음, 새로운 로그를 이전 로그와 같은 디렉터리로 설정하여 생성한다. 이제 새로운 로그가 이전 로그에서 데이터를 가져와 준비를 마쳤는지 확인하자.


``` go
// WriteALogPackage/internal/log/log_test.go

func testReader(t *testing.T,log *Log) {

append:=&log_v1.Record{

Value: []byte("hello world"),

}

off,err:=log.Append(append)

require.NoError(t,err)

require.Equal(t,uint64(0),off)

  

reader:=log.Reader()

b,err:=io.ReadAll(reader)

require.NoError(t,err)

  

read:=&log_v1.Record{}

err=proto.Unmarshal(b[lenWidth:],read)

require.NoError(t,err)

require.Equal(t,append.Value,read.Value)

}
```

`func testReader(t *testing.T,log *Log)`는 디스크에 저장한 전체 로그를 읽을 수 있는지 테스트한다. 이 기능으로 스냅숏을 찍고 로그를 복원할 수 있다. 8.2절을 참고한다.

``` go

// WriteALogPackage/internal/log/log_test.go

  

func testTruncate(t *testing.T,log *Log) {

append:=&log_v1.Record{
Value: []byte("hello world"),
}

  

for i := 0; i < 3; i++ {
_,err:=log.Append(append)
require.NoError(t,err)
}

  

_,err:=log.Read(0)
require.NoError(t,err)

  

err=log.Truncate(1)
require.NoError(t,err)

  

_,err=log.Read(0)
require.Error(t,err)

  

}
```

`func testTruncate(t *testing.T,log *Log) `는 로그를 잘라내고, 더는 필요 없는 오래된 세그먼트를 제거한다.

이제 로그 코드 구현이 끝났다. 단순한 기능의 로그가 아니라 카프카를 구동할 수 있는 수준의 로그를 구현했음에도 그리 어렵지 않았다.

---
# 3.4 마치며

3장에서는 로그가 무엇이고 왜 중요한지 살펴보았다. 분산 서비스를 포함한 다양한 애플리케이션에서 어떻게 쓰이는지를 알았고 어떻게 만드는지를 배웠다. 여러분이 구현한 로그는 분산 로그의 기초가 될 것이다. 이제 구현한 라이브러리를 기반으로 서비스를 만들 수 있고, 다른 컴퓨터에서도 라이브러리의 기능에 접근할 수 있도록 할 수 있다.