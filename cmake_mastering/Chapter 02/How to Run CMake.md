CMake가 시스템에 저장되었다면 프로젝트 빌드하는데 굉장히 쉽다. ==CMake가 프로젝트를 빌드할 때 두 개의 메인 디렉토리가 있다==: 소스 디렉토리와 바이너리 디렉토리 이 두개이다.
소스 디렉토리는 소스코드가 어디에 존재하는지 알려준다. 이는 또한 CMakeLists 파일이 있는 곳이기도 하다. 바이너리 디렉토리는 당신이 CMake 결과 오브젝트파일(Resulting object file), 라이브러리 그리고 실행파일(executable)들을 두는 곳이다. 일반적으로 CMake는 어떠한 소스 디렉토리 파일을 기록하지 않으며, 오로지 바이너리 파일만 기록한다. 만약 당신이 소스와 바이너리 둘 다 설정하고 싶을 수도 있을 것이다. 이는 인소스(in-source) 빌드라고 불리우는데, 반대로 아웃-오브-소스(out-of-source)는 이와 다르다.


CMake는 인소스, 아웃오브소스 빌드를 모든 운영체제에서 지원한다. 이는 당신이 완전히 소스 코드 트리 밖에서 빌드를 구성할 수 있다는 의미이며, 빌드를 통해 모든 파일들을 지우는 것이 아주 쉽다는 말이 된다. 빌드 트리를 가지는 것은 소스트리가 복수의 단일 소스 트리를 빌드하는 것을 쉽게 하는 것과 다르다. 당신이 복수의 빌드를 다른 옵션으로 할 때 유용하지만, 오로지 소스코드의 하나의 복사에 지나지 않는다. 이제 GUI 기반의 Qt를 사용한 것과 커맨드라인 인터페이스를 통해 CMake 를 실행해 보겠다.




### Running CMake's Qt Interface

CMake는 클린턴 스팀슨(Clinton Stimpson) 주도 하에 개발된 Qt를 포함하여 UNIX, 맥, 윈도우즈 와 같은 대부분의 플랫폼에서 사용 가능하도록 하게 한다. 이런 인터페이스는 CMake 소스코드에 포함되어 있지만, 빌드를 당신의 시스템에 Qt가 설치되어 있어야 한다.

- [Figure 1]- Qt 기반 CMake GUI




윈도우에서 실행 파일은 `cmake-gui.exe`라고 명명되며 스타트 메뉴에 Program files 에 있어야 한다. 이는 컴퓨터에서 바로 접근할 수 있는 지름길(shortcat)이며, 만약 CMake로 소스를 빌드할 때, 빌드 디렉토리가 도리 수 있을 것이다. UNIX와 Mac 유저의 실행파일은 cmake-gui 이며 CMake 실행파일에 저장되어 있다. GUI는 Figure 1(여기에는 없음)의 그림과 같이 비슷하게 나타난다. 제일 위의 두 개의 앤트리(entry)는 소스코드와 바이너리 디렉토리이다. 이는 소스코드가 어디 있는지 무엇을 컴파일하는지 그리고 바이너리 결과가 어디에 있는지 구체화하도록 해 준다. 당신은 이 두 개를 설정하여야 한다. 만약 바이너리 디렉토리에 대한 명시를 정확히 하지 않을 경우, 이것 자체가 생성된다. 만약 바이너리가 CMake에서 구성이 될 시 자동으로 소스 트리가 설정된다.

중간 시점(middle area)는 당신이 다른 옵션을 빌드 시스템에 구체화할 수 있게 한다. 더 애매한 변수들이 숨겨져 있지만, 이는 뷰 아래에 있는 "Advanced View(향상 뷰)"를 선택하면 볼 수 있다. 타이핑을 통해 모든 종류의 값을 검색 상자를 통해 중간 시점에서 찾을 수 있다. 특정한 환경설정이나 옵션을 거대한 프로젝트에서 찾는데 꽤 편리할 것이다. 마지막 시점(bottom area)에서는 구성(Configure)과 실행 버튼을 진행 표시줄(progress bar)와 내릴 수 있는 외부 창과 함께 있다.


