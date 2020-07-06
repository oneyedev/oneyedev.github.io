# Build Github page using Vue Cli 3

Vue Cli가 2.x버전에서 3.x 버전으로 업데이트 되면서 매우 편리해졌다. Vue Cli를 이용해 Vue 프로젝트를 구축하고 Github page로 웹 페이지를 간단하게 배포해보자.

## Preview
https://oneyedev.github.io/hello-vue-github-page/

## Installation
Vue Cli가 이미 설치 되어있는 경우 이 스탭은 패스해도 된다

```sh
npm install -g @vue/cli
```

## Creating a Project

프로젝트를 만들기 위해 아래 명령어를 입력한다. project-name을 생략한 경우, 현재 폴더에서 프로젝트가 만들어 진다.

```sh
# vue create <project-name>
vue create hello-vue-github-page
```

feature preset을 선택하지 않은 경우, 아래와 같이 프로젝트에 추가하고 싶은 feature을 선택할 수 있다. 이전 버전에 비해 획기적으로 향상된 부분이다.

![vue-cli-select-features](https://cli.vuejs.org/cli-select-features.png)

각 feature에 대해 간략한 설명을 더하면
- Babel : ECMAScript 2015+ 문법을 사용한 코드가 옛 브라우저와 같은 환경에서도 동작하도록 바꾸어주는 Javascript 컴파일러. 최근 웹개발환경에서는 거의 필수로 사용되고 있다(https://babeljs.io).

- TypeScript : Javascript의 느슨한 타입과 같은 한계점을 극복하기 위해 나온 Javascript의 superset 언어. 개인적으로는 선호하지 않지만 Java나 C++같이 OOP에 익숙한 개발자들에게는 좋은 대안이 될 수 있다(https://www.typescriptlang.org).

- PWA Support : Web과 App의 장점을 살린 Progressive Web App을 지원하도록 프로젝트 환경을 구성해준다. 현대 웹과 앱은 통일된 UX를 제공하도록 기술발전이 진행중이다. 제공하고자하는 서비스가 웹브라우저 상에서 앱처럼 동작 해야 하는 앱(ex. 구글 확장프로그램)이라면 선택하자 
_*필자도 아직 잘 알지 모르는 분야라 추후 공부해보고자 한다*

- Router : URL이 바뀔시 전체 문서를 다시 불러오지 않고 변경부분만 업데이트하는 SPA(Single Page Application) 환경 내에서 URL과 Vue App의 상호작용을 지원하는 공식 Library. SPA 를 만들고자 한다면 사실상 필수이다(https://router.vuejs.org)

- Vuex : Vue App에 대한 전역 상태 정보 Libray. 기본적으로 Vue Component간의 데이터 교환은 props와 event를 통해 부모자식관계(*OOP의 상속관계가 아니다*)에서 이루어진다. 만일 프로젝트가 커져 Component트리가 복잡해 진다면, 다수의 Component가 공유 해야 하는 정보를 다루기가 여간 까다롭지않다. 만들고자하는 프로젝트 규모와 특성에 맞춰서 선택하자(https://vuex.vuejs.org)

- CSS pre-processors : 순수 CSS의 문법적 한계를 보완하기 위한 전처리기를 포함할지 여부. SASS/SCSS, LESS, Stylus 중 선택할 수 있다. CSS계의 Babel역할을 담당하는 PostCSS는 기본적으로 포함 되어 있다.

- Linter / Formatter : 코드의 스타일 -*문자 간격, 변수 명, 탭 크기, 괄호 위치와 같이 컴파일 오류는 아니지만 코드를 이해하고 작성하는 데 영향을 주는 요소*- 을 일정하게 유지해주는 Library. 특히 웹과 같이 다양한 언어 혹은 문법을 다루는 프로젝트에서 Code Formatter 의 역할을 나날이 중요해져가고 있다(https://eslint.org/).

- Unit Testing - Vue Component가 정상적으로 동작하는 지 확인해볼 수 있는 Unit Test Framework를 포함할지 여부, Mocha + Chai 혹은 Jest 중에서 선택 할 수 있다. TDD에 습관을 들이고 싶은 개발자라면 필수.

- E2E Testing - End To End Testing의 약자. Component 수준이 아닌, 최종적으로 보여지는 브라우저 화면을 테스트하고 싶을 경우 선택하자. Cypress와 Nightwatch 중에서 선택할 수 있다.

간단한 예제로 진행할 만큼, 기본 선택되어있는 babel과 eslint만으로 프로젝트를 생성한다.

프로젝트가 생성되었으면, 아래 명령어로 dev server를 띄워 페이지가 잘 뜨는지 브라우저로 확인해보자
```sh
# cd <project-name>
cd hello-vue-github-page
npm run serve
```

```sh
  App running at:
  - Local:   http://localhost:8080/
  - Network: unavailable
```


## Building a Project

package.json을 보면 아래와 같이 3가지 스크립트가 추가 되어있다.

```json
"scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"    
}
```

- serve - 개발모드(default)로 Vue App을 실행
- build - 배포를 위한 Build 명령. Webpack 번들링으로 dist폴더에 배포할 정적 리소스(HTML, JS, CSS 등)가 생성된다.
- lint - src폴더내의 모든 코드를 자동으로 포맷팅

실제로 빌드해보기에 앞서 번들링 시에 사용될 Webpack의 Config를 확인해보자 
위의 스크립트에 아래 명령을 추가 후 실행해보자.
```json
"scripts": {    
    "inspect": "vue-cli-service inspect"    
}
```
```sh
npm run inspect
```

혹은 터미널에서 직접 실행해도 된다.
```sh
vue inspect
```

실행하면 터미널에서 많은 Webpack의 설정정보가 출력되는 것을 볼 수 있다. 자세히 살펴보고 싶은 경우 Vue UI를 이용하는 것을 추천한다. _*빈 폴더에 npm init부터 시작하여 온갖 삽질을 해봤던 필자에게는 너무나 행복한 경험이었다. 심지어 .gitignore까지!*
```js
// ... many webpack config
  entry: {
    app: [
      './src/main.js'
    ]
  }
}
```

그럼, 실제로 아래 명령으로 빌드를 해보자
```sh
npm run build
```
dist폴더에 아래와 같은 파일들이 생성된 것을 볼 수 있다.
```folder
- dist
 - css
 - img
 - js
 favicon.ico
 index.html
```
이 dist 폴더가 실제로 우리가 웹으로 배포할 정적 리소스들이다. 간단하게 Local환경에서 웹배포를 테스트 해볼 수 도 있다.

```sh
npm install -g serve
```

```sh
# serve <folder-name>
serve dist
```

```
┌────────────────────────────────────────────────────┐
│                                                    │
│   Serving!                                         │
│                                                    │
│   - Local:            http://localhost:5000        │
│   - On Your Network:                               │
│                                                    │
│   Copied local address to clipboard!               │
│                                                    │
└────────────────────────────────────────────────────┘
```

### outputDir

그런데 안타깝게도 github page는 프로젝트 root 폴더 혹은 root/docs 폴더로만 웹 정적리소스를 배포한다. 따라서 우리는 크게 3가지 방법을 떠올릴 수 있다.

1. 배포할 정적리소스만 관리될 Github Repository를 따로 만든다.
1. dist폴더를 docs 폴더로 rename 혹은 copy 후 push 한다.
1. Vue Cli의 Webpack config를 수정하여 번들 결과물 폴더이름을 docs로 변경한다


1번의 방법은 repository에 정적리소스만 존재하므라 크기를 줄일 수 있고, 타환경에서 clone 할 일이 있을 경우-*클라우드를 통한 배포 등*- 장점이 될 수 있다. 단, 관리할 repo가 늘어난다는 것은 부담이다. 이 방법을 선택할 경우 git의 submodule을 사용하는 것을 고려해보라.

2번의 방법은 Jenkins와 같은 CI/CD 환경이 미리 구축되어 있다면, 빠르게 적용할 수 있다는게 장점이다. -*사실 간단한 쉘 명령으로도 가능하다*- 다만, 배포까지 한 스탭이 추가되어 약간의 시간 손해는 감수해야 하며, 개발자의 본성상 "굳이?"라는 의문이 들게 한다.

3번의 방법은 2번의 장점을 그대로 가져가면서, 시간 손해라는 단점을 상쇄시킬 수 있다. 그러므로 우리는 방법3으로 진행해보자. 

이전버전과 달리 vue cli3를 이용해 만든 프로젝트에는 webpack.config.js 파일이 따로 존재하지 않는다. 필요한 경우 vue.config.js파일을 만들어 webpack설정을 수정할 수 있다. 자세한 것은 아래 두 페이지를 참조 바란다.

1. [Working with Webpack](https://cli.vuejs.org/guide/webpack.html#simple-configuration) 
1. [vue.config.js option list](https://cli.vuejs.org/config/#vue-config-js)

프로젝트 root 폴더에 vue.config.js 파일을 만들고 아래 내용을 추가한다.

```js
// vue.config.js
module.exports = {
  outputDir: 'docs'  
}
```

### publicPath

또 한 가지 고려사항이 있다. github page는 end-point url 스키마가 user(혹은 organizaiton)인 경우와 project인 경우가 다르다.

```sh
http(s)://<user-name>.github.io # for user/org repository
http(s)://<user-name>.github.io/<project-name> # for project repository
```

두 경우 모두 baseUrl은 `<username>.github.io`이지만 진입점이 다르기 때문에, project repository의 `index.html`에서 참조하는 js, css 파일을 제대로 불러오지 못하게 된다.

```sh
# valid url
http(s)://<user-name>.github.io/<project-name>/js/app.<hash>.js
# request url in index.html, so 404 not found will fallback
http(s)://<user-name>.github.io/js/app.<hash>.js
```

따라서 vue.config.js에 추가 옵션이 필요하다. publicPath는 브라우저 리소스가 상대경로를 참조할 때 base로 사용되는 값이다. 기본값은 '/' 이다. 따라서 user/organization repository를 사용한다면 publicPath옵션은 생략 가능하다. 

```js
// vue.config.js
module.exports = {
  // publicPath: '/<project-name>' required only project repository
  publicPath: '/hello-vue-github-page'
}
```

### Run final build
최종적으로 vue.config.js는 다음과 같다.
```js
// vue.config.js
module.exports = {
  outputDir: 'docs', 
  // publicPath: '/<project-name>' required only project repository
  publicPath: '/hello-vue-github-page'
}
```
저장 후 build를 수행한다
```sh
npm run build
```
```sh
  File                                 Size               Gzipped

  docs\js\chunk-vendors.ecd76ec1.js    82.81 KiB          29.94 KiB
  docs\js\app.faa07fd5.js              4.60 KiB           1.65 KiB
  docs\css\app.e2713bb0.css            0.33 KiB           0.23 KiB
 
 DONE  Build complete. The docs directory is ready to be deployed.
```

docs 폴더로 결과물이 생성되는 것을 볼 수 있다.

## Deploying to Github Page

위에서 빌드한 docs폴더를 github page를 통해 배포해보자. Github Repository를 생성 후 프로젝트 root에서 다음을 실행하자.

※ 생성시 Repositroy Name을 `<user-name>.github.io`으로 한다면 user repository가 된다.

```sh
git init # initialize local git repository
git add . # add every files to index
git commit -m "Deploy" # commit changes
git push -u <Github repository URL> master # push to remote github repository. Add -f option when pushing to existing repository
```

Settings - GitHub Pages - Source - master branch /docs folder를 선택한다. docs 폴더가 존재하지 않으면 해당 선택지가 활성화되지 않으므로 주의.

![github-pages](https://help.github.com/assets/images/help/pages/select-master-branch-docs-folder-as-source.png)

잠시 뒤  아래 링크에서 배포된 페이지를 확인할 수 있다.
```sh
http(s)://<user-name>.github.io # for user/org repository
http(s)://<user-name>.github.io/<project-name> # for project repository
```

