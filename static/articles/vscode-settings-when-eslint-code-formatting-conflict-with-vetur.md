# vscode settings when eslint code formatting conflict with vetur

vscode에서 eslint와 vetur를 사용하다보면 자동포맷기능이 겹쳐 의도치 않은 결과가 나오는 경우가 많다. 

vetur에서 제공하는 code snippets이 마음에 들어서 vetur를 삭제하지는 못하고, vetur의 formatting 기능만 끄기로 하였다. 열심히 삽질한 결과 vscode 설정을 아래와 같이 정하여 해결되었다.

## .vscode/settings.json
```json
{
    "editor.formatOnSave": true,
    "vetur.format.enable": false,
    "vetur.validation.template": false,
    "eslint.autoFixOnSave": true,
    "eslint.validate": [
        {
            "language": "vue",
            "autoFix": true
        },
        {
            "language": "javascript",
            "autoFix": true
        }
    ]
}
```
