# express-mongo-in-docker-compose
Node 웹 개발환경에서 자주 사용되는 Express와 MongoDB를 도커 환경내에서 구축해보고, Docker-compose를 이용해 한 파일에서 관리해보자.

## Requirements

[docker](https://www.docker.com/)
[docker-compose](https://docs.docker.com/compose/install/) : Docker for Mac(or window)를 설치했다면 기본 포함되어 있다.

## Run a Express app in Node Container
먼저 기본적인 Node container를 띄우자. __ Dockerfile부터 보고싶으신 분은 [Dockerlize](#dockerlize)로 이동__

```sh
docker pull node:alpine
docker run --rm -it \
    --name api-server \
    -v $(pwd):/usr/src/app \
    -v /usr/src/app/node_modules \
    node:alpine sh
```

`--rm` : 컨테이너 종료 시 자동으로 삭제되도록 하는 옵션
`-it` : interactive(`i`) 와 tty(`t`)를 동시에 쓰는 옵션, 사용자 입출력을 보기 위한 용도.
`--name` : 컨테이너의 이름 지정, 추후 컨테이너를 검색 혹은 지정할 때 자주 사용된다. 생략시 랜덤한 이름이 배정된다.
`-v` : 호스트와 컨테이너의 볼륨(폴더) 바운드, `<host>:<container>`문법이며 `$(pwd)`는 호스트의 현재 워킹디렉토리를 뜻하는 예약어이다

> `-v usr/src/app/node_modules` 해당 옵션이 실행될 경우 컨테이너에 node_modules 빈 폴더가 생성된다. 이 옵션은 호스트와 컨테이너의 node_modules가 공유되지 않기 위해서 추가하였다. 

위의 명령을 실행했다면 마치 원격접속이라도 한 듯, 컨테이너의 쉘이 실행 될것이다 __도커 만세!__

필요한 노드 모듈을 설치해준다.

```sh
cd usr/src/app
npm init -y # Every configurations is 'yes'
npm install --save express mongodb
```

volume mount로 호스트 폴더에 package.json이 생성된다. 이제 프로젝트 root에 [Express Hello World](https://expressjs.com/en/starter/hello-world.html)를 작성해준다.

```js
// server.js
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => {
    res.send('Hello World!')
})

app.listen(port, () => console.log(`Example app listening on port ${port}!`))
```

```js
// package.json
"scripts": {
    "start": "node server.js"
}
```

호스트에서 파일을 생성하면 컨테이너에 자동 생성 될 것이다. (다시 한번 __도커만세!__) 컨테이너에서 `npm start`로 정상적으로 Express앱이 띄워본다.

```sh
npm start
# Example app listening on port 3000!
```

### 보고도 못 믿겠어(옵션)
정말 제대로 띄워진건지 궁금해서 호스트 웹 브라우저에 http://localhost:3000 로 접속해도 아무것도 뜨지 않는다.
컨테이너의 포트가 외부에 노출되어 있지 않기 때문이다. 컨테이너 내에서 확인하기 위해 새로운 쉘을 띄워 `curl` 명령을 날려보자.

```sh
docker exec -it api-server sh
apk add curl # 이미 설치된 경우 생략가능
curl http://localhost:3000 # Hello World!
```
 > `run`과 `exec`의 가장 큰 차이중 하나는 명령어의 대상이 `run`은 도커이미지 `exec`는 컨테이너라는 것이다. 
 > 이미 띄워진 컨테이너에 새로운 명령을 여는 것이므로 `exec`를 사용하고 지정자도 컨테이너 이름(예시에서는 `api-server`)으로 하였다.
 > 마찬가지 이유로 exec 명령어에는 `--rm` 옵션이 존재하지 않는다.

## Dockerlize
지금까지의 과정을 매번 한다는건 여간 귀찮은 일이 아니므로, Dockerfile에 위 스탭을 최대한 적용하여 도커 이미지로 만들어보자

```Dockerfile
# Dockerfile
# Specify base image
FROM node:alpine

# Specify working directory
WORKDIR /usr/src/app

# Copy current host files to workdir in container 
COPY . .

# Install dependencies
RUN npm install

# Specify initial command
CMD ["npm", "start"]
```

> `server.js`와 `package.json`은 위 스탭 참조

```Dockerfile
# .dockerignore
node_modules/
```
> `.dockerignore`를 지정하면 Copy시 해당 폴더가 무시 된다.

이제 이미지 빌드 후 컨테이너를 실행해보자
```sh
docker build . -t api-server
docker run --rm -p 3000:3000 api-server
```

`-t` : 이미지 빌드 시 태그를 붙임으로 추후 찾기 쉽게하기 위한 용도(없으면 컨테이너 실행 시 Image ID를 사용해야한다)
`-p` : `<host-port>:<container-port>` 호스트와 컨테이너의 포트를 바인딩하기 위한 옵션

이제 호스트의 http://localhost:3000으로 접속하면 Hello World!가 출력된다.

## Create a Network Bridge

각 컨테이너는 격리된 가상 공간에서 실행되기 때문에 Express에서 `DB_URL`을 `localhost`로 지정해주어봐야 제대로 인식되지 않는다.
따라서, 컨테이너간의 연결을 담당해줄 네트워크를 만들어야 한다. 

```sh
docker network create express-mongo
```

`express-mongo`라는 이름의 네트워크를 만들었다. 특별한 옵션이 없는 경우 `bridge`모드로 생성된다. 컨테이너가 같은 호스트내에 존재할 때 사용되는 네트워크 모드이다. 자세한 내용은 [공식문서](https://docs.docker.com/network/) 참조.

## Run a Mongo Container

이제 Mongo 이미지를 가져와 띄워보자. mongodb는 기본적으로 27017포트에 바인딩된다.

```sh
docker pull mongo
docker run -d --rm --name mongo-container -p 27017:27017 --network express-mongo mongo
```
`-d` : daemon 모드로 실행
`--network` : 컨테이너에서 사용할 네트워크 지정

`--name` 옵션의 값(여기선 `mongo-container`) 추후 Express app의 DB_URL 지정에서 사용된다.
`curl` 혹은 웹브라우저를 통해 제대로 동작하는지 확인해보자. 
> `-p` 옵션은 host에서 확인하기 위한 용도일 뿐 생략가능하다.

```sh
curl http://localhost:27017
# It looks like you are trying to access MongoDB over HTTP on the native driver port.
```


## Link a Express app and a Bridge network

이제 Mongo의 DB_URL을 지정하고 네트워크를 Express App과 연결하자. DB_URL의 호스트 이름에 위에서 설정한 컨테이너 이름을 적는다.

```js
// mongo.js
const MongoClient = require('mongodb').MongoClient;
const DB_URL = 'mongodb://mongo-container'; // localhost(혹은 ip) 대신 컨테이너 이름을 적는다
const option = { useUnifiedTopology: true }

module.exports = {
    connect(callback){
        MongoClient.connect(DB_URL, option, function(err, client){
            if(err) {
                console.error('Failed connecting to server')
            } else {
                callback(client.db('example'))
            }
        });
    }
}
```
```js
// server.js
const mongo = require('./mongo')
app.get('/db', (req, res) => {
    mongo.connect(db => {
        const collection = db.collection('documents');
        collection.find({}).toArray(function(err, docs){
            res.send(docs)
        })
    })
})
app.get('/db/insert', (req, res) => {
    mongo.connect(db => {
        const collection = db.collection('documents');
        collection.insertMany([{"key":"value"}], function(err, result) {
            res.send(result)
        });
    })
})
```
> 위 소스코드는 테스트만을 위한 용도 일뿐, DB 커넥션의 해제나 에러처리 등은 전혀 고려하지 않은 코드이다.
> 실제 제품코드에서는 `mongodb` native driver보다는 [mongoose](https://mongoosejs.com/)같은 라이브러리를 쓰는 것을 추천한다.

이제 해당 소스코드를 이미지로 빌드 후 실행해보자.
```sh
docker build . -t api-server
docker run --rm -d --network express-mongo -p 3000:3000 api-server
curl http://localhost:3000/db/insert
# {"result":{"ok":1,"n":1},"ops":[{"key":"value","_id":"5e637ced1eb418001246557f"}],"insertedCount":1,"insertedIds":{"0":"5e637ced1eb418001246557f"}}
curl http://localhost:3000/db
# [{"_id":"5e637ced1eb418001246557f","key":"value"}]
```

## Final: Express-Mongo in Docker-compose

Dockerfile이 standalone한 단일 도커 이미지를 만드는 설계도라면, 
Docker-compose는 복수의 도커 컨테이너 간의 동작과 설정을 정의하는 도커 스택 정의서라고 볼 수 있다.

지금까지 위에서 설정한 옵션들을 docker-compose.yml 안에 녹여보자.
```yml
version: "3.7"
services:
    api-server: # Express service 이름, container_name 옵션 생략시 container 이름이 된다
      build: . # 프로젝트 폴더에서 Dockerfile을 자동으로 찾아 이미지를 빌드한다
      ports: # 외부에 노출할 포트
        - "3000:3000" # host:container
      links: # 브릿지 네트워크로 연결할 컨테이너
        - mongo-container # Mongo container 이름과 같아야한다.
    mongo-container: # Mongo service 이름, container_name 옵션 생략시 container 이름이 된다
        image: mongo # 직접 빌드하는 것이 아니라 Docker hub에서 공식 이미지를 가져와 사용한다
        restart: always
```
굉장히 심플해졌다! docker 실행 시 설정 해줘야하는 많은 옵션들이 docker-compose안에서 설정 가능하다

```sh
docker-compose up -d
```

`up`: 이미지가 없을 시 빌드해주고 컨테이너 실행까지 한번에 해주는 명령, 워킹 디렉토리에서 `docker-compose.yml`을 자동으로 찾는다.
`-d`: daemon모드로 실행

간단한 명령 한번으로 도커이미지를 빌드하고 네트워크를 만들고 연결시키는 작업을 다 해준다. 또 한번 __도커 만세!__

## 마치며
진입장벽이 상당히 높지만 확실히 도커를 사용하면 DevOps영역에서 정말로 편해지는 것을 느낀다.
너무나 상이한 개발 및 배포환경에 머리 좀 뜯어본 경험이 있다면, 도커를 배우는데 시간투자하는 것은 아깝지 않은 선택인 듯하다.