소스코드와 바이너리 디렉토리를 구체화할 때 반드시 구성(Configure) 버튼을 눌러야 한다. 이는 CMake가 CMakeLists에 있는 소스코드 디렉토리를 읽게 한 뒤 캐시(cache) 영역이 어떠한 형태의 프로젝트 옵션으로 업데이트 되는지 보여준다. 만약 당신이 cmake-gui를 바이너리 디렉토리에 처음 실행한다면, 당신을 어떤 실행기를 통해 사용하게 할지 유도할 것이다. 마치 [Figure 2]에 나와 있는 것처럼. 이 다이얼로그는 당신이 원하는 대로 빌드 할 수 있게, 커스터마징과 컴파일러 컴파일러 수정을 나타낸다.

- [Figure 2]

이 후 첫번째 구성은 만약 당신의 캐시(cache)가 Configure 버튼을 다시 눌렀을 시 받아들일(adjust)것이다. 새 값은 구성 프로세스로부터 빨간 색으로 색칠 된 채 생성됬을 것이다. 확실히 하기 위해 당신이 구성(Configure)을 클릭하기 전까지 빨간 색이 아니었던 가능한 값을 보게 되면서 행복해할 것이다. 한번 구성이 끝나면, 실행 버튼(Generate button)을 누르면, 이것이 올바를 파일을 생산할 것이다.


당신의 환경이 cmake-gui를 실행하는데 알맞은지는 아주 중요하다. 만약 당신이 비주얼 스튜디오를 사용한다면 당신의 환경은 그 환경에 맞게 수정되어야 할 것이다. 만약 NMake나 MinGW를 사용한다면 컴파일러가 그 환경에 돌아가도록 할 것이다. 또한 이미 설정된 환경변수를 요구하는 당신의 컴파일러나 셸(shell)에서도 다룰 수 있다. 예를 들어, 마이크로소프트 비주얼 스튜디오는 비주얼 스튜디오 커맨드 프롬프트를 생성하는 스타트 메뉴 옵션이 있다. 당신은 cmak-gui를 만약 NMake의 Makefile을 사용하고 싶다면, 커맨드 프롬프트에서 cmake-gui를 실행해야 한다. 같은 방법으로 MinGW에도 적용되며, cmake-gui를 MinGW shell를 사용하여, MinGW의 경로에 있는 컴파일러를 실행하게 해야 한다.


cmake-gui는 구체화된 바이너리 라이브러리로 빌드 파일 실행을 마무리했을 때, 종료된다. 만약 비주얼 스튜디오가 실행기(generator)로 선택되었다면, MSVS workspace(아니면 solution) 파일이 생성된다. 이 파일의 이름은 당신이 CMakefLists 파일에서 `PROJECT` 명령어를 통해 초기에 설정된 이름을 통해 정해진다. 많은 다른 실행기 타입들을 위해 Makefile이 생성된다. 다음 단계의 프로세스는 MSVC를 통해 워크스페이스를 여는 것이다. 열리면, 프로젝트는 **일반 매너의 마이크로소프트 C++**(normal manner of executables in the package)에서 빌드될 수 있다. `ALL_BUILD` 타겟은 모든 라이브러리와 실행 파일을 패키지 안에서 빌드하도록 쓰여질 수 있다. 만약 빌드 타입 Makefile 파일을 사용할 때, make나 nmake를 사용하여 Makefiles를 빌드할 수 있게 한다.


### Running the ccmake Curses(커서) Interfaces

대부분의 유닉스 플랫폼은, 만약 커서 라이브러리(curses library)가 지원된다면, CMake는 ccmake라는 실행파일을 지원한다. 이 인터페이스는 GUI 기반의 Qt와 유사한 터미널 기반의 텍스트 어플리케이션이다. ccmake를 실행하기 위해,  디렉토리 바꾸기(change directory, cd)를 당신이 원하는 바이너리 디렉토리로 위치를 바꾼다. 이는 우리가 소위 인소스(in-source)라 부르는 소스코드를 빌드하거나 새로 생성한 디렉토리와 같을 수 있다. 그러면 ccmake를 커맨드라인을 통해 실행할 수 있다. 인소스는 `.`를 사용하여 소스 디렉터리를 빌드할 수 있다. 이는 [Figure 3]에서 텍스트 인터페이스를 통해 시작하는 것을 볼 수 있다(이 케이스는 캐시 변수가 VTK에 있는 것이며, 자동적으로 설정된 것이다).

