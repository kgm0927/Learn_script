

### 흐름 제어의 마지막 장에서는 쉘 루프의 또 다른 구성 요소를 살펴볼 것이다. ==for 루프==는반복 중에 작업 순서를 처리하는 수단을 제공한다는 점에서 [[while]]과 [[until]]루프와 차이가 있다. 이는 프로그래밍할 때 매우 유용하다는 것을 알게 될 것이다. 그래서 for 루프는 bash 스크립팅에서 매우 인기있는 구조다.

for 루프는 당연히 for 명령어로 구현된다. 최신 bash 버전은 두 가지 형식의 for 문을 제공한다.

---
# for: 전통적인 쉘 형식


for명령어의 원 문법은 다음과 같다.

```shell
for variable [in words]; do
	commands
done
```

*variable*은 루프 수행 중에 증가되는 변수명이고, *words*는 선택적인 *variable*에 순차적으로 할당되는 항목의 목록이다. 그리고 *commands*는 각 반복마다 실행되는 명령들이다.

for 명령어는 커맨드라인에서 유용하다. 그것이 어떻게 동작하는지 쉽게 알 수 있다.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ for i in A B C D; do echo $i; done
A
B
C
D

```

이 예제에서 for 명령어에 네 개의 단어 목록(A, B, C, D)이 주어진다. 네 단어 목록으로 루프는 네 번 실행된다. 각 루프가 실행될 때마다 단어가 변수 i에 할당된다. 루프 내에서 [[echo]] 명령어로 할당 내용을 보기 위해 i 값을 표시한다. [[while]]과 [[until]] 루프처럼 done 키워드는 루프를 닫는다.

for 문의 정말 강력한 기능은 단어 목록을 생성할 수 있는 흥미로운 방법을 상당수 제공한다는 점이다. 예를 들면, 중괄호 확장을 할 수 있다.

```shell
┌──(kali㉿kali)-[~]
└─$ for i in {A..D}; do echo $i; done 
A
B
C
D

```

또는 경로명 확장

```shell                                
┌──(kali㉿kali)-[~]
└─$ for i in distros*.txt; do echo $i; done
distros-bydate.txt
distros-dates.txt
distros-key-names.txt
distros-keyvernums.txt
distros-names.txt
distros.txt
distros-vernums.txt
distros-versions.txt
distros-version.txt
distros_version.txt
   ```


그리고 명령어 치환

```shell
#!/bin/bash

# longest-word : find longest string in a file

while [[ -n $1 ]]; do
        if [[ -r $1 ]]; then
        max_word=
        max_len=0
        for i in $(strings $1); do
                len=$(echo $i | wc -c)
                if ((len > max_len )); then
                        max_len=$len
                        max_word=$i
                fi
        done
        echo "$1: '$max_word' ($max_len characters)"
        fi
        shift
done
           
```

이 예제는 파일 내의 가장 긴 문자열을 검색한다. 커맨드라인에 하나 이상의 파일명이 주어질 때, 이 프로그램은 각 파일마다 읽을 수 있는 텍스트 "단어들"의 목록을 생성하기 위해 strings 프로그램(GNU binutils 패키지에 포함된)을 사용한다. for 루프는 각 단어를 차례대로 처리하면서 현 단어가 지금까지 발견된 가장 긴 것인지 확인한다. 루프가 완료되면 가장 긴 단어로 표시된다.

만약 `for` 명령의 *words* 부분이 생략되면, for은 위치 매개변수를 기본으로 처리한다. 우리는 이 방법을 사용하기 위해 longest-word 스크립트를 수정할 것이다.

```shell
#!/bin/bash

# longest-word2 : find longest string in a file

for i; do
        if [[ -r $i ]]; then
                max_word=
                max_len=0
                for j in $(strings $i); do
                        len=$(echo $j | wc -c)
                        if (( len > max_len )); then
                                max_len=$len
                                max_word=$j
                        fi
                done
                echo "$i: '$max_word' ($max_len characters)"
        fi
done

