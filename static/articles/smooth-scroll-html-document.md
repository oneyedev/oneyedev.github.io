# Smooth scroll HTML document

HTML document 내에서 anchor 이동시 스크롤이 부드럽게 나타나도록 구현해보자.

## Preview

https://oneyedev.github.io/vue-router-anchor-scroll-sample/smooth

## Move by constant speed

`window`객체의 `requestAnimationFrame`메소드는 다음 애니메이션 프레임 때 `윈도우 객체가 생성된 후의 시간`을 매개변수로 콜백호출된다. `setInterval`메소드보다 부드러운 애니메이션 및 최적화를 위해 사용되곤한다. 자세한 사항은 [여기서](https://developer.mozilla.org/ko/docs/Web/API/Window/requestAnimationFrame) 확인. 만일 애니메이션 프레임 마다 호출되기 원하면, 재귀함수처럼 콜백함수내에서 `requestAnimationFrame`메소드를 재호출해야한다.
> 비동기 메소드로 호출되기 때문에 무한루프에 빠지지는 않으나, 해당 콜백이 매프레임 호출되는 것을 막기 위해서는 종료조건을 잘 작성해야한다.

```js
function moveByConstantSpeed(target, speed) {
    // callback method which is called each frame.
    const step = (currentTime, prevTime) => {
        // calculate exact delta time with previous frame.
        const deltaTime = currentTime - prevTime;
        const top = target.getBoundingClientRect().top;
        // end callback when scroll is close to target. 
        // scroll value is updated by integer unit.
        if (Math.abs(top) <= 1) return;
        if (top > 0) {
            // target is below
            const sc = document.scrollingElement;
            if (sc.scrollTop + sc.clientHeight === sc.scrollHeight){
                // end callback when scroll is ended
                return;
            } 
            // calculate pixel vector to move
            const deltaV = Math.min(deltaTime * speed, top);
            window.scrollBy(0, deltaV);
        } else {
            // target is upper
            if (document.scrollingElement.scrollTop === 0) {
                // end cacllback when scroll is top
                return;
            }
            // calculate pixel vector to move
            const deltaV = Math.max(-deltaTime * speed, top);
            window.scrollBy(0, deltaV);
        }
        // re-register next step method
        window.requestAnimationFrame(nextTime => step(nextTime, currentTime));
    };
    // register first step method
    window.requestAnimationFrame(currentTime => step(currentTime, window.performance.now()));
}
```
> 위 메소드는  `target` HTML element가 항상 존재한다는 가정하에 작성되는 등 동적인 상황에 대한 고려가 되어있지 않으므로 실제 적용하는데에는 주의가 필요.

## Move by constant frames
`속력 = 거리 / 시간(프레임)`이므로 간단한 계산으로 구현.
```js
function moveByConstantFrame(target, frame) {
    const distance = Math.abs(target.getBoundingClientRect().top);
    const speed = distance / frame;
    moveByConstantSpeed(target, speed);
}
```

## `scrollBehavior` in `vue-router`

`vue-router`의 `scrollBehavior`에 작성시 동기로 호출해도 되지만, `Promise`형태로 작성하면 에러 발생시 다양한 대처를 할 수 있다.
```js
  scrollBehavior(to, from, savedPosition) {
    if (to.hash) {
      return new Promise((resolve, reject) => {
        const target = document.querySelector(to.hash);
        try {
          if (target) {
            moveByConstantSpeed(target, 10);
          } else {
            reject(to.hash + "not exist");
          }
        } catch (error) {
          reject(error);
        }
      });
    }
  }
```

## futhermore...
이번 튜토리얼은 일종의 `linear` 스크롤을 구현한 것이라고 볼 수 있다. 다음은 `easing-function`을 이용하여 더욱 자연스러운 스크롤을 구현해보자.


