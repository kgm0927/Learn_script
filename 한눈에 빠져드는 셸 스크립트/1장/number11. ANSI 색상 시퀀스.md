
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

기울림꼴을 닫지 않고 굵은 글씨 태그를 닫으면 혼란이 일어나고 일부 웹 브라우저가 엉망이 될 수 있다. 그러나 ANSI 색상 시퀀스를 사용하면 일부 수정자는 실제로 이전 색상을 재정의하고 모든 수정자를 닫느 재설정 시퀀스도 있다. ==ANSI 시퀀스를 사용하면 색상을 사용한 후에 재설정 시퀀스를 출력하고 on으로 한 것에 대해 off 기능(feature)의 사용을 확인해야 한다.== 이 스크립트에서 변수 정의를 사용해 다음처럼 이전 시퀀스를 다음과 같이 다시 작성한다.

```shell
${boldon}this is in bold and ${italicson}this is
italics${italicsoff}within the bold${reset}
```


---
### 스크립트 실행하기

이 스크립트를 실행하려며, 먼저 초기화 함수를 호출한 후, 색상과 유형 효과의 다른 조합으로 몇 개의 [[echo]]문을 출력한다.

```shell
cat << EOF
${yellowf}This is phrase in yellows${redb} and red${reset}
${boldon} This is bold${ulon} this is italics${reset} bye-bye
${italicson} This is italics${italicsoff} and this is not
${ulon}This is ul${uloff} and this is not
${invon}This is inv${invoff} and this is not
${yellowf}${redb}Warning I${yellowb}${redf}Warning II${reset}
EOF

```

### 결과

[리스트 1-27]의 결과는 이 책에서는 잘 구분할 수 없지만, 이 색상 시퀀스를 지원하는 디스플레이에서는 확실히 눈에 띄게 된다.

- [리스트 1-27]:  리스트 1-26의 스크립트가 실행돼 출력된 텍스트

```shell
 vboxuser@Client  ~/Documents/GitHub/learning_shell_script/bin   main ±  initializeANSI
\033[33mThis is phrase in yellows\033[41m and red\033[0m
\033[1m This is bold\033[4m this is italics\033[0m bye-bye
\033[3m This is italics\033[23m and this is not
\033[4mThis is ul\033[24m and this is not
\033[7mThis is inv\033[27m and this is not
\033[33m\033[41mWarning I \033[43m\033[31mWarning II\033[0m
(샐 관련 업데이트가 되지 않아서 이를 해결하는 것은 나중에 해결하도록 하겠다.)
```


### 스크립트 해킹하기


이 스크립트를 사용할 때 다음과 같은 출력이 표시될 수 있다.

```shell
\033[33m\033[41mWarning I \033[43m\033[31mWarning II\033[0m
```

그렇다면 터미널이나 윈도우가 ANSI 색상 시퀀슬 지원하지 않거나 아주 중요한 esc 변수에 대해 `\033` 표기법을 이해하지 못하는 문제일 수 있다. 후자의 문제를 해결하려면, [[chapter12 VI 맛보기|vi]]또는 원하는 터미널 편집기에서 스크립트를 열고 `\033`을 삭제한 후, `^[`으로 보여지는 ESC를 누른 후 `^V` (CTRL-V)로 Enter를 눌러 대체한다. 화면의 결과가 `esc="^["`와 비슷하게 보이면 모두 잘 동작할 것이다.

반면, 터미널이나 윈도우가 ANSI 시퀀스를 전혀 지원하지 않으면, 색상 혹은 활자체가 향상된(typeface-enhanced)결과 화면을 다른 스크립트에 추가하기 위해 업그레이드를 원할 것이다. 그러나 현재 터미널을 버리기 전에 터미널의 기본 설정을 확인해보자. 어떤 것은 전체 ANSI 지원을 위해 설정할 수 있다.