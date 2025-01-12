##### CMake를 위한 초깃값(Initial Values) 설정

CMake가 상호작용 모드에서 잘 작동될 때, 가끔 당신은 캐시 앤트리를 GUI에 실행시키지 않고 설치할 필요가 있다. 이는 nightly dashboards(정확한 의미 모름)을 설정하거나 만약 같은 캐시 값으로 많은 빌드 트리를 생성할 때 종종 있는 일이다. 이러한 케이스는 CMake 캐시는 두 가지 방법으로 초기화할 수 있다. 첫 번째는 캐시 값을 CMake 커맨드라인인 `-DCACHE_VAR:TYPE=VALUE` 인자를 사용하는 것이다. 예를 들어, 아래의 nigthly dashboard 스크립트를 유닉스 머신으로 구성할 때 이러하다:

```shell
#!/bin/tcsh

cd ${HOME}

# wipe out the old binary tree and then create it again

rm -rf Foo-Linux
mkdir Foo-Linux

cd Foo-Linux

# run cmake to setup the cache
cmake -DBUILD_TESTING:BOOL=ON <etc...> ../Foo

# generate the dashboard
 ```

윈도우즈의 배치 파일(batch file)와 같은 형태이다. 두 번째 방법은 CMake의 `-c`옵션을 통해 로드를 하기 위해 파일을 생성을 하는 것이다. 이 경우 캐시를 -D 옵션으로 설정하는 대신 CMAke로 구문 분석된 파일을 통해 캐시를 수행한다. 이러한 구문의 파일은 CMakeLists 구문의 기준이며 전형적인 `set` 명령어의 나열이다. 

``` shell

# Build the vtkHybrid kit.
set (VTK_USE_HYBRID ON CACHE BOOL "doc string")
```

어떤 경우에는 아마 캐시와 당신이 원하는대로 캐시값을 특정 방향으로 설정하게 할 수 있을 것이다. 예를 들어 당신이 이전 유저들이 CMake를 사용하고 꺼 놨음에도 불구하고, 하이브리드(Hybrid)를 작동시키고 싶을 때 처럼 말이다. 그러면 이렇게 할 수 있다.

```shell
#Build the vtkHyhrid kit always.
set (VTK_USE_HYBRID ON CACHE BOOL "doc" FORCE)
```

아마 당신은 캐시 파일을 바로 수정하거나, 프로젝트 '초기화'를 초기 캐시 파일을 넘기는 방법으로 할 욕구가 있을 것이다. 이는 작동하지 않을 것이며 미래에 추가적인 문제를 일으킬 가능성이 있다. 먼저 CMake 캐시의 구문은 변화의 주제이다. 두 번째로 캐시 파일은 절대 경로명(full paths)를 통해 이들을 바이너리 트리 사이에 이동하기 적절하지 않게 만든다. 그러니 만약 캐시 파일을 초기화하고 싶다면, 위의 설명한 두 개의 방법중 하나를 사용하는 것이 좋을 것이다.

