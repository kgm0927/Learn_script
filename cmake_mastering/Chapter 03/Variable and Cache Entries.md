CMakeLists 파일은 다른 프로그래밍 언어처럼 변수를 사용한다. 변수는  "ON"나 "OFF" 같이 나중에 사용할 변수를 저장하거나, `/usr/include`, `/usr/local/include` 같이 리스트를 나타낼 때 사용하기도 한다. 많은 유용한 변수는 자동적으로 CMake와 논의될 `Appedix A -` 변수를 통해 정의된다.

CMake의 변수는 `${VARIABLE}` 기록을 통해 참조되어지는데, 이는 `set` 명령어 실행을 위해 정의된다. 아래의 예시를 살펴보자:

```shell
# FOO is undefined

set (FOO 1)
# FOO is now set to 1

set (FOO 0)
# FOO is now set to 0
```

보면 "복잡하지 않은(straightforward)"것 같지만, 아래의 예시를 보라.

```shell
set (FOO 1)

if (${FOO} LESS 2)
	set (FOO 2)
else (${FOO} LESS 2)
	set (FOO 3)
endif (${FOO} LESS 2)
```

확실히 `if`문이 사실이라면, 이는 `if`문이 실행될 것이다. 만약 FOO 변수가 2라면, `else`에 접촉되어 `FOO`는 2가 될 것이다. 보통 CMake의 새 값 `FOO`가 쓰이지만, `else` 문은 조종하거나 항상 `if`문이 실행될 때, 뒤에 있는 값을 참조하는 행위를 통해 드문 "예외(exception)"처리를 한다. 그래서 이런 경우(위의 경우) `else` 절(clause) 실행되지 않을 것이다. 더 깊게 변수의 범위를 이해하기 위해 예시를 보자:

```shell

set (foo 1)

# process the dir1 subdirectory
add_subdirectory (dir1)

# include and process the commands in file1.cmake
include (file1.cmake)

set (bar 2)
# process the dir2 subdirectory
add_subdirectory(dir2)

# include and process the commands in file2.cmake
include (file2.cmake)
```

이 예제에서는 변수 `foo`가 처음에 정의되었기 때문에, 프로세싱 도중에 dir1과 dir2이 정의될 것이다. 대조적으로, `bar`는 오직 dir2가 프로세싱될 때 정의된다. 이렇듯 `foo`는 file1.cmake과 file2.cmake가 프로세싱될 때 정의되는데, 반대로(whereas) `bar`는 file2.cmake가 프로세싱될 때 정의된다.

CMake의 변수는 대부분의 언어로부터 약간 떨어진 범주에 있다. 변수를 절정할 때 현재 CMakeLists 파일과 함수가 관측 가능하며, 똑같이 서브디렉토리의 CMakeLists 파일 또한 그렇고, 모든 함수 매크로(macros) 또한 적용되며, `INCLUDE`를 통해 포함된 모든 파일도 포함된다. 서브디렉토리는 새 변수의 범위(scope)가 현재 호출하는 범위의 모든 값의 범위로 생성되거나 초기화될 때 프로세스된다. 모든 새 변수는 자식 범위(child scope)에서 생성되며, 또는 현존하는 변수로 만들어지며, 부모 범위(parent scope)에 영향을 주지 않는다. 아래의 예시를 보자:

``` cmake
function (foo)
	message (${test}) # test is 1 here
	set (test 2)
	message (${test}) # test is 2 here, but only in this scope
endfunction()

set (test 1)
foo ()
message (${test}) # test will still be 1 here.
```

일부 경우에는 함수 아니면 서브디렉토리를 이것의 부모 범위의 변수가 설정되기를 원할 것이다. 이는 CMake이 함수로부터 반환되기 위해, 그리고 `PARENT_SCOPE`옵션으로 `set` 명령어와 같이 끝낼 수 있는 유일한 방법이다. 우리는 이전의 예시를 함수 foo가 test 값을 아래와 같이 부모 범위의 값으로 변경하여 수정할 수 있다.

