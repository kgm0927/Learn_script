
---
# scp와 [[sftp]] - 안전하게 파일 전송하기


OpenSSH 패키지에는 네트워크를 통해 파일을 복사할 때, SSH로 암호화된 터널을 활용할 수 있는 두 프로그램이 있다. 첫 번째 프로그램은 파일을 복사하는 [[cp]] 프로그램과 거의 유사하게 사용 가능한 scp(보안 복사)이다. 눈에 띌만한 차이점이 있다면 바로 목적지에 대한 경로명이나 대상이 되는 정보 앞에 원격 호스트명과 콜론 기호가 나온다는 것이다. 예를 들어 remote-sys라는 원격 시스템의 홈 디렉토리에 있는 foo.txt 문서를 로컬 시스템의 현재 작업 디렉토리로 복사하고 싶다면 다음과 같이 할 수 있다.

(현재 지금 비슷한 상황을 만들 수는 없다. 그래서 예시를 그냥 옮겨 적도록 하겠다.)


``` shell
┌──(kali㉿kali)-[~]
└─$ scp localhost:foo.txt .
kali@localhost's password: 
foo.txt                                                                          100%   88    28.0KB/s   00:00    
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ 

```

[[ssh]]로 사용하려는 리모트 호스트 계정명이 로컬 시스템 명과 맞지 않을 경우 리모트 호스트명 앞에 사용자 이름을 적용할 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ scp kali@localhost:foo.txt .
kali@localhost's password: 
foo.txt                                                                          100%   88    43.8KB/s   00:00    
                                                                                                                   

```