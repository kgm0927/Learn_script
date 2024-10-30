
이 장에서 나온 많은 스크립트는 자립 생성형 스크립트가 아닌 함수로 작성돼 시스템호출 오버 헤드 없이 다른 스크립트에 쉽게 통합될 수 있다. C에 있는 것처럼 셸 스크립트에는 `#include` 기능이 없지만, 동일한 목적으로 사용되는 *sourcing* 파일이라는 대단히 중요한 기능이 있으므로 라이브러리 기능인 것처럼 다른 스크립트를 포함할 수 있다.

왜 이것이 중요한지 알아보기 위해 다른 것을 고려해보자. 셸 내에서 셸 스크립트를 호출하면 해당 스크립트는 기본적으로 자체 서브 셸 내에서 실행된다. 다음 테스트에서 확인해보자.

```shell
 vboxuser@Client  ~/Documents/GitHub/learning_shell_script/bin   main ±  chmod +x tinyscript.sh
 
 vboxuser@Client  ~/Documents/GitHub/learning_shell_script/bin   main ±  test=1
 
 vboxuser@Client  ~/Documents/GitHub/learning_shell_script/bin   main ±  ./tinyscript.sh
 
 vboxuser@Client  ~/Documents/GitHub/learning_shell_script/bin   main ±  echo $test
1

 vboxuser@Client  ~/Documents/GitHub/learning_shell_script/bin   main ±  


```

스크립트 *tinyscript.sh*는 변수 test의 값을 변경했지만, 스크립트를 실행하는 서브 셸에서만 변경됐으므로 셸 환경의 기존 테스트 변숫값에는 영향을 미치지 않는다. ==대신에 점(.) 표기법 사용해 스크립트를 실행하면 스크립트의 각 명령이 현재 셸에 직접 입력된 것처럼 처리된다.==

```shell
 vboxuser@Client  ~/Documents/GitHub/learning_shell_script/bin   main ±  . tinyscript.sh
 vboxuser@Client  ~/Documents/GitHub/learning_shell_script/bin   main ±  echo $test
2
```

==예상대로 exit 0 명령이 있는 스크립트는 소싱(sourcing) 하면, source 동작으로 소스 스크립트가 기본 실행 프로세스가 되기 때문에 셸을 종료하고 창에서 로그아웃한다.== 서브 셸에 스크립트가 실행 중이면, 메인 스크립트를 중지하지 않고 종료한다. 이것이 스크립트를 . 이나 source, (나중에 설명하겠지만) exec로 소싱하는 주요 차이점이자 이유다. 표기법은 실제로 bash의 source 명령과 동일하다. 서로 다른 POSIX 셸에서 이식성이 좋기 때문에 이 책에는 `.`를 사용한다.


---
### 코드

이 장의 함수를 다른스크립트에서 사용하기 위해 라이브러리로 변환하려면, 모든 함수와 필요한 전역 변수 또는 배열(즉, 여러 함수에 공통적인 값)을 추출해 하나의 큰 파일로 연결해야 한다. 이 파일을 library.sh라고 한다면, 다음 테스트 스크립트를 사용해 이 장에서 만든 모든 함수에 액세스할 수 있고, [리스트 1-28]과 같이 올바르게 동작하는지 확인할 수 있다.


```shell

```