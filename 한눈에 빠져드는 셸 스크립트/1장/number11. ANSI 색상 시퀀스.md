
비록 깨닫지 못하더라도, 대부분의 터미널 애플리케이션은 여러 가지 스타일의 텍스트를 지원한다. 스크립트에서 특정 단어를 굵은 글씨로 표시하고 싶거나, 빨간색 배경에 노란색 배경으로 표시하고 싶은지의 여부는 조금 다를 수 있다. 그러나 ANSI(American National Standards Institute) 시퀀스를 사용해 이러한 변형을 표현하는 것은 매우 사용자 친화적이지 않기 때문에 어려울 수 있다. 간단히 하기 위해 [리스트 1-26]은 ANSI 코드를 나타내는 값 세트를 작성한다. 이 세트는 다양한 색상 및 포맷 옵션을 설정 및 해제하는 데 사용할 수 있다.

---

### 코드

- [리스트 1-26]: initializeANSI 스크립트 함수
```shell
#!/bin/bash

  

# ANSI 색상 -- 이 변수를 사용해 다양한 색상과 포맷으로 출력할 수 있다.

# 'f'로 끝나는 색상 이름은 전경색,

# 'b'로 끝나는 색상 이름은 배경색이다.

  

initializeANSI(){

esc="\033" # If doesn't work, an ESC directly.

  

# 전경색

blackf="${esc}[30m"; redf="${esc}[31m"; greenf="${esc}[32m"

yellowf="${esc}[33m" bluef="${esc}[34m"; purplef="${esc}[45m"

cyanf="${esc}[46m"; whitef="${esc}[37m"

# 배경색

blackb="${esc}[40m"; redb="${esc}[41m"; greenb="${esc}[42m"

yellowb="${esc}[43m" blueb="${esc}[44m"; purpleb="${esc}[45m"

cyanb="${esc}[46m"; whiteb="${esc}[47m"

  
  

# 굵게, 기울임꼴, 밑줄 및 반전 스타일 토글

bolden="${esc}[1m"; boldoff="${esc}[22m"

italicson="${esc}[3m"; italicsoff="${esc}[23m"

ulon="${esc}[4m"; uloff="${esc}[24m"

invon="${esc}[7m"; invoff="${esc}[27m"

  

reset="${esc}[0m"

}
```


---

### 동작 방식

HTML에 익숙하다면, 이러한 시퀀스가 동작하는 방식에 당황할지 모른다. HTML에서는 수정자(Modifier)를 반대로 순서를 열고 닫으므로 열어본 모든 수정자를 닫아야 한다. 따라서 굵게 표시된 문장 내에 기울임꼴로 된 구절을 만들려면 다음 HTML을 사용한다.

```html
<b>this is in bold and <i>this is italics</i> within the bold</b>
```

기울림꼴을 닫지 않고 굵은 글씨 태그를 닫으면 혼란이 일어나고 일부 웹 브라우저가 엉망이 될 수 있다. 그러나 ANSI 색상 시퀀스를 사용하면 일부 수정자는 실제로 