
### 간단한 텍스트 포매터


fmt 프로그램도 텍스트를 자른다. 게다가 더 많은 것을 할 수 있다. 파일이나 표준 입력을 허용하고 텍스트 열의 문장 포맷을 지정한다. 기본적으로, 공백 줄과 들여쓰기를 유지하면서 텍스트를 합치거나 채운다.


이를 확인하기 위해서는 텍스트가 조금 필요할 것이다. fmt 정보 페이지에서 일부를 가져와보자.

``` shell
        `fmt' reads from the specified FILE arguments (or standard input if none
are given), and writes to standard output.

        By default, blank lines, spaces between words, and indentation are
preserved in output; successive input lines with different
indentation are not joined; tabs are expanded on input and introduced on
output.


        `fmt' prefers breaking lines at the end of a sentence, and tries to avoid
line breaks after the first word of a sentence or before the last word of a
sentence. A "sentence break" is defined as either the end of a parapgraph or a
word endig in any of `.?!', followed by two spaces or end of line, ignoring
any intervening parentheses or quotes. Like TeX, `fmt' reads entire 
"paragraphs" before choosing line breaks; the algorithm is as varient of that
given by Donald E. Knuth and Michael F. Plass in "Breaking Paragraphs Into
Lines", `Software--Practice & Experience' 11, 11 (November 1981), 1119-1184.
                                                                                 
```

우리는 이 텍스트를 텍스트 편집기에 복사하고 fmt-info.txt 파일로 저장할 것이다. 자, 이제 이 텍스트를 50자 너비 기준으로 맞추기 위해 다시 포맷을 설정한다고 치자. 이를 위해 fmt를 -w 옵션과 함께 사용하여 처리할 수 있다.


``` shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ fmt -w 50 fmt-info.txt | head
        `fmt' reads from the specified FILE
        arguments (or standard input if none
are given), and writes to standard output.

        By default, blank lines, spaces between
        words, and indentation are
preserved in output; successive input lines with
different indentation are not joined; tabs are
expanded on input and introduced on output.


```

음, 뭔가 어색한 결과가 나왔다. 아마도 우리는 다음과 같은 텍스트를 원했을 것이다.

```
By default, blank lines, spaces between words, and indentation are
preserved in output; successive input lines with different indentation
are not joined; tabs are expanded on input and introduced
 on output.
```

그런데 fmt는 첫째 줄 들여쓰기를 유지하고 있다. 다행히도, fmt는 이를 바로 잡을 옵션을 제공한다.

```shell
┌──(kali㉿kali)-[~]
└─$ fmt -cw 50 fmt-info.txt      
        `fmt' reads from the specified FILE
arguments (or standard input if none are given),
and writes to standard output.

        By default, blank lines, spaces between
words, and indentation are preserved in output;
successive input lines with different indentation
are not joined; tabs are expanded on input and
introduced on output.


        `fmt' prefers breaking lines at the end
of a sentence, and tries to avoid line breaks
after the first word of a sentence or before
the last word of a sentence. A "sentence break"
is defined as either the end of a parapgraph or
a word endig in any of `.?!', followed by two
spaces or end of line, ignoring any intervening
parentheses or quotes. Like TeX, `fmt' reads
entire "paragraphs" before choosing line breaks;
the algorithm is as varient of that given by
Donald E. Knuth and Michael F. Plass in "Breaking
Paragraphs Into Lines", `Software--Practice &
Experience' 11, 11 (November 1981), 1119-1184.
                                                  
```

훨씬 낫다. -c 옵션의 추가로 원하는 결과를 얻을 수 있게 됐다.

fmt는 [표 21-3]처럼 흥미로운 옵션들이 있다.


- [표 21-3] fmt 옵션


| 옵션          | 설명                                                                                                                                                                                                   |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -c          | **crown margin** 모드로 동작하게 된다. 이는 문단 첫 두 줄의 들여쓰기를 유지한다. 그 다음 줄부터는 두 번째 줄의 들여쓰기                                                                                                                        |
| -p *string* | *string*을 접두어로 줄을 시작하게 만든다. 그 이후에 *string*의 내용은 각 줄 앞에 붙여진다. 이 옵션은 소스 코드 주석을 구성하는 데 사용될 수 있다. 예를 들면, 어떤 프로그래밍이나 설정 파일은 `#`문자를 주석 처리하는데 사용한다. 이를 위해 `-p ' #'` 옵션을 사용하면 주석으로 만들 수 있다. 다음 예제를 참고하라.<br> |
| -s          | 분할 모드. 이 모드에서는 각 줄은 "열 너비에 딱 맞게" 분할될 것이다. 짧은 줄은 너비를 채우기 위해서 합쳐지지 않을 것이다. 이 모드는 합쳐지길 원하지 않는 코드와 같은 텍스트를 구성할 때 유용하다.                                                                                   |
| -u          | 간격을 균등하게 유지한다. 이는 전통적인 타자기 스타일의 텍스트를 구성하게 될 것이다. 이는 단어 사이는 하나의 공백, 문장 사이는 두 개의 공백으로 처리한다. 이 모드는 강제로 왼쪽과 오른쪽 여백을 정렬하는 양쪽 정렬을 제거하려고 할 경우 유용하다.                                                         |
| -w *width*  | *width* 값을 기준으로 열을 구성한다. 기본값은 75자다.                                                                                                                                                                  |
| ^           | ^ **저자주**: fmt는 열을 균등하게 맞추기 위해 실제로 지정된 너비보다 약간 짧게 구성한다.                                                                                                                                              |

  특히 *-p* 옵션은 좀 더 흥미롭다. ==만약 모두 동일한 문자열로 시작하는 행들로 구성되어 있다면 이 옵션을 사용해서 파일의 선택 영역을 구성할 수 있다.== 대다수의 프로그래밍 언어는 해시(#) 기호를 주석의 시작으로 사용한다. 그래서 이 옵션을 사용하여 만들 수 있다. 주석을 사용하는 프로그램을 시뮬레이션하는 파일을 만들어보자.

``` shell
┌──(kali㉿kali)-[~]
└─$ cat > fmt-code.txt
# This file contains code with comments.

# This line is a comment.
# Followed by another comment line.
# And another.

This, on the other hand, is a line of code.
And another line of code.
And another.                                                                                                                   

```

이 샘플 파일은 `#` 문자열(공백 포함)로 시작하는 "코드" 열을 가지고 있다. 이제 fmt를 사용하여 코드 영역은 건드리지 않고 주석 영역만을 구성할 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ fmt -w 50 -p '# ' fmt-code.txt     
# This file contains code with comments.

# This line is a comment.  Followed by another
# comment line.  And another.

This, on the other hand, is a line of code.
And another line of code.
And another.      
```

==여러분은 주석 부근의 줄만 합쳐진 것을 알아차렸을 것이다.== 반면 긴 줄과 지정된 접두어로 시작하지 않은 줄들은 그대로 유지되었을 것이다.

