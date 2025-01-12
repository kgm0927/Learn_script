
'소스 파일 구조체(source file structure)'는 타겟과 많이 비슷하다. 파일이름, 확장(extension), 그리고 수 많은 '소스 코드와 연관된 일반적인 프로퍼티'(general properties related to a source file)'를 저장한다. 타겟처럼 `set_source_files_properties`와 `get_source_file_property`를 사용하여 프로퍼티를 얻거나 설정할 수 있거나, 더 많은 제네릭 버전(generic versions)을 구할 수 있다. 가장 흔한 프로퍼티는 이러하다:


#### COMPILE_FLAGS

컴파일 플래그는 소스 코드를 구체화한다. 이것들은 소스를 구체화하는 `-D`와 `-I` 플래그를 포함한다.

#### GENERATED

`GENERATED` 프로퍼티는 소스 파일이 빌드 프로세스 일부에서 생성된다는 것을 알린다. 이 경우 CMake는 소스 파일이 없을 때 의존성 계산(computation of dependencies)을 위해 CMake를 첫 번째로 실행시키는데, 이는 냐하면 다른 방식으로 다루기 위해서이다.

#### OBJECT_DEPENDS

추가적인 파일을 소스 파일에 추가하는 것은 반드시 의존성에 따라야 한다. CMake는 자동적으로 일반적으로 쓰는 C, C++ 그리고 포트란(Fortan) 의존성 정하기 위해 의존성을 실행한다.
이러한 파라미터(parameter , 매개변수)는 "일반적이지 않은(unconventional)" 의존성이나 소스코드가 의존성 분석을 하는 도중에 존재하지 않는 곳에 드물게 사용된다.




### ABSTRACT (추상화)

##### WARP_EXCLUDE

CMake는 곧바로 프로퍼티를 사용하진 않는다. CMake에 있는 일부 로딩된 명령어와 확장은 어떻게 그리고 언제 C++ 클래스를 다른 언어 Tcl, Python 같은 언어들에 감쌀지(wrap)를 결정할 지 계속 주시한다.