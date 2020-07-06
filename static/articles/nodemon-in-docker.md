# Nodemon in Docker

DevOps관점에서 Docker는 독점 수준으로 컨테이너 생태계를 장악하고 있는 듯 하다._머지않아 개발자의 컴퓨터엔 git과 docker만 살아남지 않을까_
Node 개발환경 구축시 자주 사용되는 nodemon을 Docker 컨테이너 위에서 동작하도록 구축해보자.

## Requirements
`docker` : 끝. 참 편한 세상이다_(도커 만세!)_ 모든 디펜던시는 도커 컨테이너 안에서 설치-구축-서비스 된다.
`node` : (옵션) 호스트에서 실행해보고 싶은 경우만 설치.

## package.json
먼저 로컬에서 Node를 개발하는 것 __처럼__ 프로젝트 루트에 package.json, main.js, src폴더를 작성한다.
예를 들기 위해, 단순히 src에 있는 json파일을 불러와 1초마다 한번씩 출력해주는 단순한 프로그램을 작성하자.

```js
// package.json
"scripts": {
    "dev": "node main"
}
```

```js
// main.js
setInterval(function () {
    const something = require('./src/something.json')
    console.log(something)
}, 1000)
```

```js
// src/something.json
{
    "foo": "bar"
}
```

`npm run dev`로 호스트(로컬)에서 실행해 볼 수 있다.

## nodemon
src폴더내에 무언가를 변경할 때마다 `npm run dev`을 터미널에서 실행하는 건, 여간 귀찮은 일이 아니므로 `nodemon`을 설치해보자.

```bash
npm install --save-dev nodemon
```

```js
// package.json
"devDependencies": {
    "nodemon": "^2.0.2" 
},
```
> Node를 호스트에 설치하지 않은 경우 수동으로 package.json에만 추가해 놓자. `npm`으로 설치한 경우 자동으로 추가 된다.

그리고 마지막으로 nodemon config를 추가하자. nodemon이 자동으로 src폴더 내의 모든 파일 변경 사항을 감지해 `npm run dev`를 재시작 해주도록 하는 설정이다.

```js
// package.json
"scripts": {
    "dev": "node main",
    "dev:watch": "nodemon"
},
"nodemonConfig": {
    "exec": "npm run dev",
    "watch": [
        "src/*"
    ]
}
```
> 프로젝트 루트에 `nodmon.json`를 추가해서 cli옵션에 file을 지정해줘도 된다.

로컬(호스트)에서 `npm run dev:watch`를 실행 후, src폴더내의 something.json의 내용을 변경 및 저장하면,
바뀐 내용이 출력되는 것으 볼 수 있다.

## Dockerfile
이제 이 모든 로컬(호스트) 개발환경을 Docker 컨테이너로 옮겨보자. 프로젝트 루트에 아래와 같이 `Dockerfile` 파일을 작성한다.

```docker
# Dockerfile
# Docker 허브에서 node 공식 linux alpine 이미지를 가져온다
FROM node:10.15-alpine

# working directory 지정
WORKDIR /usr/src/app

# COPY <host> <docker-image>
# 호스트 프로젝트 루트의 있는 모든 파일을 도커이미지로 복사한다.
# 위에 WORKDIR을 지정했으므로 /usr/src/app폴더에 복사된다.
COPY . .

# 도커이미지에 포함할 라이브러리 설치 명령
RUN npm install

# 컨테이너 시작시 자동으로 실행할 명령
CMD [ "npm", "run", "dev:watch" ]
```

이제 아래 명령으로 도커이미지를 빌드하자
```sh
docker build . -t nodemon-in-docker
```
현재 프로젝트 루트에서 자동으로 `Dockerfile`을 찾아 설정대로 이미지를 만들어 줄 것이다.

> `-t` 명령은 이미지에 태그를 붙여서 추후 쉽게 이미지를 찾기 위한 용도이다. 깜박했다면 `docker images`명령으로 id를 찾아 실행해야 한다.

## Run docker container
이제 만든 도커이미지를 컨테이너로 실행해보자

```sh
docker run --rm -v $(pwd)/src:/usr/src/app/src nodemon-in-docker
```
`--rm` : 도커 컨테이너가 종료됐을시 자동으로 컨테이너를 삭제하는 옵션
`-v` : 볼륨옵션, 호스트의 폴더와 컨테이너 내의 폴더를 마운트 해준다. `<host폴더>:<container폴더>` 형태. `$(pwd)`는 호스트의 컨텍스트 루트를 나타내는 예약어이다. 자세한 것은 [공식 설명](https://docs.docker.com/storage/volumes/) 참조

위 명령을 요약하자면, nodemon-in-docker라는 태그의 이미지를, 호스트의 `/src`폴더와 컨테이너의 `/usr/src/app/src`폴더를 마운트하여 실행해달라는 뜻이다.

이제 로컬(호스트) `src/something.json`을 수정 후 저장해보자. 
그러면 놀랍게도, 컨테이너에서 돌고 있던 `nodemon`이 변경사항을 감지해서 변경된 값을 출력해 줄 것이다. 

로컬(호스트)에 Node 관련 라이브러리를 전혀 설치 않았음에도 불구하고, 우리는 Node로 어떤 개발이든 할 수 있게 되었다. 개발환경 구축하는데 1주 쯤은 가볍게 잡아 먹었던 걸 생각하면... 다시 한번 _도커만세!_
