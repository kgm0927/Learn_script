
당신이 CMake로 project를 실행할 때 이미 빌트 인이 되어 있을 것이다. 만약 당신의 타겟 생성기가 Makefile 기반이면 당신은 프로젝트를 디렉토리를 바이너리 트리로 바꾸고 make라는 타이핑으로 바꿀 수 있을 것이다(아니면 qmake 아니면 nmake이 적당할 것이다). 만약 당신이 파일들을 IDE 가령 비주얼 스튜디오 같은 곳에 생성한다면, 프로젝트를 파일 안에 로드할 것이면 당신의 바람대로 잘 빌드될 것이다.


또 다른 옵션은 CMake 빌드 옵션을 커맨드라인에 사용하는 것이다. IDE 사용이 요구되는 방식이지만, 이러한 옵션은 간단히 당신의 프로젝트를 커맨드라인에서 빌드할 수 있게 하는 편의를 제공한다. 코맨드라인  옵션 `-`를 포함한다.

```shell
Usage: cmake --build <dir> [options] [-- [native-options]]
Options:
<dir>            = Project binary directory to be built.
--target <tgt>   = Build <tgt> instead of default targets.
--config <cfg>   = For multi-configuration tools, choose <cfg>.
--clean-first    = Build target 'clean' first, then build.
					(To clean only, use --target 'clean'.)
--               = Pass remaining options to the native tool.
```


그래서 당신이 비주얼 스튜디오를 생성기로 사용하여도 커맨드라인을 통해 당신의 프로젝트를 원하는 대로 빌드할 수 있다.

```shell
cmake --build <your binary dir>
```

이것이 CMake를 설치와 실행을 간단한 방법으로 하는 방법이다. 다음 챕터에서는 CMake를 더 자세하고 어떻게 더 복잡한 프로젝트를 하는데 사용할 지 알아볼 것이다.