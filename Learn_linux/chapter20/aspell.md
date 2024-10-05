
마지막으로 살펴볼 툴은 대화식 맞춤법 검사기인 aspell이다. aspell 프로그램은 ispell이라는 이름의 초기 프로그램의 후계자이고 대부분은 그 대체품으로 사용될 수 있다. aspell 프로그램은 맞추멉 검사가 필요한 다른 프로그램에서 주로 사용된다. 또한 커맨드라인에서 단독 툴로서 효과적으로 사용될 수 있다. HTML 문서, C/C++ 프로그램, 이메일 메시지와 그 외 특수한 텍스트 등 다양한 종류의 텍스트 파일들을 지능적으로 검사하는 능력을 가지고 있다.

간단한 산문을 포함한 텍스트 파일의 맞춤법 검사를 위해 aspell을 다음과 같이 사용할 수 있다. 

`aspell check textfile`

*textfile*은 검사할 파일의 이름이다. 실습으로, 고의적으로 철자 오류를 포함한 foo.txt라는 간단한 텍스트 파일을 생성한다.

>[!error] 그 전에 알아야 할 것
>```
>┌──(kali㉿kali)-[~]
└─$ aspell check foo.txt
Error: No word lists can be found for the language "ko_KR".
>```
>이런 식으로 그냥 진행할 경우 문제가 생긴다. 영어 환경이 아닌 '한국어 환경'이기 때문 그래서 여기서 **환경 설정**을 해야 한다.
>```
>┌──(kali㉿kali)-[~]
└─$ sudo dpkg-reconfigure        
/usr/sbin/dpkg-reconfigure: 다시 설정할 패키지를 지정하십시오    
>
>┌──(kali㉿kali)-[~]
└─$ sudo dpkg-reconfigure locales // 현재 언어 환경을 바꿈 (한국어 설정을 위해서는 꼭 알아야 할 것)
Generating locales (this might take a while)...
  en_US.UTF-8... done
  ko_KR.UTF-8... done
Generation complete.
>```
> 이런 식으로 설정을 바꾸고, 리부트(reboot)를 해야만 한다.


``` shell

┌──(kali㉿kali)-[~]
└─$ cat > foo.txt
The quick brown fox jimped over the laxy dog.

```

다음은 aspell을 사용하여 파일을 검사할 것이다.

```shell
┌──(kali㉿kali)-[~]
└─$ aspell check foo.txt


```

aspell은 다음 화면처럼 대화식 검사 모드를 제공한다.

``` shell

The quick brown fox jimped over the laxy dog.



                                                                                                                   
1) jumped                                                6) comped
2) gimped                                                7) camped
3) limped                                                8) humped
4) pimped                                                9) impede
5) wimped                                                0) umped
i) Ignore                                                I) Ignore all
r) Replace                                               R) Replace all
a) Add                                                   l) Add Lower
b) Abort                                                 x) Exit
                                                                                                                   
? 

```

화면 상단에는 텍스트의 미심쩍은 단어를 강조 표시한다. 중간에는 0부터 9 사이의 번호로 매긴 10개의 추천 절차와 이어서 가능한 동작 목록을 표시한다. 마지막으로 가장 하단에는 명령을 선택할 수 있는 프롬프트가 표시된다.

만약 1을 입력하면, aspell은 문제가 되는 단어를 jumped로 교체하고 그 다음 절차가 틀린 단어인 laxy로 이동한다. 만약 lazy를 선택하면 aspell은 그것을 교체하고 종료한다.  aspell이 종료된 후, 파일을 확인하고 틀린 철자가 올바르게 고쳐진 것을 볼 수 있다.

```shell
┌──(kali㉿kali)-[~]
└─$ cat foo.txt         
The quick brown fox jumped over the lazy dog.    
```

커맨드라인 옵션 `--dont-backup`을 통해 지정하지 않는 한, aspell은 원본에 .bak 확장자를 추가한 백업 파일을 생성한다.

sed의 편집 솜씨를 뽐내기 위해, 이전 파일에 잘못된 철자를 집어넣어 다시 사용할 것이다.


