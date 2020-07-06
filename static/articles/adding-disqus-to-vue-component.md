# Adding Disqus to vue component
블로그 댓글 기능을 해줄 수 있는 [Disqus](https://disqus.com/)를 붙여보았다.

1. [universal-embed-code](https://help.disqus.com/installation/universal-embed-code): `Disqus`측에서 제공해주는 universal embed code를 이용하는 방법이다. 순수 HTML로 이루어진 홈페이지에서도 적용할 수 있는 장점이 있지만 `window`전역 객체 접근이 있기 때문에 SPA 사이트에는 잘 맞지 않는다.
1. [Using Disqus on AJAX sites](https://help.disqus.com/developer/using-disqus-on-ajax-sites): `Disqus` 스크립트를 적용한 후, 자체적으로 제공하는 AJAX형태의 reset메소드를 이용하는 방법이다. SPA 사이트와 궁합이 좋다.
1. [vue-disqus](https://github.com/ktquez/vue-disqus): Vue Framework를 사용한다면, 이미 나와 같은 삽질을 한 개발자가 만들어 놓은 `vue-disqus`를 사용하면 편리하다.

## Disqus Config
위 세방법 모두 disqus측에 건네 줄 설정 정보는 아래와 같다.
- shortname: Disqus 포럼의 이름이다. 포럼은 Disqus thread의 모음이다. Disqus에 등록한 웹사이트 ID를 입력하면 된다.
- this.page.identifier: Disqus thread의 ID, 댓글이 달릴 현재 페이지 ID를 지정하면 된다.
- this.page.title: Disqus thread의 이름, Disqus의 서비스에서 사용될 때 노출되는 명칭이므로, 쉽게 이해할 수 있는 명칭을 지정하는 것이 좋다.
- this.page.url: Disqus thread가 embed되는 페이지의 url, Disqus 서비스에서 사용되는 링크를 지정할 수 있다.


## Universal Embed Code
<github-vue-code url="https://gist.githubusercontent.com/oneyedev/362e1b1e1be15428f6ae99389067cb42/raw/vue-disqus-embed.vue"></github-vue-code>

`disqus_thread`는 `embed` script에 의해 치환될 영역으로 바꾸지 말아야 한다.

## Using Disqus on AJAX sites
<github-vue-code url="https://gist.githubusercontent.com/oneyedev/04fa9edec692783d583bd67966df39f6/raw/vue-disqus-ajax.vue"></github-vue-code>

최초 로드 후, `reset`메소드로 ajax호출할 수 있다.

## Using vue-disqus

### installation
```sh
npm install --save vue-disqus
```

```js
import Vue from 'vue'
import VueDisqus from 'vue-disqus'

Vue.use(VueDisqus)
```
모듈 설치 후 컴포넌트를 전역등록 하면 어느 곳에서나 사용 가능하다.

### Usage in component
<github-vue-code url="https://gist.githubusercontent.com/oneyedev/7b5eb7a07bc0d01a1bdfad911e31dd3d/raw/vue-disqus-sample.vue"></github-vue-code>

