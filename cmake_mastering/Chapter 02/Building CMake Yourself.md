
만약 바이너리가 시스템에서 작동되지 않는다면, 아니면 바이너리가 원하는대로 CMake가 사용될 수 없다면, 소스코드를 빌드하여 CMake를 사용할 수 있게 한다. www.cmake.org 에 있는 설명서를 통해 CMake의 소스 코드를 얻을 수 있다. 소스코드를 얻으면 두 가지 방법으로 빌드할 수 있다. 만약 컴퓨터에 알맞은 CMake의 버전을 설치하면 다른 버전의 CMake를 빌드할 수도 있다. 일반적으로 현 버전의 CMake는 항상 이전의 버전의 CMake로부터 빌드된다. 이는 어떻게 새 버전의 CMake가 대부분의 윈도우즈 시스템에 빌드되는지 보여준다.

두 번째 방법은 CMake가 부트스트랩 빌드 스트립트로 빌드되는 방법이다. 이를 위해 CMake 소스 디렉토리와 타입으로 바꾸어야 한다.

```shell
./boostrap
make
make install
```

make(파일 프로그램) 설치 방법은 CMake가 빌드 디렉토리에서 실행될 때부터 선택적이다. UNIX에서는 만약 GNU C++ 컴파일러를 사용하지 않고 있다면, 부트스트랩을 통해 어떤 컴파일러에 사용하고 싶은지 말 해야 할 것이다.환경변수 `CXX`를 부트스트랩을 실행하기 전에 설정하면 모든 것이 끝난다. 만약 컴파일러에 특수 플래그(sepcial flags)가 필요하다면, `CXXFLAGS` 환경 변수를 설정한다. 예를 들어 SGI의 7.3X 컴파일러에선, 이러한 형식으로 CMake를 빌드해야 할 것이다.

```shell
cd CMake
( setenv CXX CC; setenv CXXFLAGS "-LANG:std"; ./bootstrap)
```

