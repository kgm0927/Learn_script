
[[chapter27 흐름 제어 if 분기|if]] 명령어와 가장 흔하게 사용되는 명령어는 단연코 test 명령어이다. test 명령어로 다양한 검사와 비교 작업을 수행할 수 있는데 두 가지 형태로 쓰인다.


`test expression`

그리고 가장 많이 쓰이는 형태인

	[expression]

`expression`에는 명령어 성공 여부를 검사하는 표현식이 들어간다. test 명령어에는 이 표현식이 참이면 0, 만약 거짓이면 1의 종료 상태 값을 반환한다.




### 파일 표현식

[표 27-1]에는 파일의 상태를 알아보는 표현식이 나와 있다.


- [표 27-1] File 표현식


| 표현식                 | 표현식 참인 경우 ...                                                            |
| ------------------- | ------------------------------------------------------------------------ |
| *file1* -ef *file2* | *file1*과 *file2*는 동일한 inode 번호를 가진다(하드 링크에 의해 이 두 파일명은 동일한 파일을 참조하게 된다.) |
| *file1* -nt *file2* | *file1*은 *file2*보다 최신이다.                                                 |
| *file1* -ot *file2* | *file1*은 *file2*보다 오래되었다.                                                |
| -b *file*           | *file*이 존재하고 이 파일은 블록 특수 파일이다.                                           |
| -c *file*           | *file*이 존재하고 이 파일은 문자 특수 파일이다.                                           |
| -d *file*           | *file*이 존재하고 이 파일은 디렉토리이다.                                               |
| -e *file*           | *file*이 존재한다.                                                            |
| -f *file*           | *file*이 존재하고 이 파일은 일반 파일이다.                                              |
| -g *file*           | *file*이 존재하고 이 파일은 setgid가 설정되어 있다.                                      |
| -G *file*           | *file*이 존재하고 이 파일은 유효 그룹 ID의 소유다.                                        |
| -k *file*           | *file*이 존재하고 이 파일은 "sticky 비트"를 가지고 있다.                                  |
| -L *file*           | *file*이 존재하고 이 파일은 심볼릭 링크다.                                              |
| -O *file*           | *file*이 존재하고 이 파일은 유효 사용자 ID의 소유다.                                       |
| -p *file*           | *file*이 존재하고 이 파일은 네임드 파이프다.                                             |
| -r *file*           | *file*이 존재하고 이 파일은 읽기 전용이다.                                              |
| -s *file*           | *file*이 존재하고 파일 크기가 0보다 큰 파일이다.                                          |
| -S *file*           | *file*이 존재하고 이 파일은 네트워크 소켓이다.                                            |
| -t *fd*             | *fd*는 터미널이 지정된 파일 디스크립터다. 이것으로 표준 입력/출력/오류의 리다이렉션 여부를 확인할 수 있다.          |
| -u *file*           | *file*이 존재하고 이 파일은 setuid가 설정되어 있다.                                      |
| -w *file*           | *file*이 존재하고 이 파일은 쓰기가 가능하다.                                             |
| -x *file*           | *file*이 존재하고 이 파일은 실행이 가능한 파일이다.                                         |

다음과 같이 파일 표현식을 확인할 수 있는 스크립트가 있다.

```shell
┌──(kali㉿kali)-[~]
└─$ cat test-file    
#!/bin/bash

# test-file: Evaluate the status of a file

FILE=~/.bashrc

if [ -e "$FILE" ]; then
        if [ -f "$FILE" ]; then
                echo "$FILE is a regular file."
        fi
        if [ -d "$FILE" ]; then
                echo  "$FILE is a directory."
        fi
        if [ -r "$FILE" ]; then
                echo "$FILE is readable."
        fi
        if [ -w "$FILE" ]; then
                echo "$FILE is writable."
        fi
        if [ -x "$FILE" ]; then
                echo "$FILE is executable/searchable."
        fi
else
        echo "$FILE does not exist"
        exit 1
fi

exit

```

이 스크립트는 `FILE` 상수에 할당된 파일을 검사하고 그 결과를 표시한다. 이 스크립트에서 살펴봐야 할 두 가지 흥미로운 점은, 첫째, `$FILE` 매개변수가 표현식 내에서 사용될 때 따옴표로 인용된다는 점이다. 꼭 따옴표를 사용해야 하는 것은 아니지만 매개변수가 빈 상태로 되지 않게 해 준다. `$FILE` 매개변수 확장이 빈 값을 반환하면 오류가 발생하기 때문이다(연산자가 아닌 null 문자열이 아닌 것으로 해석된다). 매개변수에 따옴표를 사용함으로써 문자열이 비어있더라도 연산자 다음에 해당 문자열이 따라 나온다는 것을 분명히 알 수 있다. 둘째, 스크립트 끝 부분의 `exit` 명령어는 선택적으로 하나의 명령 인자와 함께 사용될 수 있는데 이 인자는 스크립트의 종료 상태를 나타낸다. 명령 인자를 사용하지 않으면 종료 상태는 기본적으로 0이다. 이런 식으로 exit를 스트립트상에서 사용하여 `$FILE` 매개변수가 존재하지 않는 파일명을 가리킬 경우 실패를 나타내도록 한다. 이 스크립트 마지막 exit 명령어는 형식적으로 사용한 것이다. 스크립트가 끝까지 실행되면 기본적으로 종료 상태 값 0으로 스크립트가 종료된다.

이와 유사하게 쉘 함수도 정수 인자를 포함하여 return 명령어로 종료 상태 값을 반환할 수 있다. 앞의 스트립트를 더 큰 프로그램에 포함시키기 위해 쉘 함수를 바꿔서, `exit` 명령어 대신 `return` 명령어를 통해 그 결과를 확인해 볼 수 있다.

```shell
test_file(){

# test-file: Evaluate the status of a file

FILE=~/.bashrc

if [ -e "$FILE" ]; then
        if [ -f "$FILE" ]; then
                echo "$FILE is a regular file."
        fi
        if [ -d "$FILE" ]; then
                echo  "$FILE is a directory."
        fi
        if [ -r "$FILE" ]; then
                echo "$FILE is readable."
        fi
        if [ -w "$FILE" ]; then
                echo "$FILE is writable."
        fi
        if [ -x "$FILE" ]; then
                echo "$FILE is executable/searchable."
        fi
else
        echo "$FILE does not exist"
        exit 1
fi

}

```
