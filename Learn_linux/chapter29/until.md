
until 명령어는 0이 아닌 종료 상태를 만났을 때 루프를 종료하는 대신에 계속 수행된다는 것만 제외하고 while과 동일하다. **until 루프**는 종료 상태 값으로 0을 받을 때까지 계속된다. 우리의 **while-count** 스크립트에서는 count 변수의 값이 5보다 작거나 같은 동안에 루프를 반복시킬 수 있다. until을 이용해서 똑같은 결과를 만들 수 있다.

```shell
#!/bin/bash

# until-count: display a series of numbers


count=1

until [ $count -gt 5 ]; do
        echo $count
        count=$((count + 1))

done
echo "Finished"


```

[[test]] 표현식을 `$count -gt 5`로 변경함으로써 until 명령은 적시에 루프를 종료시킨다. [[while]] 또는 unitl 중에서 어떤 것을 사용할지 결졍하는 것은 스크립트에 따라 가장 확실한 테스트를 할 수 있는 것을 택하는 문제에 달려 있다.