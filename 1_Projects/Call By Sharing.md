#callbysharing #callbyreference #callbyvalue

일단 아래의 코드를 보자.

```python
# set variable
a = { 'name': 'sean' }
print(a, id(a))
# {'name': 'sean'} 2287444788416

# test1
def test1(x):
    x['name'] = 'mike'
test1(a)
print(a)
# {'name': 'mike'}

# test2
def test2(x):
    x = { 'name': 'dora' }
test2(a)
print(a)
# {'name': 'mike'}

# test3
b = 'sean'
def test3(x):
    x = 'mike'
test3(b)
print(b)
# sean
```

set variable
딕셔너리 a를 세팅한다.

test1
함수 test1를 선언한다. 함수 안에서 파라미터로 온 딕셔너리의 name 컬럼 값을 변경한다.
인자로 보내졌던 a의 컬럼도 변경된다.

test2
함수 test2를 선언한다. 함수 안에서 파라미터로 온 딕셔너리와 똑같은 포맷으로 세로 세팅하고, name 컬럼값만 변경한다.
인자로 보내졌던 a에는 변경되지 않는다.

test3
스트링 b를 세팅한다.
함수 test3을 선언한다. 함수 안에서 파라미터로 온 스트링을 수정한다.
인자로 보내졌던 b에는 변경되지 않는다.

결론
함수 안의 파라미터는 Call By Reference처럼 보이나, 그냥 같은 주소값을 가지는 딕셔너리를 카피한 것이다. 주소 값은 같으니 안에서 컬럼 값을 변경해도 a가 같이 변경되는 것이다. (test1)

하지만 엄밀하게 밖에서 a가 안에서의 x와는 다른 변수이기 때문에 새로 딕셔너리를 세팅하면 전혀 다른 객체가 되고 원래 a에는 영향이 없다. (test2)

스트링은 Callbyvalue이기 때문에 그냥 새로운 값이 수정된다.