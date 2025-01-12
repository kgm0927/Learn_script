"빌드 구성(Build Configurations)"은 프로젝트가 다른 방식으로 디버그, 최적화(optimized), 또는 그 어떤 다른 플래그 설정을 할 수 있도록 해 준다. CMake는 default, Debug, Release, MinSizeRel, 그리고 RelWithDebInfo 구성으로 저원한다. 디버그(Debug)는 기본 디버그 플래그를 실행한다. 공개(Release)는 기본적인 최적화를 한다. MinSizeRel은 가장 작은 크기의 오브젝트 코드를 생산하지만 빠른 코드는 아니다. RelWithDebInfo 빌드는 "디버그 정보(debug information)"과 같이 최적화 빌드를 제공한다.


CMake는 약간 다른 방식으로 실행기가 사용되는 방법에 따라 구성을 손보게 한다. 가능하면 "네이티브 빌드 시스템 모음(The conventions of the native build system)"을 읽어보기 바란다. 이 말은 구성이 Makefile과 비주얼 스튜디오 프로젝트파일을 사용할 때 다른 방식으로 빌드할 때 영향을 미친다는 의미이다.

비주얼 스튜디오 IDE는 빌드 구성에 대한 관념(혹은 설명, notion)을 지원한다. 보통 비주얼 스튜디오의 디폴트 프로젝트는 디버그와 "공개 구성(Release configurations)"이 있다. IDF에서는 "빌드 디버그(build)"를 선택할 수 있고, 파일이 "디버그 플래그(Debug flags)"를 통해 빌드될 수 있다. IDE는 모든 바이너리 파일을 "활동 구성(active configuration)"의 이름과 함께 디렉토리 안에 넣는다. 이는 추가적인 복잡성을 프로젝트에 가져올 수 있는데, 빌드 프로그램이 커스텀 명령어를 통해 빌드 프로그램이 빌드 프로세스로서 실행될 필요가 있기 때문이다. `CMAKE_CFG_INTDIR` 변수와 커스텀 명령어 색션에서 더 많은 정보를 통해 어떻게 다루는지 알아보아라. 변수 `CMAKE_CONFIGURATION_TYPES`는 CMake가 어떤 구성이 워크스페이스 놓여 있는지 알려줄 것이다.


Makefile 기반의 생성기는, 오로지 하나의 생성기가 CMake가 작동될 때 실행되며, 이는 `CMAKE_BUILD_TYPE` 변수에 구체화되어 있다. 만약 변수가 비어 있다면 어떤 플래그도 빌드하기 위해 있지 않는다. 만약 변수가 "구성(configuration)"의 이름을 설정해 놓는다면, 적당한 변수와 "규칙(rules)"이 (예를 들어 `CMAKE_CXX_FLAGS_<ConfigName>`) 컴파일 라인에 더해진다. Makefile은 특별한 구성 서브디렉토리를 오브젝트 파일을 위해서는 사용하지 않는다. 디버그와 "제공된 트리(release trees)"를 빌드하기 위해, 유저는 복수의 빌드 디렉토리를  CMake의 "아웃 오브 소스(out of source)"빌드 피쳐를 통해 생성되며, `CMAKE_BUILD_TYPE`로 원하는 선택을 각 빌드에 설정하도록 한다.

``` cmake
# With source code in the directory MyProject
# to build MyProeject-debug create that directory, cd into it and 
(ccmake ../MyProject -DCMAKE_BUILD_TYPE:STRING=Debug)

# the same idea is used for the release tree MyProject-release
(ccmake ../MyProject -DMAKE_BUILD_TYPE:STRING=Release)

```