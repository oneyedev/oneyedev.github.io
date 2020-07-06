# Javascript path alias intellisense in VS Code
Vue Framework에서는 모듈 임포트시 `@/`가 `src/`로 치환된다. 
Code editor에서 path 자동완성을 지원해주지 못해 불편하던 중 해결 방법을 발견하여 공유한다.

## Solution
프로젝트 root폴더에 jsconfig.json 파일을 만들고 아래와 같이 옵션을 추가해주면 된다.
```json
{
    "compilerOptions": {
        "target": "es6",
        "baseUrl": ".",
        "paths": {
            "@/*": [
                "src/*"
            ]
        }
    },
    "exclude": [
        "node_modules",
        "dist"
    ]
}
```
> 빠른 자동완성을 위해 `node_modules`이나 `dist`같은 폴더는 제외하는 것을 권장한다.