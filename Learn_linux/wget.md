


---
# - 비대화식 네트워크 다운로더


파일을 다운로드할 때 사용되는 또 다른 유명한 커맨드라인 프로그램으로는 **wget**이 있다. 웹이나 FTP 사이트를 통해 컨텐츠를 다운로드 할 때 유용한 프로그램이다. **==단일의 파일이든 다중 파일이든 혹은 사이트 전체까지도 다운로드 할 수 있다.==** http://www.linuxcommand.org/ 사이트의 첫 번째 페이지를 다운로드하려면 다음과 같이 할 수 있을 것이다.


``` shell
┌──(kali㉿kali)-[~]
└─$ wget http://www.linuxcommand.org/                                             
--2024-03-14 20:01:15--  http://www.linuxcommand.org/
Resolving www.linuxcommand.org (www.linuxcommand.org)... 204.68.111.101
Connecting to www.linuxcommand.org (www.linuxcommand.org)|204.68.111.101|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3929 (3.8K) [text/html]
Saving to: ‘index.html’

index.html                   100%[=============================================>]   3.84K  --.-KB/s    in 0s      

2024-03-14 20:01:15 (390 MB/s) - ‘index.html’ saved [3929/3929]

                                                                  
```

wget과 다양한 옵션을 사용하면 반복적인 다운로드도 가능해진다. 백그라운드에서 파일을 다운로드한다거나(로그아웃 해도 다운로드는 계속 진행됨), 일부 다운로드된 파일의 다운로드를 완료할 수 있다. 이러한 기능에 대해서는 man 페이지에 자세히 나와있다.