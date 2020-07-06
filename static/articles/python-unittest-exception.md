# Python unittest exception

Python unittest에서 exception을 테스트 하는 방법을 간단히 알아보자.

우선 아래와 같이 ValueError를 일으키는 모듈이 있다고 가정하자.

```python
# module.py
def doSomething():
    raise ValueError('Error Message!')
```

테스트 코드 입장에서는 `doSomething`메소드가 `ValueError`를 일으켜야 `Success`, 일으키지 않으면 `Fail`로 취급해야 한다. 이렇게 Python의 Exception을 테스트 하는 방법은 크게 두가지가 있다.

1. [By try-except syntax](#test-exception-by-try-except)
2. [By with syntax](#test-exception-by-with)

## Test exception by try-except
```python
def test_exception_by_try_except(self):
    try:
        module.doSomething()
        self.fail("ValueError not arose")
    except ValueError as err:
        self.assertEqual(str(err), "Error Message!")
```

전통적인 try-except구문으로 exception을 잡는 방법이다. `self.fail`메소드가 없을 경우, `doSomething`메소드가 `ValueError`를 일으키지 않을시에도 테스트가 성공으로 뜨므로 주의.

## Test exception by with
```python
def test_exception_by_with(self):
    with self.assertRaises(ValueError) as err:
        module.doSomething()

    self.assertEqual(str(err.exception), "Error Message!")
```
`unittest`에서 제공하는 `assertRaises`메소드로 exception을 잡는 방법이다. 위의 방법과 달리 `self.fail`메소드가 없어도 오감지하지 않는다. 개인적으로 이 방법이 가독성이 좋아보여 추천.



