

### 우리는 바로 이전 장에서 메뉴 방식의 프로그램을 개발하여 다양한 시스템 정보를 생성하였다. 프로그램이 작동하긴 하지만 사용하는 데 있어 여전히 심각한 문제가 존재한다. 단 한 번의 메뉴 선택 후 종료되기 때문이다. 심지어 유효하지 않은 선택이 발생하면 프로그램은 오류 메시지와 함께 종료해버리게 된다. 사용자에게 다시 선택할 수 있는 기회조차 주지 못하고 말이다. 프로그램을 다시 구성하여, 사용자가 프로그램 종료를 선택할 때까지 메뉴를 계속적으로 선택할 수 있도록 반복적으로 표시할 수 있다면 훨씬 더 편리할 것이다.



이를 위해 이번 장에는 **루핑**(looping)이라는 프로그래밍 개념에 대해 알아볼 것이다. 이는 프로그램의 일부를 반복적으로 실행할 때 사용되는 것이다. 쉘은 루프를 수행하기 위해 세 개의 합성 명령어들을 제공하고 있다. 그 중에서도 일단 두 가지만 살펴보고 나머지 한 가지는 33장에서 다루기로 한다.

---
# 루프 돌기(반복)

일상 생활은 반복적인 행동들로 이루어진다. 매일같이 출근하고 개를 산책시키고 당근을 써는 것처럼 모든 작업들은 일련의 단계들을 반복하고 있다. 당근을 써는 작업을 예로 들어보자. 이 행동을 의사코드로 표현하면 다음과 같은 단계로 구성될 수 있을 것이다.

1. 도마 준비하기
2. 칼 준비하기
3. 도마 위에 당근 올려놓기
4. 칼 들어올리기
5. 당근에 칼 대기
6. 당근 썰기
7. 모두 다 썰었으면 마치고, 그렇지 않으면 4단계로 돌아간다.

4단계에서 7단계까지는 루프 형태를 이루고 있다. 루프 내의 행동들은 "당근을 다 썰었다"라는 상태에 도달할 때까지 반복된다.

---
# while

- [[while]]


---
# until

- [[until]]

---
# 루프를 이용한 파일 읽기

[[while]] 및 [[until]] 명령으로 표준 입력을 처리할 수 있다. 즉 while과 until로 파일을 처리할 수 있다는 것이다. 다음 예제에 이전 장에서 사용했던 distros.txt 파일 내용을 표시할 것이다.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ./while-read 
Distro: SUSE    Version: 10.2   Released: 12/07/2006
Distro: Fedora  Version: 10     Released: 11/25/2008
Distro: SUSE    Version: 11.0   Released: 06/19/2008
Distro: Ubuntu  Version: 8.04   Released: 04/24/2008
Distro: Fedora  Version: 8      Released: 11/08/2007
Distro: SUSE    Version: 10.3   Released: 10/04/2007
Distro: Ubuntu  Version: 6.10   Released: 10/26/2006
Distro: Fedora  Version: 7      Released: 05/31/2007
Distro: Ubuntu  Version: 7.10   Released: 10/18/2007
Distro: Ubuntu  Version: 7.04   Released: 04/19/2007
Distro: SUSE    Version: 10.1   Released: 05/11/2007
Distro: Fedora  Version: 6      Released: 10/24/2006
Distro: Fedora  Version: 9      Released: 05/13/2008
Distro: Ubuntu  Version: 6.06   Released: 06/01/2006
Distro: Ubuntu  Version: 8.10   Released: 10/30/2008
Distro: Fedora  Version: 5      Released: 03/20/2006
   
```

파일을 루프 안으로 포함시키기 위해서 리다이렉션 연산자를 done 구문 다음에 사용하였다. 루프는 해당 파일로부터 각 항목을 입력하기 위해서 [[read]] 명령을 실행할 것이다. read 명령은 파일 끝에 도달할 때까지 종료 상태 0을 가지고 실행되다가 파일의 모든 내용을 읽고 나면 종료될 것이다. 파일의 끝에 다다르면 0이 아닌 종료 상태를 반환하고 루프를 종료할 것이다. 또한 루프와 파이프라인을 함께 사용하는 것도 가능하다.

```shell
#!/bin/bash

# while-read2: read lines from a file

sort -k 1,1 -k 2n distros.txt | while read distro version release; do
        printf "Distros: %s\tVersion: %s\tReleased: %s\n" \
                $dirstro \
                $version \
                $release
done
        
```

여기서 우리는 [[sort]] 명령의 결과를 가지고 텍스트로 표시하였다. 하지만 여기서 기억해야 할 중요한 내용은 파이프라인이 서브쉘 내에서 루프를 실행하였기 때문에, 루프 내에서 생성되고 할당된 모든 변수들은 루프가 종료될 때 함께 사라진다는 점이다.


---
# 마무리 노트

루프 입문과 더불어 분기, 서브루틴, 시퀀스까지 배운 내용을 토대로 프로그램에서 사용되는 주요한 흐름 제어 방식을 살펴보았다. bash에는 더 많은 트릭들이 존재하지만 대부분 이 기본 개념들에서 발전된 형태다.