

CMakeList 파일을 작성하는 많은 방법은 간단한 언어로 작성하는 것과 같다. 대부분의 CMake 언어는 사용자 혼자의 방식으로 해결할 수 있도록 '흐름 제어 구조'(flow control structures)을 제공한다. CMake는 세 가지 흐름 제어 구조를 제공하고 있다:

- 조건(혹은 조건부) 구문 (conditional statements) (e.g. `if` )
-  루프(반복) 구조 (looping constructs) (e.g. `foreach` 그리고 `while`)
- 프로시저(주로 void 형 함수) 정의 (procedure definitions) (`macro` 그리고 `function`)

첫 번째로 우리는 `if` 명령어를 다룰 것이다. CMake에서의 `if` 명령어를 다루는 방법은 다른 `if` 명령어와 같다. 이는 이것의 표현과 코드 자체의 실행 또는 공식적인 `else` 절의 코드를 평가한다. 예를 들어:

```cmake
if (FOO)
	# do something here
else (FOO)
	# do something else
endif (FOO)
```

한 가지 알아두어야 할 다른 점은 조건부인 `if` 구문은 `else`와 `endif` 절 사이에서 반복된다. 이는 이 책에서 공식적이며 두 가지 스타일의 예시를 볼 수 있다. 어떤 방식을 사용할 것인지 정하면 된다:

```cmake
if (FOO) 
	# do something here
else ()
	# do something else
endif()
```

당신이 `else`나 `endif`와 같은 절을 포함할 때, 이는 추가적인 에러 확인도 포함한다. 이렇듯 이 구문은 반드시 `if` 구분과 맞아야 한다. 아래의 구문을 통해 작동이 되지 않는 코드를 살펴보자:

```cmake
set(FOO 1)

if (${FOO})
	# do something
endif (1)
	# ERROR, it doesn't match the original if conditional
	
```

운이 좋게도 CMake는 `if`문이 `endif`로 매치되지 않을 때 장황(?)(verbose) 에러 메시지를 제공한다. 이는 조건 매치과 관련된 어떤 문제도 찾아(track down)낼 수 있게 도와준다. 조건 구문의 `else`와 `endif` 명령어 또한 CMakeLists 파일에 추가적인 도움을 준다. 긴 `if` 구문에서는 어떤 `endif`가 `if`로 종료되는지 쉽게 찾을 수 있게 해 준다. `if`구문은 깊이(depth)에 상관없이 포함되어 있을 수 있으며, 어떤 명령어도 `if`나 `else`안에서 사용할 수 있음을 알 수 있다.

다른 많은 언어와 함께 사용할 때, CMake는 `elseif`를 통해 복수의 구성을 순차적으로 테스트할 수 있다. 예를 들어:

```cmake
if (MSVC80)
	  # do something here
elseif (MSVC90)
	# do something else
elseif (APPLE)
	# do something else
endif ()
```

`if`명령어는 사용할 수 있는 오퍼레이션(operation) 설정이 제한되 있다. `$ {FOO} && ${BAR} || ${FUBAR} ` 같은 전형적인 C언어 스타일 구현은 지원하지는 않지만, 대부분의 경우에 작동되는 제한적인 부분 표현에는 지원한다. 특히 `if`는



- `if (variable)`

만약 변수의 값이 비어있지 않을 때 ,True, `0`, `FLASE`, `OFF`, or `NOTFOUND`.

- `if (NOT variable)`

만약 변수의 값이 비어있다면, True, `0`, `FALSE`, `OFF`, or `NOTFOUND`.

- `if (variable1 AND variable2)`

만약 두 변수가 각각 같다면 True.

- `if (variable1 OR variable2)`

만약 둘 중에 하나라도 TRUE라면 True.

- `if(COMMAND command-name)  `

만약에 주어진 이름이 적용될 수 있는 명령어라면 True.


- `if(DEFINED variable)`

만약 값에 상관없이 주어진 변수가 설정되었다면 True.


- `if(EXISTS file-name)`
- `if(EXISTS directory-name)`

만약 적힌 파일이나 디렉토리가 존재하면 True.

- `if(IS_DIRECTORY name)`
- `if(IS_ABSOLUTE name)`

만약 주어진 이름이 디렉토리 이거나, 적대적 경로명(absolute path)라면 true.

- `(name1 IS_NEWER_THAN name2)`

만약 구체화된 파일 name1이 name2보다 더 이른 시간에 수정(modification)되었다면 True.


- `if(variable MATCHES regax)`
-  `if(string MATCHES regax)`

만약 주어진 문자열 또는 변수의 값이 주어진 일반적인 표현과 일치하면 True.


`EQUAL`, `LESS` 그리고 `GENEATER`등등의 옵션은 숫자 비교(Numeric comparisons)할 때 사용가능하다. `STRLESS`, `STREQUAL`, 그리고 `STRGREATER`는 사전적 비교(lexicographic comparisons)하는 데 사용할 될 수 있다. `VERSION_LESS`, `VERSION_EQUAL`, 그리고 `VERSION_GREATER`는 버전들을 형식 `major[.minor[.patch[.tweak]]]`을 통해 구별하는 것이 가능하다. C와 C++ 처럼 이러한 표현들은 같이 더 강력한 조건문(conditionals)을 생성하기 위해 결합될 수 있다. 예를 들어 아래의 조건문(conditionals)을 다루어 보자:


