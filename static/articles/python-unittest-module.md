# Python unittest module

프로젝트 루트폴더에 테스트 코드와 소스코드가 동시에 있으면 간단히 실행할 수 있지만, 각각 다른 폴더로 분리한 경우 약간의 추가작업이 필요하다. 테스트 파이썬 버전은 3.7이다.

## Folder strucutre
```
project-root
⌙src
  ⌙module.py
⌙test
  ⌙__init__.py
  ⌙test_module.py
```
`src`폴더와 `test`폴더를 만들어 소스코드와 테스트코드를 분리하고, `test`폴더에는 빈 파일 `__init__.py`를 만들어 모듈로 취급되도록 만들자.

> 테스트 파일명은 `test`로 시작해야한다. 만약 원치않을 경우 하단의 [discover](#discover-unit-test)를 참조.

## Type source code

간단한 plus연산 함수를 만든다.

```python
# src/module.py
def plus(a, b):
    return a + b
```

## Type test code
이를 테스트하는 코드를 테스트 폴더안에 작성해보자.

```python
# test/test_module.py
import unittest
from src import module

class TestName(unittest.TestCase):
    def test_3_plus_4_equals_7(self):
        self.assertEqual(module.plus(3, 4), 7)

```

테스트 클래스내의 메소드명은 `test`로 시작해야한다. 그렇지 않으면 인식되지 않는다.

## Run unit test

아래와 같이 터미널에서 실행하면 테스트가 실행되는 것을 볼 수 있다.

```sh
python -m unittest
```

```sh
Ran 1 test in 0.000s
```

## Discover unit test

만일 어떤 이유에선지 `__init__.py`가 없다던가, 테스트 파일 형식을 바꾸고 싶다면, `discover`를 명시적으로 선언하여 실행해야한다.

```sh
python -m unittest discover -s test_directory -p "*_test.py"
```
위의 명령은 `test_directory`라는 폴더에서 `_test.py`로 끝나는 파일들을 테스트코드로 실행하라는 뜻이다.

자세한 사항은 [파이썬 공식 문서](https://docs.python.org/ko/3/library/unittest.html)를 참조.