```cmake
function (foo)
message (${test}) # test is 1 here
set (test 2 PARENT_SCOPE)
message (${test}) # test still 1 in this scope
endfunction()

set (test 1)
foo()
message (${test}) # test will now be 2 here
```

변수는 목록 안의 값을 나타내기도 한다. 이 경우 변수가 확장될 때 복수의 값에서 확장될 것이다. 아래의 예시를 보자:

```cmake
# set a list of items
set (items_to_buy apple orange pear beer)

# loop over the items
foreach(item ${item_to_buy})
	message("Don't forget to buy one ${item}")
endforeach()
```

어떤 경우에는 당신의 프로젝트를 CMake의 유저 인터페이스에 있는 변수를 설정해서 빌드하기를 원할 수 있다. 그럴 경우 변수는 반드시 캐시 앤트리(cache entry)여야 한다. 언제든 CMake는 바이너리 파일이 작성된 곳의 디렉터리에 캐시 파일을 생산할 수 있다. 몇 가지 캐시에 대한 목적이 있다. 첫 번째는 유저의 선택권의 저장하고 정함으로서, CMake를 다시 실행하여 정보에 다시 재진입(reenter)하지 않게 해 준다. 예를 들어 `option` 명령어는 부울(Boolean) 값 변수를 생성하며 캐시에 저장한다.


```cmake
option(USE_JPED "Do you want to use the jpeg library")
```

위의 라인은 `USE_JPEG`라는 변수를 생성하고 캐시에 넣는다. 이 방법으로 유저는 변수를 유저 인터페이스로 설정하고 이것의 값이 유저가 CMake를 다시 실행할 수 있도록 해 준다. 캐시의 변수를 생성하는 것은 `option, find_file` 명령어를 사용할 수 있다는 의미이며, 기본 명령어 `set`을 `cache` 옵션과 함께 사용할 수 있다는 의미이다.

```shell
set (USE_JPEG ON CACHE BOOL "include jpeg support?")
```


cache 옵션을 사용할 때 반드시 변수의 타입과 문자열 도큐멘테이션(documentation)을 넘겨야 한다. 변수의 타입은 어떻게 설정되고 나타내지는지 GUI로 알 수 있다.

변수의 타입은 `BOOL, PATH, FILEPATH`그리고 `STRING`이 있다. 문서화된 문자열은 온라인으로 도움을 주기 위해 GUI에서 사용된다.


캐시의 다른 목적은 결정하기 힘든(expensive) 키 변수를 저장하기 위해서이다. 이런 변수는 사용자로부터 보이지 않거나 "조절 가능(adjustable)"하진 않을 것이다. 일반적으로 이러한 값은 CMake의 컴파일과 값을 결정하고 프로그램을 실행하는 `CMAKE_WORDS_BIGENDIAN` "시스템 의존 변수(system dependent variables)"이다. 이러한 값이 결정되었으면, 매번 CMake가 실행될 때마다 재계산되는 것을 막기 위해, 이들을 cache 안에 저장된다. 일반적으로 CMake는 변수 제한을 통해 프로퍼티가 바뀌지 않게 하려고 한다(such as the byte order of the machine you are on). 만약 중요한 변화가 컴퓨터에서 일어난다면, 이와 같이 운영체제가 바뀐다면, 아니면 컴파일러가 뒤바뀐다면, 캐시 파일을 삭제할 필요가 있다(그리고 아마 모든 바이너리 트리의 오브젝트 파일(object file), 라이브러리, 실행파일도 포함될 것이다).