[Figure 3]

간단한 설명이 창 아래에 있는 것이 보일 것이다. 만약 `c`를 누른다면 이는 프로젝트를 구성할 것이다. 반드시 항상 캐시 값을 변경한 후 구성해야 한다. 값을 바꾸기 위해, 화살표 키로 cache 엔트리를 선택하면 원하는데 값이 설정될 것이며, g를 누르면 Makefile가 생성되며 프로그램 종료가 될 것이다. 또한 `h`키는 도움말 설정을 할 것이며, `q`는 나가기 `t`는 향상된 캐시 앤트리 뷰를 토글화 하는 것이다. Hello라고 불리우는 hello world 프로젝트에서 유닉스 플랫폼에서 CMake 사용의 용례를 두 가지 보여주고 있다. 첫 번째는 인소스 빌드 실행이다.

```shell
cd Hello
ccmake .
make
```

두 번째 예시는 아웃오브소스(out-of-source) 빌드 실행이다.

```shell
mkdir Hello-Linux
cd Hello-Linux
ccmake ../Hello
make
```


### Running CMake from the Command Line

커맨드라인에서, CMake는 상호적으로 주고받는 새션(interactive question and answer session) 또는 비 상호작용적 프로그램으로 실행될 수 있다. 상호작용 모드로 실행하기 위해선, 그냥 `-i` 옵션을 CMake에 추가한다. 이는 CMake가 당신에게 각각의 프로젝트를 위한 캐시 파일에 있는 엔트리에 대해 묻게 한다. CMake는 마치 GUI나 커시스(curses)기반 인터페이스와 같은 합리적인 디폴트(일반적인 요소?) 제공한다. 프로세스는 더 이상 물어볼 것이 없다면 종료한다. 상호작용 모드 CMake 는 아래와 같은 형태로 작동된다.

```shell

$ cmake -i -G "NMake Makefiles" ../CMake
Would you like to see advanced options? [No]:
Please wait while cmake processes CMakeLists.txt files....

Variable Name: BUILD_TESTING
Description: Build the testing tree.
Current Value: On
New Vale (Enter to keep current value):

Variable Name: CMAKE_INSTALL PREFIX
Description: Install path prefix, prepended onto install
Directories.
Current Value: C:/Program Files/CMake
New Value (Enter to keep current value):

Please wait while cmake processes CMakeLists.txt files
CMake complete, run make to build project.
```

CMake를 사용하여 프로젝트를 비상호작용적 모드로 빌드한다는 것은 거의 아니면 아예 옵션 없이 간단한 프로세스를 한다는 것이다. VTK 같은 큰 프로젝트에서는, `ccmake, cmake -i` 또는 `cmake-gui`를 추천한다. 비상호작용적 CMake로 프로젝트를 빌드하기 위해, 첫 번째로 디렉토리를 당신이 원하는 바이너리로 위치를 옮긴다. 인소스 빌드를 원한다면 `cmake .`를 실행하며 그리고 `-D` 플래그를 통해 어떠한 옵션이건 전달할 수 있다. 아웃오브소스 프로젝트를 위해서는 `cmake`와 인자(argument) 형태로 소스코드에 경로(path)를 제공해야만 한다. 그러면 타입 `make`와 프로젝트는 컴파일 된다. 어떤 프로젝트는 타입 `make install`로 이들을 설치하여  똑같이 타겟을 설치할 수 있다.


### Specifying the Compiler to CMake

어떤 시스템은 하나 이상의 컴파일러를  다른 . 이러한 케이스는 CMake를 통해 사용하고자 하는 컴파일러의 위치를 구체화해야 한다. 이를 구체화하는 3가지 방법이 있다; 실행기가 컴파일러를 구체화할 수 있으며, 환경변수를 설정하거나 캐시 앤트리를 설정할 수 있다. 몇몇의 실행기는 특정 컴파일러와 연결되어 있으며, 예를 들어 비주얼 스튜디오 6 실행기는 항상 바이크로소프트 비주얼 스튜디오 6 컴파일러를 사용한다. Makefile 기반 실행 CMake는 일반적인 컴파일러에서 작동되는 컴파일러를 찾을 때까지 시도한다. 리스트는 이 파일에서 찾을 수 있다:

