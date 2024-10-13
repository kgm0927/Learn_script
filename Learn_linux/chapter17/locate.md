
손쉽게 파일 찾기


locate 프로그램은 경로명에 대한 빠른 데이터베이스 검색을 수행하고 주어진 조건에 일치하는 모든 이름을 출력한다. 예를 들면, 이제 [[zip]]으로 시작하는 모든 프로그램을 찾으려고 한다. 우리는 프로그램을 찾는 것이기 때문에 bin/ 으로 끝나는 디렉토리명을 가정할 수 있다. 따라서 이런 식으로 찾아보도록 하자.

``` shell
┌──(kali㉿kali)-[~]
└─$ locate bin/zip
```

locate 프로그램은 경로명을 검색하고 bin/zip 문자열이 포함된 결과를 보여줄 것이다.

``` shell
┌──(kali㉿kali)-[~]
└─$ locate bin/zip
/usr/bin/zip
/usr/bin/zipcloak
/usr/bin/zipdetails
/usr/bin/zipgrep
/usr/bin/zipinfo
/usr/bin/zipnote
/usr/bin/zipsplit
/usr/sbin/zip2john


```

만약 검색 조건이 이처럼 간단하지 않다면, locate를 다른 명령어나 옵션과 함께 사용하여 재미있는 검색조건을 만들어볼 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ locate zip | grep bin
/usr/bin/bunzip2
/usr/bin/bzip2
/usr/bin/bzip2recover
/usr/bin/funzip
/usr/bin/gpg-zip
/usr/bin/gunzip
/usr/bin/gzip
/usr/bin/p7zip
/usr/bin/preunzip
/usr/bin/prezip
/usr/bin/prezip-bin
/usr/bin/streamzip
/usr/bin/unzip
/usr/bin/unzipsfx
/usr/bin/zip
/usr/bin/zipcloak
/usr/bin/zipdetails
/usr/bin/zipgrep
/usr/bin/zipinfo
/usr/bin/zipnote
/usr/bin/zipsplit
/usr/lib/klibc/bin/gunzip
/usr/lib/klibc/bin/gzip
/usr/lib/python3/dist-packages/binwalk/plugins/gzipextract.py
/usr/lib/python3/dist-packages/binwalk/plugins/gzipvalid.py
/usr/lib/python3/dist-packages/binwalk/plugins/ziphelper.py
/usr/lib/python3/dist-packages/binwalk/plugins/__pycache__/gzipextract.cpython-311.pyc
/usr/lib/python3/dist-packages/binwalk/plugins/__pycache__/gzipvalid.cpython-311.pyc
/usr/lib/python3/dist-packages/binwalk/plugins/__pycache__/ziphelper.cpython-311.pyc
/usr/sbin/zip2john
/usr/share/man/man1/prezip-bin.1.gz
/usr/share/metasploit-framework/vendor/bundle/ruby/3.1.0/gems/bindata-2.4.15/examples/gzip.rb
/usr/share/metasploit-framework/vendor/bundle/ruby/3.1.0/gems/rex-zip-0.1.5/bin
/usr/share/metasploit-framework/vendor/bundle/ruby/3.1.0/gems/rex-zip-0.1.5/bin/console
/usr/share/metasploit-framework/vendor/bundle/ruby/3.1.0/gems/rex-zip-0.1.5/bin/setup

```

locate 프로그램은 수년간 사용되어 왔고 여러 형태로 응용되어 사용되고 있다. 최신 리눅스 배포판에서 볼 수 있는 가장 유명한 것은 slocate와 mlocate다. 이들은 대개 locate 라는 이름의 심볼릭 링크에 의해 사용된다. locate의 다른 버전들도 동일한 옵션들을 사용하고 있다. 일부 버전은 정규 표현 검색(19장에서 다룰 예정)과 와일드카드 사용을 지원한다. locate의 [[man]] 페이지에서 locate의 어느 버전이 설치되어 있는지 확인해보도록 하자.


>[!tip] locate 데이터베이스의 정체
>이미 알고 있을지도 모르지만, 일부 리눅스 베포판에서는 시스템이 설치된 후 locate 프로그램이 제대로 작동하지 않을 수 있다. 하지만 그 다음 날 다시 시도해보면 제대로 작동한다. 무슨 일일까? locate 데이터베이스는 updatedb라는 또 다른 프로그램으로 만들어진다. 이 updatedb 프로그램은 크론 잡(cron job)으로 주기적으로 실행되는데, locate가 있는 대부분의 시스템은 하루에 한 번 이 프로그램을 실행한다. 데이터베이스가 지속적으로 업데이트되지 않았기 때문에 locate로 파일을 검색하면서 최신 파일은 검색되지 않았다는 것을 알 수 있을 것이다. 이 문제를 해결하기 위해서 슈퍼유저 권한으로 프롬프트에서 updatedb 프로그램을 실행하여 직접 업데이트할 수 있다.