``` cmake

if((1 LESS 2) AND (3 LESS 4))
	message("sequence of numbers")
endif()

if (1 AND 3 AND 4)
	message ("series of true values")
endif (1 AND 3 AND 4)

if (NOT 0 AND 3 AND 4)
	message ("a false value")
endif (NOT 0 AND 3 AND 4)

if (0 OR 3 AND 4)
	message("or statements")
endif (0 OR 3 AND 4)

if(EXISTS ${PROJECT_SOURCE_DIR}/Help.txt AND COMMAND IF)
	message ("Help exists")
endif (EXISTS${PROJECT_SOURCE_DIR}/Help.txt AND COMMAND IF)
set (fooba 0)

if (NOT DEFINED foobar)
	message("foobar is not defined")
endif (NOT DEFINED foobar)

if (NOT DEFINED fooba)
	message ("fooba not defined")
endif (NOT DEFINED fooba)
```

복합 `if` 구문은 평가될 오퍼레이션이 구체화될 '순서'(order of precedence)를 정한다. 예를 들어 아래의 구문을 보면, `NOT`는 `AND`보다 먼저 평가되며, 그 반대로는 할 수 없다. 즉 구문은 거짓이 되거나, 복사될 수 없다. `AND`실행은 true일 시 제일 첫 번째로 실행된다.


```cmake
if (NOT 0 AND 0)
	message("This line is never executed")
endif (Not 0 AND 0)
```

CMake는 '삽입어구'(parenthetical)로 제시된 그룹을 첫 번째로, `EXISTS`, `COMMAND`, `DEFINED`와 그리고 비슷한 접두(prefix) 연산자를 다음으로, 그리고 `EQUAL`, `LESS`, `GREATER`, `STREQUAL`, `STRLESS`, `STRGREATER`그리고 `MATCHES` 연산자를 다음으로 순서를 정하여 '혼합 계산'(order of operatioin)을 정의한다. `NOT` 연산자가 다음으로 평가되고, 마지막으로 `AND`와 `OR` 연산자 표현이 평가된다. 같은 순위의 연산자들끼리는, `AND`이나 `OR`같은 경우, 왼쪽에서 오른쪽으로 평가된다. 한번 모든 표현이 평가가 된다면 마지막 결과는 True인지 False인지 테스트를 거치게 될 것이다. CMake는 다음에 오는 모든 값이 사실인지 간주할 것이다: `ON, 1, YES, TRUE, Y`. 다음의 값은 거짓으로 간주될 것이다: `OFF, 0, NO, FALSE, N, NOTFOUND, *-NOTFOUND, IGNORE`. '이 테스트는 대소문자를 구분하지 않으므로'(This test is case insensitive) True, True 및 True는 모두 동일하게 취급된다.


이제 흐름 제어 명령어를 다루어 보자. `foreach`, `while`, `macro`와 `function` 명령어는 CMakeLists 파일의 용량을 줄이거나 유지보수 가능하게 만들기 탁월하다. `foreach` 명령어는 CMake 명령어의 묶음을 반복적으로 리스트에서 실행할 수 있도록 해 준다. 아래에 VTK에으로부터 적용된 예시를 보도록 하자:

```cmake
foreach(tfile
	TestAnisotropicDiffusion2D
	TestButterWorthLowPass
	TestButterworthHighPass
	TestCityBlockDistance
	TestConvolve
)
add_test (${tfile}-image ${VTK_EXECUTABLE}
		${VTK_SOURCE_DIR}/Tests/rtImageTest.tcl
		${VTK_SOURCE_DIR}/Tests/${tfile}.tcl
		-D ${VTK_DATA_ROOT}
		-V Baseline/Imaging/${tfile}.png
		-A ${VTK_SOURCE_DIR}/Wrapping/Tcl
		)
endforeach ( tfile )

```

`foreach`첫 번째 인자 명령어는 '이터레이션(순차적) 반복'(iteration of the loop)을 각각의 값을 가지는 변수의 이름이다. 남은 인자들은 반복을 통하여 리스트의 값을 정한다. 이번 예에는 `foreach`의 '구문 내용'(body)는 CMake 명령어, `add_test`이다. `foreach` 루프의 구문 내용은 루프 변수를 언제건 루프를 돌려 리스트에 있는 값으로 현재 값을 대체하는 방식으로 참조되도록 한다. 첫 번째 순회에선, `${tfile}` 발생(occurrences)이 `TestAnisotropicDiffusion2D`로 대체된다. 다음 순회에선, `${tfile}`가 `TestButterWorthLowPass`로 대체된다. `foreach`루프는 모든 인자가 진행된 될 때까지 반복을 계속할 것이다.

