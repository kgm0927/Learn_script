



---
# 링크 생성


ln 명령어는 하드 링크와 심볼릭 링크를 만들 때 사용한다. 다음과 같이 각각 사용하는 방법이 다르다.


ln *file link*


하드 링크를 만들 때 사용하는 것이고,


ln –s *item link*

이것은 item 파일 또는 디렉토리에 심볼릭 링크를 생성한 것이다.


- 하드 링크


링크를 생성하는 기존 유닉스 방법이지만 심볼릭 링크는 좀 더 최근의 방식이다. 기본적으로 하나의 파일에는 하나의 하드 링크가 있는데 그것이 바로 파일에 이름을 만들어주는 것이다. 하드 링크가 만들어질 때 그 파일에 대한 디렉토리가 곧바로 생성이 된다.

하지만 하드 링크에는 치명적인 약점이 2가지 있다.


| 1. 파일시스템 외부에 있는 파일을 참조할 수 없다. 다시 말해 하드 링크는 같은 디스크 파이션에 있는 파일이 아니면 참조할 수 없다는 것이다. |
| -------------------------------------------------------------------------------- |
| 2. 하드 링크는 디렉토리를 참조할 수 없다.                                                        |


하드 링크는 파일 그 자체만으로 구분해내기 어렵다. 심볼릭 링크가 있는 디렉토리 목록과 달리 하드 링크를 포함한 디렉토리 목록은 해당 링크가 가리키고 있는 것이 무엇인지 보여주지 않는다. 하드 링크가 삭제될 때, 링크도 사라지지만 파일 내용은 그 파일의 모든 링크가 삭제될 때가지 계속 남는다(즉 파일에 할당된 공간이 그대로 남는다는 의미이다).

가끔씩 하드 링크를 사용할 경우를 대비하여 하드 링크에 대해서 알아두는 것은 중요하지만 최근에는 심볼릭 링크를 좀 더 선호하고 있다. 이 부분은 뒤에서 다룬다.


- 심볼릭 링크

하드링크의 한계를 극복하기 위해서 탄생되었다. 심볼릭 링크는 참조될 파일이나 디렉토리를 가리키는 텍스트 포인트가 포함된 특수한 파일을 생성한다. 이 점에서 윈도우의 바로 가기와 아주 흡사하다.

심볼릭 링크가 참조하고 있는 파일과 심볼릭 링크 그 자체에는 서로 구분하기 힘들 정도다. 심볼릭 링크에 편집을 하게 되면 심볼릭 링크가 참조하고 있는 파일도 역시 똑같은 변경이 이루어진다.

하지만 심볼릭 링크를 삭제하는 경우에는 그 링크만 삭제되고 파일은 남아있다. 심볼릭 링크를 삭제하기 전에 파일을 지웠다면 심볼릭 링크는 살아있지만 이 링크는 아무것도 가리키지 않게 된다. ‘이러한 경우를 링크가 **깨졌다** 고 표현한다.’