
---

# 필터

파이프라인은 데이터의 복잡한 연산을 수행할 때 종종 사용된다. 하나의 파이프라인에 여러 명령어를 입력하는 것이 가능하다. 이러한 방식의 명령어들을 ‘필터’라고 한다. 필터는 받은 내용을 어떻게든 바꾸어 출력하게 한다.

첫 번째로 시도해볼 것은 **sort** 명령어다. 한 가지 상황을 상상해보자. 우리는 /bin 디렉토리와 /usr/bin 디렉토리에 있는 실행 프로그램을 하나의 목록으로 정렬한 뒤 그 목록을 보길 원한다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~$ ls /bin /usr/bin | sort |less
```

두 디렉토리를 지정했기 때문에 ls 결과로 정렬된 두 목록을 가져올 것이다. 파이프라인에 sort를 포함함으로써 하나로 정렬된 데이터 목록으로 바꿀 수 있다.