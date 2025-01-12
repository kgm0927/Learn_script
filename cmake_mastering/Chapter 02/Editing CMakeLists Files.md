

CMakeLists 파일은 거의 모든 텍스트 에디터에서 편집이 가능하다. 몇몇의 에디터 Notepad++와 같이 CMake 구문 강조(hightlighting)과 들여쓰기(indentation) 지원 빌트인을 한다. 이것들은 Docs 디렉토리의 소스 구분에서 찾을 수 있으며, CMake 웹사이트에서 다운로드 받을 수 있다. 파일 `cmake-mode.el`은 Emacs 모드이며, `cmake-indent.vim`그리고 `cmake-syntax.vim`은 Vim을 통해 쓰이곤 한다. 비주얼 스튜디오 안에 있는CMakeLists 파일은 리스트로 구성되어 있는 프로젝트의 일부이며 이들을 간단히 더블 클릭을 수정할 수 있다. 어떠한 종류의 실행기(Makefile, 비주얼스튜디오 등등)건 만약 당신이 CMakeLists 파일을 수정하거나 재실행하면, 자동으로 생성된 파일을 요구한 대로 업데이트하여 CMake에 적용시킨다. 이는 당신의 생성한 파일이 항상 CMakeLists 파일과 동기화(sync)되어 있다는 것을 확신(assure)시키게 해준다.


CMake가 의존성 정보를(dependency information)를 계산(computes)와 유지보수를 하기 때문에, CMake 실행 파일을 반드시 항상 make 파일을 만들때 항상 사용가능해야 하거나, IDE가 CMake 생성 파일에서 실행되고 있어야 한다. 이 말은 만약 CMake 입력 파일이 디스크를 바꾸어 버린다면, 당신의 빌드 시스템은 자동적으로 CMake를 재실행하거나, 최신 빌드 파일을 생산한다. 이러한 이유로 당신은 일반적으로 Makefile을 실행하거나 아니면 CMake와 CMake가 설치되지 않은 다른 기계로 설치해서 실행하면 안된다.