``` shell
                                             
┌──(kali㉿kali)-[~]
└─$ sed -i 's/lazy/laxy/; s/jumped/jimped/' foo.txt

```

파일을 "제자리" 편집하려면 sed에 i 옵션을 사용해야 한다. 이는 편집 결과가 표준 출력이 아닌 변경될 그 파일 자신에 다시 쓰인다는 것을 의미한다. ==또한 세미콜론으로 구분된 행에서 하나 이상의 편집 명령을 놓을 수 있는 기능을 가지고 있다.==


다음은 aspell이 어떻게 다양한 종류의 파일을 처리할 수 있는지 살펴볼 것이다. [[chapter12 VI 맛보기|vim]](도전적인 이들은 sed를 사용하길 원할 수도 있다)과 같은 텍스트 편집기를 사용하여 파일에 HTML 마크업을 추가할 것이다.


``` shell
<html>
        <head>
                <title>Mispelled HTML file</title>
        </head>
        <body>
                <p> The quick brown fox jimped over the laxy dog.</p>
        </body>
</html>      


```


이제, 수정된 파일로 맞춤법 검사를 하게 되면 문제가 보일 것이다. 이렇게 실행하면 된다.

```shell
┌──(kali㉿kali)-[~]
└─$ aspell check foo.txt

```

실행 결과는 다음과 같다.

``` shell

<html>
        <head>
                <title>Mispelled HTML file</title>
        </head>
        <body>
                <p> The quick brown fox jimped over the laxy dog.</p>
        </body>
</html>


                                                                                                                   
1) HTML                                                  6) Hormel
2) ht ml                                                 7) HDMI
3) ht-ml                                                 8) Tamil
4) HTML's                                                9) hotly
5) hotel                                                 0) Hummel
i) Ignore                                                I) Ignore all
r) Replace                                               R) Replace all
a) Add                                                   l) Add Lower
b) Abort                                                 x) Exit
                                                                                                                   
? 

```

aspell은 HTML 태그의 내용을 잘못된 철자로 보여줄 것이다. 이 문제는 -H(HTML) 검사 모드 옵션으로 해결할 수 있을 것이다.

``` shell
┌──(kali㉿kali)-[~]
└─$ aspell -H check foo.txt
            
```

결과는 다음과 같다.

``` shell


<html>
        <head>
                <title>Mispelled HTML file</title>
        </head>
        <body>
                <p> The quick brown fox jimped over the laxy dog.</p>
        </body>
</html>


                                                                                                                 
1) Misspelled                                            6) Misapplied
2) Dispelled                                             7) Miscalled
3) Mi spelled                                            8) Respelled
4) Mi-spelled                                            9) Misspell
5) Spelled                                               0) Misled
i) Ignore                                                I) Ignore all
r) Replace                                               R) Replace all
a) Add                                                   l) Add Lower
b) Abort                                                 x) Exit
                                                                                                                   
? 

```

좀 전의 HTML은 무시되고 파일의 마크업이 아닌 부분만 검사하게 된다. 이 모드에서는 HTML 태그의 내용은 무시되고 철자 검사를 받지 않는다. 하지만 ALT 태그의 내용은 이 모드에서도 검사하게 된다.

---
# 마무리 노트


이 장에서 우리는 텍스트를 조작하는 커맨드라인 툴의 일부를 살펴보았다. 다음 장에서는 더 많은 것을 살펴볼 것이다. 인정컨대, 반실용적인 예제를 보여주려고 노력했지만 이 툴을 어떻게 사용할 수 있고 매일매일 왜 사용하는지 즉각 와 닿지 않을 것이다. 우리는 뒷장에서 실전 문제를 해결하기 위한 도구 집합을 토대로 구성된 툴을 발견하게 될 것이다. 이러한 툴을 특히 쉘 스크립트와 같은 경우에 그 진정한 가치를 보여줄 것이다.

---
# 추가 학습

좀 더 연구할 가치가 있는 흥미로운 텍스트 조작 명령어들이 몇몇 있다. 이들 중에는 split(파일을 분리), csplit(파일을 문맥 기반으로 분리), sdiff(파일 차이점을 나란히 결합)가 있다.