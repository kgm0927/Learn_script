
---
# 프로그램 정보 표시


GNU 프로젝트는 info 페이지라는 [[man]] 페이지의 대안을 제공한다. info 페이지는 info 라는 읽기 프로그램으로 볼 수 있다. info 페이지는 웹 페이지처럼 ‘하이퍼링크’되어 있다.


``` shell
File: dir, Node: Top, This is the top of the INFO tree.

This is the Info main menu (aka directory node).

A few useful Info commands:

'q' quits;

'H' lists all Info commands;

'h' starts the Info tutorial;

'mTexinfo RET' visits the Texinfo manual, etc.

* Menu:

(그 외 생략)
```


info 프로그램은 info 파일을 읽는데, 이 파일은 개별 노드마다 트리 구조화되어 있고 각각 개별 주제를 포함하고 있다. info 파일에는 노드 간에 이동할 수 있는 하이퍼링크를 제공한다.

하이퍼링크는 `*` 기호를 시작으로 하는 것을 구별할 수 있고 하이퍼링크 커서를 두고 enter 키를 입력하면 바로 활성화된다.


info 명령을 실행하기 위해서는 info와 프로그램명을 입력하면 된다. 밑의 표는 info 페이지를 보는 동안 리더(reader) 제어를 위해 사용되는 명령어 목록이다.



[표 5-2] info 명령어

| 명령어                   | 실행                            |
| --------------------- | ----------------------------- |
| ?                     | 명령어 도움말 보기                    |
| PAGE UP 또는 BACKSPACE  | 이전 페이지 보기                     |
| PAGE DOWN 또는 Spacebar | 다음 페이지 보기                     |
| n                     | 다음-다음 노드 보기                   |
| p                     | 이전-이전 노드 보기                   |
| u                     | 위로-현재 표시된 노드의 상위 노드(주로 메뉴) 보기 |
| ENTER                 | 현재 커서 위치에 있는 하이퍼링크 이동하기       |
| q                     | 종료하기                          |


지금까지 우리가 논의한 대부분의 커맨드라인 프로그램은 GNU 프로젝트의 coreutiles 패키지의 일부분이다. 따라서 이 패키지를 입력하면 더 많은 정보를 얻을 수 있다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ info coreutils
```


coreutiles 패키지가 제공하는 프로그램들의 문서로 이동할 수 있는 하이퍼링크를 포함한 메뉴 페이지를 보여줄 것이다.


### README 및 기타 프로그램 문서 파일들


사용자 컴퓨터에 설치된 대부분의 소프트웨어 패키지들은 문서 파일을 가지고 있는데 이는 /usr/share/doc 디렉토리 내에 존재한다. 대부분은 일반적은 텍스트 파일 형식으로 저장되고 less 명령어로 열어 볼 수 있다.

일부 파일들은 HTML 포맷으로 되어 있어 웹 브라우저를 통해서도 확인 가능하다. .gz 확장자를 사용하는 파일들은 [[gzip]] 압축 프로그램으로 압축된 파일임을 나타낸다. gzip 패키지에는 zless 라고 불리는 [[less]]의 특별한 버전이 있는데 이것은 gzip으로 압축된 텍스트 파일의 내용을 표시해준다.