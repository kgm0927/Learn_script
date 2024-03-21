

---


두 번째 SSH 파일 복사 프로그램은 sftp이다 sftp이란 이름은 ftp 프로그램의 보안 기능이 향상된 버전을 뜻한다.  이것 역시 ftp 프로그램과 거의 유사하게 동작하지만 평문 대신에 모든 것을 SSH로 암호화된 터널을 사용한다. 원격 호스트에 실행할 FTP 서버가 필요하지 않다는 점에서는 ftp의 이점을 사용한다. sftp는 SSH 서버만 있으면 된다. 즉 SSH 클라이언트와 연결할 수 있는 어떠한 원격 머신이든 FTP와 같은 서버로서 사용될 수 있다는 것이다.


``` shell

                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ sftp localhost              
kali@localhost's password: 
Connected to localhost.
sftp> ls
Desktop            Documents          Downloads          Music              Pictures           Public             
Templates          Videos             dirlist.txt        dirlist.xt         foo.txt            index.html         
ls-output.txt      nano.60969.save    
sftp> lcd Desktop
sftp> get ubuntu-8.04-desktop-i386.iso
File "/home/kali/ubuntu-8.04-desktop-i386.iso" not found.
sftp> bye
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ 

```
