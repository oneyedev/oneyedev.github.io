# Unity test runnner references error
Unity에서 test를 작성하다보면, Unity기본기능 뿐아니라 사용자가 직접 작성한 스크립트를 테스트해야하는 경우가 생긴다. 코드편집기에서는 별 문제없는데, Unity Editor로 넘어와 `Test runner`를 실행하려고 하니 `CS0246` error가 발생하였다.

## `CS0246` error


```sh
error CS0246: The type or namespace name <module> could not be found
(are you missing a using directive or an assembly reference?)
```

Reference를 못 찾는다는 에러다. 아무래도 새로 작성한 스크립트는 Dependency를 직접 지정해주어야 하는것 같다.

## Solution
1. 제품 스크립트 폴더로 가서 우클릭
1. `Create` → `Assembly Definition` → 이름 입력 후 파일 생성
1. 테스트 스크립트 폴더의 `Assembly Definition` 파일 클릭
1. 우측 속성창에 `Assembly Definition References`의 `+` 버튼 클릭 → 위에서 작성한 파일 이름 지정
1. `Apply` 버튼 클릭

> 테스트 스크립트를 처음 작성하는 경우 `Window` → `General` → `Test Runner`에서 만들면 된다. 자세한 내용은 [Unity Test Runner](https://docs.unity3d.com/Manual/testing-editortestsrunner.html) 참조.

잠시 후, 컴파일 에러가 사라지면서 `Test Runner` 창에 테스트항목들이 정상적으로 표시되었다.