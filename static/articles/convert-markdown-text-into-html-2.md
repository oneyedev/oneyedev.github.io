# Convert Markdown Text into HTML - 2

이번엔 showdown에 사용자정의 확장 모듈을 정의해 나만의 html을 만들어보는 방법을 알아보자. 

showdown에는 두 가지 확장 모듈 타입을 지원한다. 하나는 새로운 syntax 를 정의하게 해주는 `lang`타입, 다른 하나는 기존 syntax에 추가적인 작업을 정의하게 해주는 `output`타입이다. 여기서는 텍스트에 색을 입혀주는 `lang` 타입 확장모듈과 Github처럼 h1태그에 자동으로 링크를 걸어주는 `output`타입 확장모듈을 만들어보자.

또한 확장모듈을 정의하는 방법에는 두 가지가 존재한다.
- `regex`, `replace` - 매칭할 정규표현식(`regex`)과 치환될 문자열(`replace`)을 정의한다. Javascript String 프로토타입의 `replace` 메소드와 비슷하다. 비교적 간단한 기능일 경우 사용하기 편리하다.
- `filter` - 변환전 markdown 문자열(`text`)와 showdown에서 사용되는 변환기(`converter`) 및 옵션(`options`)을 인자로 받는 함수로 정의한다. 자유도가 높고 강력하지만 사용하기가 까다로운 방법이다. 자세한 내용은 [showdown github wiki](https://github.com/showdownjs/showdown/wiki/extensions) 참조하자.

## Preview
https://oneyedev.github.io/vue-showdown-highlight-sample#custom-extension

## Colorized Text Syntax Extension
`***[#ff0000] red color text***`라는 markdown 텍스트가 있다고 했을 때, 해당 텍스트를 `[]`안에 존재하는 hexcode color로 입혀주는 syntax 확장모듈을 정의해보자.

새로운 syntax를 정의하는 것이기 때문에 `type`은 `lang`으로 정의하고, 다음과 같이 `regex`에 찾을 정규표현식을 정의하고, `replace`에 치환된 문자열을 정의하자.

- 정규표현식 `\[(#[0-9A-F]{6})\]`은 `[]`안의 hex color code를 가리킨다. -*정규표현식의 기호들로 인해 이스케이프 문자 `\`가 들어가 조금은 알아보기 힘들다.*- 이 부분이 `replace`의 `$1`으로 자동치환 될 것이다.

- 정규표현식`(.+)`은 실제 text를 가리킨다. 이 부분이 `replace`의 `$2`로 자동치환될 것이다.

```js
// src/plugins/vue-showdown.js
import VueShowdown, { showdown } from 'vue-showdown'
const colorTextExtension = {
    type: 'lang', // define new syntax
    regex: /\*\*\*\[(#[0-9A-F]{6})\](.+)\*\*\*/g, // regex to match
    replace: '<span style="color:$1">$2</span>' // string to replaced
};
showdown.extension('colorTextExtension', colorTextExtension)
```

```html
<!-- src/App.vue -->
<vue-showdown
  :markdown="markdown"
  flavor="github"
  class="markdown-body"
  :extensions="['showdownHighlight', 'colorTextExtension']"
></vue-showdown>
```

저장 후, 실행해보면 의해 아래와 같이 변환되는 것을 볼 수 있다.

```js
***[#ff0000] red color text***
```

<center>↓</center>

```html
<span style="color:red">red color text</span>
```

<center>↓</center>

<pre>
<span style="color:red">red color text</span>
</pre>

## h1 Link Extension
이번엔 `<h1>`태그에 자동으로 `<a>`태그를 붙여주어서 markdown의 `#`이 인덱스 기능을 하도록 확장 모듈을 작성해보자. 우선 link icon을 위해 [google material-icon](https://google.github.io/material-design-icons/)을 사용하자. npm으로 설치해 사용해도 되지만, 여기선 간단히 `public/index.html`에서 추가하였다.

```html
<!-- public/index.html -->
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
```

이 모듈은 기존 syntax에 기능을 덧붙이는 영역이므로, `output`타입에 해당한다. 위에서 사용한 `regex`/`replace`로 작성할 수 있다. `output`타입으므로 `h1`같은 html 태그를 검색하는게 가능하다.

- `<h1 id="(.+)">`은 link의 주소가 될 id를 가져오는 부분이다. `replace`의 `$1`에 해당한다.
- `<h1>(.+)<\/h1>`는 실제 텍스트에 해당한다. `replace`의 `$2`에 해당한다.

> 만일 매칭된 모든 텍스트를 사용하고 싶다면, `$&`를 사용하면 된다.

- `<a href="#$1">`로 `#`링크를 걸어주었다. anchor tag가 되어 자동으로 페이지가 해당 id부분으로 이동할 것이다.
- `class="anchor"` `class="octicon octicon-link"`는 모두 `github-markdown-css`에 선언되어있는 css이다. h1태그 옆에 link icon을 위치시켜줄 것이다. 해당 css를 사용하지 않는다면 위치를 직접 지정해주어야 한다.
- `<i style="visibility: visible" class="material-icons">link</i>` [google material-icon](https://google.github.io/material-design-icons/)의 링크아이콘을 선언하는 부분이다. 기본적으로 `class="octicon-link"`에 의해 hover시에만 보여지도록 되어있어 여기서는 link가 존재함을 보여주기 위해 style로 해당 속성을 덮어씌웠다. 원치 않으면 해당 속성을 삭제하면 된다.

```js
const h1LinkExtension = {
  type: 'output', // add feature to existing syntax
  regex: /<h1 id="(.+)">(.+)<\/h1>/g, // you can use html regex on 'output' type
  replace: `
    <h1 id="$1">
        <a href="#$1" class="anchor">
            <i
                style="visibility: visible"
                class="octicon octicon-link material-icons"
            >
            link
            </i>
        </a>
        $2
    </h1>
  `
}
```

`filter`로 확장 모듈을 정의하는 경우를 살펴보자. 주의할 점은 `converter.makeHtml(text)`메소드에 의해 변환이 진행될 경우 filter가 재귀적으로 호출되어 자칫 무한루프에 빠지기 쉽다는 점이다. 간단한 `counter` 변수를 만들어 무한루프에 빠지지 않도록 방지하자. 정규표현식으로 할 수 있는 변환외의 것을 하고 싶다면, `filter` 함수 안에서 사용하면 된다.

```js
// This code also works!
let x = 0;
const h1LinkExtension = {
  type: 'output', // add feature to existing syntax
  filter: function (text, converter) {
      if (x < 1) {
          ++x
          const html = converter.makeHtml(text)
          return html.replace(/<h1 id="(.+)">(.+)<\/h1>/g, `
            <h1 id="$1">
                <a href="#$1" class="anchor">
                    <i
                        style="visibility: visible"
                        class="octicon octicon-link material-icons"
                    >
                    link
                    </i>
                </a>
                $2
            </h1>`)
      }
      return text;
  }
}
```

둘 중에 어느 방식으로든 정의해서 등록 후, 사용하면 된다.
```js
// src/plugins/vue-showdown.js
import VueShowdown, { showdown } from 'vue-showdown'
showdown.extension('h1LinkExtension', h1LinkExtension)
```

```html
<!-- src/App.vue -->
<vue-showdown
  :markdown="markdown"
  flavor="github"
  class="markdown-body"
  :extensions="['showdownHighlight', 'colorTextExtension', 'h1LinkExtension']"
></vue-showdown>
```
