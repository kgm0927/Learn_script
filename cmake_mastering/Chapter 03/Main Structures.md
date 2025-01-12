
이번 챕터에서는 CMake의 중요 컨셉에 대해서 설명해 볼 것이다. 이를 시작하기 앞서 다양한 타겟, 생성기 그리고 명령어를 통하여 CMake를 사용해 볼 것이다. CMake의 이러한 컨셉은 C++ 클래스와 많은 CMake의 명령어의 참조를 통해 이행된다. 이러한 컨셉들의 이해는 실행 관련 지식은 


CMake의 세부적 사항에 들어가기 앞서 이들 간의 기본적인 구조(relationships)에 대해 알 필요가 있다. 아주 저수준(lowest level) 소스 파일을 확인할 것이다. 이는 C와 C++ 소스 코드와 일치한다. 소스 코드는 타겟과 결합되어 있다. 타겟은 일반적으로 실행파일(executable, exe)아니면 라이브러리(library)이다. 하나의 디렉토리는 소스 트리안에 있는 디렉토리를 대표하며 일반적으로 CMakeLists 파일 그리고 하나 아니면 그 이상의 타겟과 연관되 있다. 모든  디렉토리는 그 디렉토리를 위한 Makefile 이나 프로젝트 파일(project file)의 생성을 책임지는(responsible for) 지역(local) 생성기를 소유하고 있다. 모든 지역 생성기는 빌드 프로세스를 감독(oversee)하는 전역 생성기(global generator)를 공유하고 있다. 마지막으로, 전역 생성기는 `cmake` 클래스 자체로부터 생성되고 추진(driven)된다.


Figure 4 기본 클래스 CMake 구조를 보여준다. 우리는 CMake의 컨셉(concept)에 대해 좀 더 세부적으로 알아볼 것이다. CMake의 실행은 cmake 클래스의 인스턴스를 생성함과 커맨드라인 인자를 전달하면서 시작된다. 이 클래스는 전체적으로 구성 프로세스(configuration process)를 관리하며 캐시 값과 같은 빌드 프로세스로부터 전역 상태인 정보들을 유지하게 한다.  `cmake` 클래스가 하는 첫 번째 일들 중 하나는 알맞은 전역 생성기를 유저의 선택에 따라 만드는 것이다(비주얼 스튜디오 10, Borland Makefiles 아니면 UNIX Makefile). 이 시점에서 cmake 클래스는  configure(명령어)와 생성기 메서드를 통해 생성된 전역 생성기에 통제권(control)를 넘긴다.


- [Figure 4]: CMake Internals



전역 생성기는 프로젝트를 위해 구성관리와 모든 Makefile (또는 project file) 생성에 책임이 있다. 실제 상황에서(in practice) 모든 일은 사실 전역 생성기에서 생성된 지역 생성기에서 마무리된다. 하나의 지역 생성기는 실행된 프로세스의 각 디렉토리를 위해서 생성된다. 그 때까지 프로젝트는 많은 지역 생성기를 위해 오로지 하나의 전역 생성기가 필요하다. 예를 들어 비주얼 스튜디오 7의 전역 생성기는 전역 생성기가 각각의 디렉토리 안에 있는 타겟을 지역 생성기가 프로젝트 파일을 위해 생성하는 동안 전체 프로젝트를 위해 솔루션 파일(solutions file, sln)을 생성한다.

"Unix Makefile" 생성기 같은 지역 생성기는 대부분의 Makefile 파일과 전역 생성기의 프로세스를 조화시키고 메인 탑(main top) 레벨의 Makefile 파일을 생성한다. 이러한 세부적인 이행은 생성기들 사이에서도 매우 다르다(vary). 비주얼 스튜디오 6 생성기는 `.dsp` 그리고 `.dsw`같은 파일 템플릿을 이용(make use of)하며 다양한 대체제를 사용하기도 한다. 비주얼 스튜디오 7의 생성기는 후에 어떠한 템플릿 사용 없이 XML 출력을 생성하기도 한다. Makefile 생성기는 UNIX, NMake, Borland 그 외 여러가지 들은 템플릿을 설정하거나 Makefile를 생성하면서 대체제를 사용하기도 한다. 


- [Figure 5]: Sample Directory Tree

``` mermaid

flowchart TD
Top[Top]--> Mid1[Mid1]
Top-->Mid2[Mid2]
Mid2-->Sub3[Sub3]
Mid1-->Sub1[Sub1]
Mid1-->Sub2[Sub2]
```


각각의 지역 생성기는 cmMakefile 라는 클래스 인스턴스를 가지고 있으며, cmMakefile은 CMakeLists가 파싱(parsing)된 결과가 저장된 곳이다. ==특히 프로젝트 안에 있는 디렉토리는== 이는 빌드 시스템에는 Makefile을 사용하지 않는 것이라는 것이 더 명백해진다. 그 인스턴스는 모든 파싱된 디렉토리의 CMakeLists파일의 정보들을 유지한다. 한 가지 cmMakefile 클래스로부터 생각해 볼 것은 몇 가지 부모 디렉토리의 변수로부터 초기화(initialized)된 구조체(structure)로 볼 수 있으며, 이 후에 실행된 CMakeLists 파일로 채워져 있다는 것이다. CMakeLists를 읽는 것은 단순히 CMake 실행이 명령을 통해 접촉하면서 찾는 것이다(의역, 생략이 약간 있음).

각 CMake의 명령어는 분리된 C++로부터 이행되며, 두 개의 메인 파트(main parts)이 있다. 첫 번째 파트의 명령어는 InitialPass 메서드이다. 이 메서드는 인자와 현재 디렉토리를 위해 진행하고 있는 `cmMakefile` 인스턴스를 받고, 그 후 이를 실행한다. 이러한 경우에는 `set` 명령어는, 인자를 실행하며 만약 인자가 맞다면 메서드를 변수를 설정하기 위해 `cmMakefile`에 있는 메서드를 호출한다. 명령어의 결과는 항상 `cmMakefile` 인스턴스에 저장한다. 정보는 절대 명령어에 저장되지 않는다. 마지막 부분의 명령어는 FinalPass이다. 이 명령어는 모든 명령어 뒤에서 실행될 수 있으며 (CMake 프로젝트 전체에) InitialPass를 적용한다. 대부분의 명령어는 FinalPass가 따라붙지는 않지만, 일부 특별한 케이스의 명령어는 initialpass가 되지 않는 전역 정보를 위해 반드시 따라붙여야 한다.

모든 CMakeLists 파일은 생성기가 `cmMakefile`인스턴스에서 타겟 빌드 시스템을 위한 적당한 파일을 생성하기 위해 정보를 수집하여 이를 통해 실행된다.



