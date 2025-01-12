시작을 위해 간단한 CMakeLists 파일을 만들어보자. 하나의 실행가능한 소스파일의  CMakeLists 파일은 두 줄의 명령어가 있다.

```CMake
project (Hello)
add_execuatble (Hello Hello.c)
```

Hello를 실행할 수 있게 할려면 CMake를 실행하여 Makefiles과 마이크로소프트 프로젝트 파일을 실행해야 한다. `project` 명령어는 어떤 이름의 최종 워크스페이스여야 하는지 그리고 `add_executable` 명령어가 어떤 실행가능한 타겟의 빌드 프로세스가 추가되는지 알게 해 준다. 이것이 전부 간단한 예제를 위해 알아야 할 것 들이다. 만약 프로젝트의 일부 파일 요구는 굉장히 쉬운 일이다, 그냥 add_executable를 아래의 라인데로 수정하기만 하면 된다.

```shell
add_executable(Hello Hello.c File2.c File3.c File4.c)
```

`add_executable`은 CMake에서 사용할 수 있는 많은 명령어 중 하나일 뿐이다. 더 복잡한 명령어는 아래에 있다.

```cmake
cmake_minimum_required (2.6)
project (HELLO)

set (HELLO_SRCS Hello.c File2.c File3.c)

if (WIN32)
	set(HELLO_SRCS ${HELLO_SRCS} WinSupport.c)
else ()
	set(HELLO_SRCS ${HELLO_SRCS} UnixSupport.c)
endif ()


add_executable(Hello ${HELLO_SRCS})

# look for the Tcl library
find_library(TCL_LIBRARY 
NAMES tcl tcl84 tcl83 tcl82 tcl80
PATHS /usr/lib /usr/local/lib
)

if (TCL_LIBRARY)
	target_link_library (Hello ${TCL_LIBRARY})
endif ()


```

이 예제에서는 `set`은 소스파일에서 그룹을 이루는 명령어로 쓰인다. `if`는 WinSupport.c , UnixSupport.c 둘 중 하나를 통해서 CMake가 윈도우즈에서 실행시킬지 아닐지를 결정하게 한다. 마지막으로, add_executable 명령어는 Tcl 라이브러리를 몇 개의 다른 이름과 다른 위치를 통해서 찾게 해 주는 명령어이다. `if` 명령어는 `TCL_LIBRARY`가 발견되어 링크 라인(link line)으로 Hello 실행 타겟에 추가되는지 확인한다. `#`문자는 커맨드라인 라인에 대한 부연 설명을 위해 사용한다. `#`뒤의 모든 문자열은 설명을 하기 위해 붙여진다.