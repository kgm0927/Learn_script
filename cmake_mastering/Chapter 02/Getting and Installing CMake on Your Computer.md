CMake를 사용하기 이전에 CMake 바이너리를 빌드 혹은 저장할 필요가 있다. 많은 시스템에서 CMake는 이미 저장되어 있거나, 아니면 일반 패키지지 관리 툴과 같이 저장이 가능할 것이다. Cygwin, Debian, FreeBSD, Mac OS X Fink 그리고 많이 CMake를 다룰 것이다. 만약 당신의 시스템이 CMake를 가지고 있지 않다면, 아주 일반적인 프로그램 아키텍처를 www.cmake.org 에서 전처리된 파일을 가지고 있다. 만약 전처리된 바이너리를 찾을 수 없다면, CMake 소스코드를 찾는 것이 좋을 것이다. CMake를 빌드하기 위해선 C++ compiler를 실행하는 것이 좋다.


### UNIX and Mac Binary Installations

만약 당신의 시스템이 표준 패키지 CMake를 제공하면, 여기에 제공하는 설치 설명서를 읽어보라. 만약 당신의 시스템이 CMake 가지고 있지 않다면, 또는 옛날 버전이라면, 상술된 사으트에서 전처리된 바이너리 파일을 다운받는 것이 좋을 것이다. www.cmake.org 에서의 바이너리는 tar 형식으로 압축된 파일이다. tar파일은 README 파일을 담고 있다. README 파일은 메니페스토 파일(manifest of the files)을 tar에 설명서와 같이 포함하고 있다. 설치를 위해 목표 파일에 동봉되어 있는 tar 파일을 추출한다(주로 `/usr/local`). 하지만 설치를 위해 root 권한이 필요가 없다면 어느 디렉토리건 상관없다.


### Windows Binary Installation

윈도우의 CMake는 NullSoft가 www.cmake.org 에서 파일 다운을 가능하게 하기 위해 고유하고 있다. 이 파일을 저장하기 위해선, 간단히 윈도우즈 머신에서 실행할 수 있도록(executable on)하여 저장할 수 있게 한다. 그러면 CMake 저장한 후에 스타트 메뉴에서 실행할 수 있게 한다.