```

이처럼, 루프의 가장 바깥쪽을 [[while]]에서 for로 변경하였다. for 명령어에서 단어 목록이 생략되었기 때문에 그 대신 위치 매개변수를 사용한다. 루프 안쪽은 이전 변수 i가 변수 j로 교체되었다. 또한 [[shift]]의 사용도 제거되었다.

>[!info] 왜 I인가?
>이 예제에서 for 루프에서 변수 i가 사용된 것을 알아차렸을 것이다. 그렇다면 왜? 사실 특별한 이유는 없고 그저 전통일 뿐이다. for에 사용되는 변수는 유효한 변수라면 상관없다. 하지만 i가 가장 흔하고 이어서 j, k가 사용된다.
>이 전통의 근간은 포트란 언어로부터 시작된다. 포트란에서 타입이 선언되지 않은 I,J,K,L,M 문자로 시작하는 변수는 자동적으로 정수형으로 간주된다. 반면 다른 문자들로 시작하는 변수들은 실수형(소수점을 가진)으로 지정된다. 이러한 동작은 프로그래머가 루프 변수에 I,J,K를 사용하게 만들었다. 임시 변수(루프 변수처럼)가 필요한 경우에 그것들을 사용하는 게 작업이 줄어들기 때문이다.

---
# for: C언어 형식

bash으 최신 버전에는 C 언어에서 사용하는 형식과 닮은 for 명령 문법의 두 번째 형식이 추가되었다. 다른 많은 언어들도 이러한 형식을 지원한다.


```shell
for ((expression1; expression2; expression3)); do
	commands
done
```

*`expression1, expression2, expression3`* 는 모두 산술식이고, *commands*는 루프의 각 반복마다 실행되는 명령들이다.

이 형식은 동작 측면에서 다음 구조와 동일하다.

```shell
(( expression1 ))
while (( expression2 )); do
	commands
	(( expression3 ))
done
```

*`expression1`* 는 루프를 위한 초기 상태이고, *`expression2`* 는 루프가 끝나는 시점을 결정하는 데 사용된다. 그리고 *`expression3`* 은 루프의 각 반복 끝 부분에서 실행된다. 

```shell
┌──(kali㉿kali)-[~]
└─$ cat simple_counter       
#!/bin/bash

# simple_counter: demo of C style for command

for (( i=0; i<5; i=i+1 )); do
        echo $i
done
       
```

실행 결과는 다음과 같다.

```shell
┌──(kali㉿kali)-[~]
└─$ ./simple_counter
0
1
2
3
4
```

이 예제의 *`expression1`* 에서는 변수 i를 0으로 초기화하고, *`expression2`* 는 i가 5보다 작은 경우에만 루프를 허용한다. 그리고 *`expression3`* 는 루프가 반복될 때마다 i의 값을 1씩 더한다.

C언어의 형식의 for문은 수열이 필요할 때 언제든지 유용하다. 이후 두 장에서 이에 대한 여러 응용을 살펴볼 예정이다.


# 마무리 노트

이제 우리는 sys_info_page 스크립트에 for 명령에 관한 지식으로 최종 개선안을 적용할 것이다. 현재 report_home_space 함수는 다음과 같다.

```shell
report_home_space(){
        if [[ $(id -u) -eq 0 ]]; then
                cat <<- _EOF_
                        <H2>Home Space Utilization (All Users)</H2>
                        <PRE>$(do -sh /home/*)</PRE>
                        _EOF_
        else
                cat <<- _EOF_
                        <H2>Home Space Uitlization ($USER)</H2>
                        <PRE>$(du -sh $HOME)</PRE>
                        _EOF_
        fi
        return
}


```

다음은 각 사용자 홈 디렉토리에 대한 자세한 정보와 파일 및 하위 디렉토리 전부를 포함하게끔 이 스크립트를 다시 작성한 것이다.

```shell
report_home_space(){
        local format="%8s%10s%10s\n"
        local i dir_list total_files total_dirs total_size user_name

        if [[ $(id -u) -eq 0 ]]; then
                dir_list=/home/*
                user_name="All Users"
        else
                dir_list=$HOME
                user_name=$USER
        fi

        echo "<H2>Home Space Utilization ($user_name)</H2>"

        for i in $dir_list; do

                total_file=$(find $i -type f | wc -l)
                total_dirs=$(find $i -type d | wc -l)
                total_size=$(du -sh $i | cut -f 1)
                echo "<H3>$i</H3>"
                echo "<PRE>"
                printf "$format" "Dirs" "Files" "Size"
                printf "$format" "----" "-----" "----"
                printf "$format" $total_dirs $total_files $total_size
                echo "</PRE>"
        done
        return
}

```

이것은 지금까지 우리가 배웠던 많은 것들을 적용한 것이다. 우리는 여전히 슈퍼유저로 테스트를 한다. 하지만 if문에서 완전한 동작을 수행하는 대신에 for 루프에서 추후 사용될 변수들을 설정한다. 그리고 여러 지역 변수들을 함수에 추가하여 [[printf]]를 사용하여 출력의 일부를 포맷했다.