# Runtime compile vue component

Vue component를 runtime시-_client browser 상에서_- compile하는 방법을 `vue-component-playground`를 만들어보면서 알아보자.

## Preview
<iframe src="https://oneyedev.github.io/vue-runtime-compile-component/" width="100%" height="400px"> </iframe>
링크 : https://oneyedev.github.io/vue-runtime-compile-component/

## Add `runtimeCompiler` in `vue.config.js`
vue 설정파일에 아래 옵션만 추가하면 바로 사용가능하다.
```js
module.exports = {
    runtimeCompiler: true
}
```

## Add dependencies for playground(for tutorial)
아래 두 모듈은 `playground` 컴포넌트를 만들기 위해서 추가하는 모듈이다. 실제 사용시에는 하지 않아도 무방하다. `vue-nav-tabs`는 tab component 라이브러리, `vue-prism-editor` code syntax highlight editor이다.

### vue-nav-tabs
```sh
npm install vue-nav-tabs
```

```js
// main.js
import VueTabs from 'vue-nav-tabs/dist/vue-tabs.js'
import 'vue-nav-tabs/themes/vue-tabs.css'
Vue.use(VueTabs)
```
> `0.5.7` 버전에서 `import VueTabs from 'vue-nav-tabs`를 하면 dependency error가 발생한다. 자세한 사항은 [이슈](https://github.com/cristijora/vue-tabs/issues/44) 확인

### vue-prism-editor
```sh
npm install vue-prism-editor
npm install prismjs
```
> `prism.js`는 수동으로 추가 해주어야한다.
```js
// main.js
import "prismjs";
import "prismjs/themes/prism.css";
import 'vue-prism-editor/dist/VuePrismEditor.css'
```

## Default vue component
아래와 같은 `template`, `script`, `css`값이 런타임시 주어진다고 하자. 물론 사용자입력에 따라 해당 값은 변할 수도 있다. 
```js
// assets/alert-button.js
export default {
    template: `<div id="playground-result">
  <button id="hello" @click="sayHelloWorld">Click Me</button>
</div>`,
    script: `return {
  methods: {
    sayHelloWorld(){
      window.alert('Hello World!')
    }
  }
}`,
    css: `#hello {
  background-color: #2196f3;
  color: white;
  width: 200px;
  height: 40px;
}`
}
``` 

## Create a playground component

### App.vue
<github-vue-code url="https://raw.githubusercontent.com/oneyedev/vue-runtime-compile-component/master/src/App.vue"></github-vue-code>

script내의 `compile` 메소드에서 사용법을 확인할 수 있다.
```js
compile() {
    // use a template
    let option = { template: this.template }

    // use a script
    option = Object.assign(option, new Function(this.script)())

    // use a css
    this.style.textContent = this.css

    // comile and mount at playground-result
    new Vue(option).$mount('#playground-result')
}
```