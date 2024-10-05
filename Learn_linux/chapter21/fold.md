
### 지정된 길이로 줄 나누기

**폴딩**(Folding)은 텍스트 행을 지정된 길이로 나누는 절차다. fold도 다른 명령들처럼, 하나 이상의 텍스트 파일이나 표준 입력을 허용한다. 간단한 텍스트 열을 fold에 보내면 어떻게 동작하는지 볼 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ echo "The quick brown fox jumped over the lazy dog." | fold -w 12
The quick br
own fox jump
ed over the 
lazy dog.

```

이제 동작 중인 fold를 볼 수 있다. echo 명령에 의해 전달된 텍스트는 -w 옵션에 의해 지정된 만큼 구분되어 나뉜다. 이 예제에서는 한 줄의 길이를 12자로 지정한다. ==너비를 지정하지 않으면 기본값은 80자다.== -s 옵션을 추가하면 다음처럼 줄의 끝에 도달하기 전 마지막 공백에서 자르게 될 것이다.

``` shell
┌──(kali㉿kali)-[~]
└─$ echo "The quick brown fox jumped over the lazy dog." | fold -w 12 -s
The quick 
brown fox 
jumped over 
the lazy 
dog.
```