
추가적으로 타겟과 소스파일은 "아마 가끔씩은(occasionally)" 디렉토리, 생성기, 그리고 테스트와 같이 협업할 때 직접 찾아야 할 수 있다. 보통 이러한 "상호작용"(interactions)은 "형상의 설정(shape of setting)"이나 "객체로부터 프로퍼터를 구하는(getting properties from these objects) 것"이 필요하다. 모든 이러한 클래스는 이와 연관된 프로퍼티를 가지고 있는데, 소스 파일과 타겟을 실행(do)하기 위해서이다. 프로퍼티에 엑세스하는 가장 일반적인 방법은 `set_property`와 `get_property` 명령어를 통한 것이다. 이러한 명령어들은 CMake 속해 있는 어떤 클래스의 프로퍼티라도 "설정(set)" 하거나 "얻을(get)" 수 있게 해 준다. 일부 타겟을 위한 프로퍼티와 소스 파일은 이미 비공개(covered) 되었으며. 또 일부 디렉토리의 유용한 프로퍼티는 이러하다:


#### ADDITIONAL_MAKE_CLEAN_FILES

이런 프로퍼티는 "make clean" 스테이지(stage)에서 삭제(be cleaned)된 추가적인 파일을 리스트에서 구체화한다. 기본적으로 CMake는 "알려진(knows about)" 모든 생성된 파일을 지우는데, 하지만 빌드 프로세스는 남겨진 파일 뒤에 있는 다른 툴을 사용한다. 이 프로퍼티는 이러한 목록의 파일을 통해 설정되어 알맞게 지워질 수 있게 된다.


#### EXCLUDE_FROM_ALL

이 프로퍼티는 만약 모든 디렉토리의 타겟과 모든 서브 디렉토리가 "디폴트 빌드 타겟(default build target)"으로부터 제외될 수 있다는 것을 알린다. 만약 아니라면, "예시(example typing)" Makefile의 제작이 타겟에서와 같이 잘 이루어졌을 것이다. 같은 컨셉의 다른 생성기의 디폴트 빌드도 적용된다.


#### LISTFILE_STACK

이런 프로퍼티는 주로 CMake 스크립트에서 에러를 디버깅하는데 유용하다. 이것은 현재 프로세싱되는 파일의 목록을 전달하기 위해 사용한다. 그래서 만약 하나의 CMakeLists 파일이 `include` 명령어를 쓸 경우, 이는 스택(stack)에 포함된 CMakeLists 파일을 추가하는데 효율적이다. 

CMake으로부터 지원받는 프로퍼티의 목록은 `cmake`와 `-help-property-list` 옵션을 실행하면서 얻을 수 있다. 생성기와 디렉터리는 자동적으로 사용자를 위해 CMake를 실행 소스트리를 실행하면서 얻을 수 있다.