캐시에 있는 변수 또한 향상된(advanced) 것인지 아닌지 알리는 프로퍼티를 가지고 있다. 기본적으로 CMake GUI가 실행될 때 (cmake-gui나 ccmake 같은 것이) "향상된 캐시 앤트리(advanced cache entries)"는 나타나지 않는다. 이는 유저가 바꾸기를 고려해야 할 때 캐시 앤트리에 캐시 앤트리에 집중할 수 있다. 향상된 캐시 앤트리는 유저가 수정할 수 있다면 다른 옵션으로 선택할 수 있지만, 대부분은 그렇게 하진 않는다. ==50 혹은 그 이상의 소프트웨어일 경우 일반적인 것은 아니지만, 향상된 프로퍼티가 소프트웨어를 key 설정으로 나누어 대부분의 유저와 향상된 옵션ㅇ== 프로젝트에 따라 향상된 캐시 앤트리가 아닐 수도 있다. 향상된 캐시를 만드는 `mark_as_advanced` 명령어는 캐시 향상을 위해 이름 변수(또는 캐시 앤트리)와 같이 사용된다.

어떤 경우 캐시 엔트리를 "사전 정의된(predefined)" 것으로 설정으로 제한하고 싶을 것이다. `STRING`을 캐시 앤트리에 사용함으로서 이를 설정할 수 있다. 아래의 CMaKeLists코드는  프로퍼티 이름을 `CRYPTOBACKEND` 와 같이 일반적으로 생성하고, `STRINGS`프로퍼티를 설정하여 3가지 옵션을 설정할 수 있게 한다. 

``` CMake
set (CRYPTOBACKEND "OpenSSL" CACHE STRING "Select a cryptography backend")

set_property(CACHE CRYPTOBACKEND PROPERTY STRINGS "OpenSSL" "LibTomCrypt" "LibDES")

```

cmake-gui가 작동되고 유저가 `CRYPTOBACKEND` 캐시 앤트리가 선택될 때, 아래로 내려보면 어떤 옵션을 하는지에 따라 바뀌는지, [Figure 6]를 보면 알 수 있다.

- [Figure 6]: Cache Value Options in cmake-gui

[그림]
몇몇의 마지막 포인트는 변수를 고려하여 만들어져야 하고 캐시를 통해 상호작용한다. 만약 변수가 캐시 안에 있다면, 이는 아직 `set`명령어로 `CACHE` 옵션 없이 CMakeLists 파일 안에서 재작성(overriden)될 수 있다. 캐시 값은 오로지 변수가 CMakeLists 파일의 프로세싱이 시작되기 전에 현`cmMakefile` 변수가 없을 때 확인될 수 있다. `set` 명령어는 현 CMakeLists 파일(그리고 평소대로 서브디렉토리를)을 프로세싱하는 변수를 캐시의 값을 바꾸지 않고 설정한다.

```cmake
# assume that FOO is set to ON in the cache

set(FOO OFF)
# sets foo to OFF for processing this CMakeLists file
# and subdirectories; the value in the cache stays ON
```

변수가 캐시 안에 있다면, 이것의 "캐시" 값은 일반적으로 CMakeLists 파일을 통해 수정될 수 없다. 이유는 한번 CMake가 변수를 캐시에 "초기값(initial vlaue)"을 추가하게 되면, 유저는 아마 값을 GUI에서 수정하려고 할 것이다. 만약 다음 CMake의 "발동(invocation)"이 변화를 `set`값을 통해 "덮어쓰기(overwrote)"로 되돌릴려고 할 때, 유저는 다시는 CMake를 덮어쓰기를 통해 변화를 줄 수 없을 것이다. 그래서 `set (FOO ON CACHE BOOL "doc")` 명령어는 일반적으로 캐시가 변수를 가지고 있지 않을 때 사용한다. 만약 변수가 캐시 안에 있다면, 맹령어는 어떠한 영향도 줄 수 없다.


만약 정말로 드물게 캐시의 변수 값을 바꾸고 싶다면 `FORCE` 옵션을 `CACHE` 옵션과의 조합을 통해 `set`를 사용하여 재작성과 캐시 의 변수 값을 바꿀 수 있다.


