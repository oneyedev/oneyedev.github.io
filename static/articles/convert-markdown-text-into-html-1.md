# Convert Markdown Text into HTML - 1

Markdown Text는 작성시의 간편함과 HTML 과의 좋은 궁합으로 인해, readme.md와 같이 간단한 설명서를 작성할 때 자주 사용되는 언어이다 *_이 블로그의 문서 또한 Markdown으로 작성되어 배포되고 있다.* Vue Framework 환경에서 Markdown Text를 HTML로 변환해 보자. 문서는 두 번에 나누어 작성되었다. 첫 번째는 showdown과 highlight를 활용하여 Markdown Text을 Github style로 HTML을 변환할 것이며, 두 번째는 showdown의 custom extension을 적용해 자신만의 스타일을 추가하는 방법을 알아 볼 것이다.

## Preview
https://oneyedev.github.io/vue-showdown-highlight-sample/

## Installation
우리는 여기서 두 가지 유용한 오픈소스 프로젝트를 사용한다.
- showdown - Markdown Text를 HTML로 바꾸어주는 Javascript Module(http://showdownjs.com).
- highlight - 웹 상에서 syntax highlight을 도와주는 Javascript Module. 특히 code를 보여줄 때 유용하다(https://highlightjs.org).

이 두 프로젝트는 Vue가 아닌 다른 환경에서도 적용가능하지만, 우리는 Vue Framework에 좀더 쉽게 적용가능한 확장 모듈을 사용할 것이다. 원하는 프로젝트 폴더에서 아래 모듈을 설치하자. 본문은 vue-cli를 통해 default로 프로젝트를 생성했다고 가정할 것이다.

```sh
npm install vue-showdown --save
npm install github-markdown-css --save
npm install showdown-highlight --save
```

- vue-showdown - showdown을 사용하는 vue-component. 설치 시 showdown은 안에 포함되어 있다(https://vue-showdown.js.org).
- github-markdown-css - github style의 markdown css, showdown은 기본적으로 HTML구조로의 변환만 지원할 뿐, CSS가 자동 적용되진 않는다(https://www.npmjs.com/package/github-markdown-css).
- showdown-highlight - showdown에 highlight를 적용하는 extension 모듈, css까지 포함되어 있다(https://www.npmjs.com/package/showdown-highlight).

## Adding vue-showdown Plugin
기본값으로 프로젝트를 생성했다면 plugin 폴더가 따로 없을 것이다. plugins폴더를 만들고 vue-showdown을 Vue에 등록하자.

```js
// src/plugins/vue-showdown.js
import Vue from 'vue'
import VueShowdown from 'vue-showdown'
Vue.use(VueShowdown)
```

```js
// src/main.js
import './plugins/vue-showdown'
```

Component가 제대로 등록되었는지 테스트해보자. App.vue의 template 부분을 아래와 같이 바꾸고, 실행해보자.
```vue
<!-- src/App.vue -->
<template>
  <div id="app">
    <vue-showdown
      markdown="# Hello vue-showdown-highlight"
      flavor="github"
    ></vue-showdown>
  </div>
</template>
```
```sh
npm run serve
```
브라우저에서 `Hello vue-showdown-highlight` 가 h1 태그로 묶여 나오면 성공이다. showdown에 의해  # 식별자가 h1태그가 되는 것을 볼 수 있다.
<pre>
# Hello vue-showdown-highlight
</pre>

<center>↓</center>

```html
<h1>Hello vue-showdown-highlight</h1>
```

> Vue Cli로 만든 default project는 App css에 `text-align: center;`가 적용되어 있으므로 가운데 정렬을 해제하고 싶으면 해당 속성을 삭제하면 된다.

아래에 Markdown sample이 있으니 테스트시 활용하자.

<pre># This is a h1
## This is a h2 
### This is a h3
#### This is a h4
##### This is a h5
###### this is a h6
</pre>
<pre>
- This is a unordered item1
- This is a unordered item2
- This is a unordered item3

1. This is a ordered item1
1. This is a ordered item2
1. This is a ordered item3

* depth1
 * depth2
   * depth3 is not working! (maybe.. bug?)
</pre>
<pre>
[This is a hyper link](https://google.com)
![This is a image](https://picsum.photos/200/200/?random)
![This is a image alt](https://)
</pre>
<pre>
**This is a emphasizing text**
*This is a italic-style text*
> This is a block quote
</pre>
<pre><code>```
This is a code block
```

```js
const HELLO_WORLD = 'Hello World!'
```
</code></pre>

## Decorate with github-markdown-css
howdown은 기본적으로 HTML구조로의 변환만 지원할 뿐, CSS가 자동 적용되진 않는다. 설치된 `github-markdown-css`를 직접 추가하고 `vue-showdown` 컴포넌트 클래스 속성 값에 `markdown-body`를 추가하자.

```js
// src/plugins/vue-showdown.js
import 'github-markdown-css'
```

```vue
<template>
  <div id="app">
    <vue-showdown
      markdown="# Hello vue-showdown-highlight"
      flavor="github"
      class="markdown-body"
    ></vue-showdown>
  </div>
</template>
```

> 전역 CSS로 적용되니 다른 CSS와 겹치지 않도록 주의하자.

`github-markdown-css`는 내부 스타일만 선언되어있다. border와 padding 등은 직접 추가하자. 해당 css는 공식 사이트에서 참조하였다(https://www.npmjs.com/package/github-markdown-css)

```vue
<style>
/* src/App.vue */
.markdown-body {
  box-sizing: border-box;
  min-width: 200px;
  max-width: 980px;
  margin: 0 auto;
  padding: 45px;
  border: 1px solid #d1d5da;
  border-radius: 3px;
}

@media (max-width: 767px) {
  .markdown-body {
    padding: 15px;
  }
}
</style>
```

## Highlight code syntax with Highlight.js

여기까지만 해도 훌륭한 문서가 되었다. 그러나 한 가지 코드블록 부분에서 가독성에 중요한 부분인 syntax highlight가 적용되지 않는 것을 볼 수 있다. Highlight.js를 이용해 색깔을 입혀보자. 

`vue-showdown`에서 사용되고 있는 `showdown` 모듈을 가져온 뒤 `showdown-highlight` 확장모듈을 덧붙인다.

```js
// src/plugins/vue-showdown.js
import VueShowdown, 
      { showdown } // get a embeded showdown from vue-showdown
      from 'vue-showdown'
// get a showdown-highlight extension module.
import showdownHighlight from 'showdown-highlight'

// use it!
showdown.extension('showdownHighlight', showdownHighlight)
```

그리고 `vue-showdown` 컴포넌트에서 해당 확장모듈을 사용하도록 지정해주자.

```vue
<template>
  <div id="app">
    <vue-showdown
      :markdown="markdown"
      flavor="github"
      class="markdown-body"
      :extensions="['showdownHighlight']"
    ></vue-showdown>
  </div>
</template>
```

이러면 기존과 달리 code 태그 클래스 값에 `hljs`라는 값이 추가 되는 것을 확인할 수 있다.
```html
<!-- before showdown-highlight extension -->
<pre>
  <code class="js language-js">const HELLO_WORLD = 'Hello World!'
  </code>
</pre>
```

<center>↓</center>

```html
<!-- after showdown-highlight extension -->
<pre>
  <code class="hljs js language-js">
    <span class="hljs-keyword">const</span>
    HELLO_WORLD = <span class="hljs-string">'Hello World!'</span>
  </code>
</pre>
```

마찬가지로 `showdown-highlight` 모듈은 `hljs`라는 값만 추가해줄 뿐 이에 해당하는 css는 직접 추가해야한다 `highlight` 라이브러리에 기본적으로 들어있는 github style의 code css를 가져와 적용하자.

```js
// src/plugins/vue-showdown.js
import 'highlight.js/styles/github.css'
```
> 전역 선언이므로 다른 css와 충돌하지 않도록 주의!

***
드디어 끝났다. 저장 후 새로 고침을 하면, 코드 블록에 색깔이 입혀지는 것을 볼 수 있을 것이다.
<pre>// before
const HELLO_WORLD = 'Hello World'
</pre>

<center>↓</center>

```js
// after
const HELLO_WORLD = 'Hello World'
```

다음 시간에는 직접 사용자 정의 extension을 사용하여, 나만의 스타일을 만들어 보는 것을 해보자.
