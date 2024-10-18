

---

# 적합한 명령어 찾기


검색어에 따라 일치하는 명령어의 man 페이지 목록을 검색하는 하는 명령어가 있다. 비록 대략적인 정보만을 보여주지만 가끔 도움이 된다. 검색어 floppy를 사용하여 man 페이지를 찾는 예제를 살펴본다.

(제대로된 결과가 나오지 않아 bin으로 대체한다.)


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ apropos bin

bind_textdomain_codeset (3) - set encoding of message translations

bindtextdomain (3) - set directory containing message catalogs

binfmt.d (5) - Configure additional binary formats for executable...
```

출력된 내용을 첫 번째 필드에는 man 페이지의 이름이 나온다. 그리고 두 번째 필드에는 그 섹션을 보여주고 있다. **[[man]] 명령어를 –k 옵션과 함께 사용하면 apropos 명령과 동일한 기능**을 한다는 것을 명심해야 한다.