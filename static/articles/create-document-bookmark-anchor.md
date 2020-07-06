# Create Document Bookmark Anchor

article 문서를 읽을 때 자동으로 목차를 옆에 띄워주는 북마크를 만들어보자.

## Preview
<iframe src="https://oneyedev.github.io/vue-router-anchor-scroll-sample/index" width="100%" height="400px"></iframe>

## Get Anchors
h1태그를 기준으로 북마크 기능을 담당하는 Component를 작성하자.

<github-vue-code url="https://gist.githubusercontent.com/oneyedev/b87bf439b8f282f81c83fcaf65afb98c/raw/document-bookmark-anchor.vue"></github-vue-code>

> DOM 트리가 생성된 후인 `mounted`훅에서 `anchor`를 가져와야 한다.

> 페이지가 스크롤이 되는 것을 확인하기 위해 의도적으로 font-size를 크게 하였다. 페이지가 스크롤이 될 정도로 커야만 anchor에 따라 움직이는 것을 확인할 수 있다. 

## Move to Anchor
anchor에 따라 스크롤을 움직이게 하는 방법은 크게 세 가지가 있다.

### Update `location.hash`
HTML5에서는 주소창의 hash값을 가지고 기본적으로 Element의 id anchor기능을 제공한다.  

```js
export default {
 methods: {
    goToAnchor(anchor) {
      location.hash = "#" + anchor.id;
    }
  }
}
``` 
이 방법은 vue-router에서 지원하는 고급기능(`offset`과 같은)을 많이 활용하지는 못하나, 가장 간단하고 `vue`종속적이지 않은 방법이다. 

### Using `window.scrollBy` or `window.scrollTo`
만일 HTML5를 지원하지 않는 브라우저라던가, `event block`등의 이유로 위의 방법이 통하지 않는다면 좀더 전통적이고 직접적인 `window`의 `scrollBy` 메소드나 `scrollTo` 를 이용하는 것도 가능하다.

```js
export default {
 methods: {
    goToAnchor(anchor) {
      const top = anchor.getBoundingClientRect().top;
      window.scrollBy(0, top);
    }
  }
}
```

```js
export default {
 methods: {
    goToAnchor(anchor) {
      const top = anchor.offsetTop;
      window.scrollTo(0, top);
    }
  }
}
```
이 방법 또한 `vue` 종속적이지 않다는 장점이 있으나, 스크롤 위치에 대한 정확한 숫자 연산을 직접 해주어야 하는 번거로움이 있다.

### scrollBehavior in `vue-router`
만일 `vue-rotuer`를 사용중이라면 `scrollBehavior`속성을 이용하여 구현할 수도 있다. 위의 방법들은 단일 컴포넌트에만 적용할 경우에는 간단하나, 전역으로 적용하고 싶은 경우, `window`객체에 직접 이벤트를 바인딩하던가 `mixin`을 이용하여 일일히 선언해주어야 하는 번거로움이 있다. 하지만 이 방법을 이용하면, 해당 `router`에서 다루어지는 페이지에 공통적으로 적용됨과 동시에 `router`별로 다른 로직이 적용될 수 있기 때문에, 코드 관리상에서 상당한 이점을 가지고 있다.

```js
export default {
 methods: {
    goToAnchor(anchor) {
      const name = this.$route.name;
      const hash = "#" + anchor.id;
      this.$router.push({ name, hash });
    }
  }
}
```
`goToAnchor`메소드 호출 시 주소창의 hash값이 업데이트 되며 `vue-router`내의 `scrollBehavior` 메소드가 호출된다.

```js
new Router({
  mode: "history",
  scrollBehavior(to, from, savedPosition) {
    if (to.hash) {
        return {
          selector: to.hash
        };
    }
  }
})
```
> `scrollBehavior`메소드는 `history`모드에서만 동작한다. 자세한 사항은 [공식 문서](https://router.vuejs.org/guide/advanced/scroll-behavior.html) 참조