```shell
Modules/CMakeDeterminCCompiler.cmake and
Modules/CMakeDetermineCXXCompiler.cmake
```

이러한 리스트들은 환경변수를 통해 미리 선점되어 CMake가 실행되기 전에 설정될 수 있다. `CC` 환경 변수는 `CXX`가 C++ compiler를 구체화할 때, C 컴파일러를 구체화한다. 당신은 컴파일러들을 `- DCMAKE_CXX_COMPILER=c1`를 통해 곧바로 커맨드라인으로 구체화할 수 있다. 만약 이것들이 설정되지 않으면,  CMake는 아래의 컴파일러 일스트로 시도를 한다:

```shell
c++ g++ CC aCC c1 bcc x1C.
```

CMake가 컴파일러를 선택하고 실행할 때, 캐시 엔트리를 `CMAKE_CXX_COMPILER`와 `CMAKE_C_COMPILER`를 통해 선택권을 바꿀 수 있다, 하지만 추천하지는 않는다. 이걸 하는 동안 문제는 당신이 이미 컴파일러를 통해 무엇을 지원할 지 테스트를 실행하고 있다는 것이다. 컴파일러를 바꾸는 것은 잘못된 결과로 이끌 수 있는 재실행을 하기위해 테스트를 하지는 않는다. 컴파일러의 플래그와 링커는 환경변수 설정으로 바뀔 수 있다. `LDFLAGS`가 캐시 값을 링크 플래그를 위해 초기화(initialize)한다. `CXXFLAGS`와 `CFLAGS`가 `CMAKE_CXX_FLAGS`와 `CMAKE_C_FLAGS`를 초기화하는 것처럼 말이다.


### Dependency Analysis
##### 의존성 분석

CMake는 강력한 C와 C++ 소스 코드파일을 위한 빌트-인(built-in) 의존성 분석 능력 가지고 있다. CMake는 또한 제한된 포트란과 자바 의존성을 지원하기도 한다. 통합 개발 환경(IDE)가 의존성 정보를 지원하고 유지보수해 왔기 때문에, CMake는 이러한 빌드 시스템을 뛰어넘었다. 하지만 make 프로그램과 작동하는 Makefile은 어떻게 자동적으로 계산하고 의존성 정보를 최신으로 유지하는지 알지 못한다. 이러한 의존성 생성(generation)과 유지(maintenance)는 CMake을 통해 자동적으로 이루어진다. 프로젝트가 CMake를 통해 처음으로 구성되어 지면, 유저는 make 프로그램을 실행시키며, CMake는 나머지 일을 한다. CMake의 의존성은 완전히 병렬성 빌드를 멀티프로세서 시스템을 위해 지원한다.


비록 유저가 CMake가 어떻게 작동되는지 알 필요가 없다 해도, 이는 아마 프로젝트의 의존성 정보(dependency information)를 확인하는데 유용하다. 이 정보는 각각의 타겟들이 저장돼 있는`depend.make`, `flags.make`, `build.make` 그리고 `DependInfo.cmake.depend.make`은 모든 디렉토리에 있는 오브젝트파일들의 의존 정보를 저장한다. 

- `flags.make`는 소스코드를 타겟으로하는 컴파일 플래그(compile flag)를 저장한다. 바꾸 싶다면 파일이 재컴파일(recompiled)된다.
- `DepenInfo.cmake`는 의존성 정보를 최신으로 유지하거나, 어떤 파일이 프로젝트의 일부이며 어떤 언어로 쓰여졌는지 정보를 저장한다 
- 마지막으로 의존성 빌드를 조종은 `build.make`에 저장된다. 만약 의존성이 버전이 구식이면 모든 타겟을 향한 의존성이 재계산되며, 현재 의존성 정보는 유지된다.
