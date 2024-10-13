
---
# 파일 소유자와 그룹 변경


chown 명령어는 ==파일 또는 디렉토리 소유자와 그룹 소유자를 변경하는 데 사용된다.== 이 명령어를 사용하려면 슈퍼유저 권한이 필요하다. chown 문법은 다음과 같다.


```shell
chown [owner][:[group]] file ...
```


chown은 명령의 첫 번째 인자에 의해 파일 소유자와 또는 파일 그룹 소유자 변경할 수 있다.


[표 9-7] chown 인자 예제

| 인자       | 결과                                                      |
| -------- | ------------------------------------------------------- |
| bob      | 파일의 소유권을 현 소유자에게서 bob으로 변경한다.                           |
| bob:user | 파일의 소유권을 현 소유자에서 bob으로 변경하고 파일 그룹 소유자를 users 그룹으로 변경한다. |
| :admins  | 파일 그룹 소유자를 admins 그룹으로 변경한다. 파일 소유자는 바뀌지 않는다.           |
| bob:     | 파일 소유자가 현 소유자에서 bob으로 변경되고 그룹 소유자는 bob의 로그인 그룹으로 변경된다.  |


예시로 슈퍼유저 특권을 가진 루트(root)와 일반 사용자인 칼리(kali), 이렇게 두 명의 사용자가 있을 때, 루트는 자신의 홈 디렉토리에서 칼리의 홈 디렉토리로 파일을 복사하기를 원한다. 루트는 칼리가 편집이 가능하기를 원하기 때문에, 루트는 복사된 파일의 소유권을 루트에서 칼리로 변경한다.


``` shell
┌──(root㉿kali)-[/home/kali]

└─# ls -l

total 36

drwxr-xr-x 2 kali kali 4096 Jul 8 02:28 Desktop

drwxr-xr-x 2 kali kali 4096 Jul 8 02:28 Documents

drwxr-xr-x 2 kali kali 4096 Jul 8 02:28 Downloads

drwxr-xr-x 2 kali kali 4096 Jul 8 02:28 Music

-rw-r--r-- 1 root root 21 Jul 10 07:05 myfile.txt

drwxr-xr-x 2 kali kali 4096 Jul 8 02:28 Pictures

drwxr-xr-x 2 kali kali 4096 Jul 8 02:28 Public

drwxr-xr-x 2 kali kali 4096 Jul 8 02:28 Templates

drwxr-xr-x 2 kali kali 4096 Jul 8 02:28 Videos

┌──(root㉿kali)-[/home/kali]

└─# sudo chown kali: ~kali/myfile.txt

┌──(root㉿kali)-[~kali]

└─# sudo ls -l ~kali/myfile.txt

-rw-r--r-- 1 kali kali 21 Jul 10 07:05 /home/kali/myfile.txt
```



루트가 자신의 홈 디렉토리에서 칼리의 홈 디렉토리로 파일을 복사했다. 그 다음은 루트는 파일 소유자를 root(sudo 사용의 결과)에서 kali로 변경했다. 첫 번째 인자의 끝에 콜론(:)을 사용하여 파일 그룹 소유권 또한 칼리의 로그인 그룹인 칼리 그룹으로 변경했다.

처음 sudo 명령의 사용 이후에는, 왜 root에게 비밀번호 입력을 위한 프롬프트가 나타나지 않은 이유는, sudo의 환경설정에 “신뢰”할 수 있는 시간이 지정되어 있기 때문이다.