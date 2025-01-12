
이제 전체적인 CMake의 실행에 대해서 논의해 볼 시간이다. `cmMakefile` 인스턴스의 핵심 키에  대해서 알아보자. 아마 가장 중요한 점은 '타겟(targets)'이다. 타겟은 실행파일(executables, exe), 라이브러리, CMake의 유틸리티를 나타낸다. 모든 `add_library`, `add_executable`, 그리고 `add_custom_target` 명령어는 타겟을 생성한다. 예를 들어 위의 명령어들은 foo라는 정적 라이브러리를 foo1.c 과 foo2.c와 같은 소스 파일처럼 생성할 것이다.

```shell
add_library(foo STATIC foo1.c foo2.c)
```

foo라는 이름은 프로젝트 어디에서건 라이브러리 이름으로 사용하기 적합하며, CMake는 어떻게 이름을 라이브러리에서 필요할 때 어떻게 확장할 지 알고 있다. 라이브러리는 STATIC (정적), SHARED(공유), MODULE(공유)와 같은 특별한 타입(자료형,type)으로 선언되거나 아니면 선언되지 않는 것으로 남겨진다. STATIC은 라이브러리에게 반드시 정적 라이브러리(static library)로 선언되라고 알린다. 비슷하게(Likewise) SHARED는 반드시 공유 라이브러리(shared library)로 선언되라고 알린다. MODULE은 실행파일에서 동적(dynamcially)으로 실행될 수 있는 라이브러리로 알린다. 많은 운영체제에는 SHARED를 사용하며, 다만 다른 Mac OS X 같은 것과는 다르다. 만약 어떤 설정도 이러한 알림에 대해서 구체화되지 않았다면 라이브러리는 공유 혹은 정적으로 빌드될 수 있다. 이러한 경우 CMake는 설정을 위한 변수 `BUILD_SHARED_LIBS`로 라이브러리가 SHARED인지 STATIC인지 결정하게 한다. 만약 정해지지 않았다면, CMake는 기본적으로 정적 라이브러리로 설정한다.


이렇듯, 실행파일은 몇 가지 설정이 있다. 기본적으로 실행파일은 전통적인 `main(int argc, const char*argv[])`을 가진 기기 '응용 프로그램'(console application)이라는 것이다. 만약 `WIN32`가 실행파일이 MS 윈도우즈에서 컴파일되어 이름을 갖게 된 후 이거나, 운영체제 시스템이 메인의 스타트업 대신 `WinMain` 호출됐을 때 구체화될 것이다. `WIN32`는 윈도우즈 시스템이 아닐 경우 효과가 없다.


추가적으로 타입을 저장하는 것은, 타겟 또한 일반적인 프로퍼티(properties, 단수 property)을 추적하는 것이다. 이러한 프로퍼티는 `set_target_properties`과 `get_target_property` 명령어를 통해 설정되거나 수거(retrieved)될 수 있으며, 또는 더 일반적인 `set_property`와 `get_property`명령어를 사용하기도 한다. 일반적으로 사용되는 프로퍼티 `LINK_FLAGS`는 링크 플래그(link flags)를 통해 특정 타겟을 구체화하기 위해 쓰인다. 타겟은 `target_link_libraries` 명령어를 사용하여 설정을 통해 링크된 라이브러리를 저장한다. 명령어의 일부가 된 이름들은 라이브러리가 되며, 고정 경로명으로 라이브러리에 기록이 되거나, 아니면 `add_library`를 통하여 라이브러리의 이름이 된다. 이들은 또한 링크 디렉토리를 연결을 위해 저장하기도 하며, 타겟의 위치를 저장하고, 연결(linking) 이후에 실행을 위해 명령어를 조작(custom, 커스텀) 하기도 한다. 

각각의 CMake 라이브러리를 생성하기 위해, 라이브러리 의존성을 통해 모든 라이브러리의 경로를 추적한다. 정적 라이브러리가 의존이 된 대로 라이브러리와 연결이 되지 않았다면, CMake에서는 라이브러리리의 경로를 통해 추적해야만 실행파일 생성을 위해 링크 라인을 구체화할 수 있다. 예를 들어,

```shell
add_library(foo foo.cxx)
target_link_libraries(foo bar)

add_executable (foobar foobar.cxx)
target_link_libraries (foobar foo)
```

foo가 암시적(implicitly)으로 foobar와 연결되어 있지만, 이는 foo 라이브러리에 연결하고 실행파일 foobar에 "경계를 설정(bar)" 설정한다. 공유된 것이나 DLL 빌드의 링킹은 항상 필요한 것이 아닌, 추가 연결에 문제가 없는 것이다. 이는 정적 빌드하는데에서 요구된다. foo 라이브러리가 바(bar) 라이브러리에 있는 심볼(symbols)을 사용하기 때문에, foobar는 가장 foo를 사용하게 되면서 반드시(most likely) 바를 사용해야 한다.