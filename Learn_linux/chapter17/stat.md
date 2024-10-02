

놀이터에 file-A라는 이름의 파일을 100개 만들었는데, 한번 확인해보자.

``` go
┌──(kali㉿kali)-[~]
└─$ find playground -type f -name 'file-A'
playground/dir-089/file-A
playground/dir-066/file-A
playground/dir-022/file-A
playground/dir-040/file-A
playground/dir-015/file-A
playground/dir-063/file-A
playground/dir-087/file-A
playground/dir-018/file-A
playground/dir-074/file-A
playground/dir-082/file-A
playground/dir-097/file-A

```

[[ls]]와는 달리 find 명령은 결과를 정렬하지 않는다. 정렬 기준은 저장 장치의 기준에 따른다. 다음과 같이 100개의 파일이 있음을 확인할 수 있다.

``` shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ find playground -type f -name 'file-A' | wc -l
100
                       
```


그 다음, 파일이 수정된 시간을 기준으로 파일을 검색해보자. 이 작업은 백업하거나 시간 순서대로 파일을 정리할 때 유용하다. 일단 수정 시간을 비교할 때 예제 파일을 만든다.


``` shell
┌──(kali㉿kali)-[~]
└─$ stat playground/timestamp
  파일: playground/timestamp
  크기: 0               블록: 0          입출력 블록: 4096   일반 빈 파일
장치: 8,1       아이노드: 1574675     링크: 1
접근: (0664/-rw-rw-r--)  UID: ( 1000/    kali)   GID: ( 1000/    kali)
접근: 2024-09-23 05:36:25.512717927 -0400
수정: 2024-09-23 05:36:25.512717927 -0400
변경: 2024-09-23 05:36:25.512717927 -0400
생성: 2024-09-23 05:36:25.512717927 -0400

```

만약 여기서 다시 이 파일에 touch를 실행하고 stat로 확인해보면, ==해당 파일의 시간이 업데이트 되었음을 알 수 있다.==

``` shell

┌──(kali㉿kali)-[~]
└─$ stat playground/timestamp
  파일: playground/timestamp
  크기: 0               블록: 0          입출력 블록: 4096   일반 빈 파일
장치: 8,1       아이노드: 1574675     링크: 1
접근: (0664/-rw-rw-r--)  UID: ( 1000/    kali)   GID: ( 1000/    kali)
접근: 2024-09-23 05:40:27.501664126 -0400
수정: 2024-09-23 05:40:27.501664126 -0400
변경: 2024-09-23 05:40:27.501664126 -0400
생성: 2024-09-23 05:36:25.512717927 -0400

```

다음은 놀이터에 있는 다른 파일들을 find로 업데이트 해 보자.

``` shell
┌──(kali㉿kali)-[~]
└─$ find playground -type f -name 'file-B' -exec touch '{}' ';'

```

이 명령으로 file-B라는 파일을 모두 업데이트한다. 예제 파일인 timestamp와 다른 모든 파일들을 비교하여 업데이트된 사실을 확인해보자.

``` shell
┌──(kali㉿kali)-[~]
└─$ find playground -type f -newer playground/timestamp        
playground/dir-089/file-B
playground/dir-066/file-B
playground/dir-022/file-B
playground/dir-040/file-B
playground/dir-015/file-B
playground/dir-063/file-B
playground/dir-087/file-B
playground/dir-018/file-B
playground/dir-074/file-B

```

결과는 100개의 file-B들을 모두 보여준다. timestamp 파일을 업데이트한 후에 file-B라는 이름의 파일 모두에 touch를 실행하였기 때문에, 그 파일들은 이제 timestamp 파일보다 "최근" 날짜가 적용되었고 이것을 -newer테스트로 확인해볼 수 있다.

마지막으로, 앞서 수행했던 잘못된 퍼미션에 대한 테스트를 playground 디렉토리에 한번 적용해보자.
```shell
┌──(kali㉿kali)-[~]
└─$ find playground \( -type f -not -perm 0600 \) -or \( -type d -not -perm 0700 \)
playground
playground/dir-089
playground/dir-089/file-G
playground/dir-089/file-U
playground/dir-089/file-T
playground/dir-089/file-D
playground/dir-089/file-M
playground/dir-089/file-H
playground/dir-089/file-X
playground/dir-089/file-E
playground/dir-089/file-K
playground/dir-089/file-Z
playground/dir-089/file-R

```

이 명령은 playground에 있는 100개의 디렉토리와 2600개의 파일을 표시한다(timestamp와 playground 디렉토리를 포함하여 총 2702개다). 왜냐하면 이 중 어느 것도 "올바른 퍼미션"과 일치하는 것이 없기 때문이다. 이미 알고 있는 연산자와 액션을 활용해서 놀이터에 있는 파일 및 디렉토리에 새로운 권한을 설정해보자.

``` shell
┌──(kali㉿kali)-[~]
└─$ find playground \( -type f -not -perm 0600 -exec chmod 0600 '{}' ';' \) -or \( -type d -not -perm 0700 -exec chmod 0700 '{}' ';' \)

```

그때 그때에 따라, 이 복잡한 합성 명령어보다 두 명령어를 사용하는 쉬운 방법을 발견하게 될지도 모른다. 한 명령어는 디렉토리를 위한 것이고 또 다른 하나는 파일에 적용하기 위한 것이다. 하지만 이런 방법을 아는 것도 나쁘지 않다. 여기서 중요한 것은 이런 유용한 작업을 위해 어떻게 연산자와 함께 사용되는지를 이해하는 것이다.


