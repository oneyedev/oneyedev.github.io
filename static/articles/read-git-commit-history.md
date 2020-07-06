# Read git commit history

좋은 블로그라면 당연히 문서의 작성날짜와 마지막 수정날짜가 있어야지...라며 시작하였다.
No DataBase를 지향하다보니 프로젝트의 git commit history를 읽어야 했고, 해당 commit 시간을 읽어 기본 asset에 업데이트 하도록 하였다. 아래는 사용한 기술 중 일부이다.

## Installation
```sh
npm install nodegit
```
자세한 정보는 [nodegit](https://www.nodegit.org/) 참조

## Write a Test
프로젝트 루트에 `.git`폴더가 있고 `README.md` git 히스토리가 있다는 가정하에 테스트 코드를 짰다. 엄밀한 의미에서 단위 테스트라고 보긴 힘들다.
```js
// tests/unit/git-controller.spec.js
import gitController from '@/module/git-controller'

// This test assume that project root already .git folder and has README.md git file history
test('README.md recent commit test', async () => {
  // given
  const file = 'README.md'

  // when
  const commit = await gitController.getRecentFileCommit(file)

  // then
  expect(commit.timeMs()).toBe(1553579967000)
})

```


## Write a Production
```js
// '@/module/git-controller'
const getRevWalker = async function(repo) {
  const walker = await repo.createRevWalk()
  walker.pushHead()
  walker.sorting(Git.Revwalk.SORT.TIME)
  return walker
}
module.exports.getRecentFileCommit = async function(filePath) {
  return Git.Repository.open('.')
    .then(getRevWalker)
    .then(walker => walker.fileHistoryWalk(filePath, 10000))
    .then(result => {
      const data = result.length > 0 ? result[0].commit : undefined
      return data
    })
}
```

## Run a Test
```sh
npm run test:unit
```
> 자세한 사항은 [@vue/cli-plugin-unit-jest](https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-unit-jest) 참조
