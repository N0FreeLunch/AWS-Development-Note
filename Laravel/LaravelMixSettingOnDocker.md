## 노드 JS 설치 코드
- Reference : https://stackoverflow.com/questions/36399848/install-node-in-dockerfile
```dockerfile
ENV NODE_VERSION=16.13.0
RUN apt install -y curl
RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
ENV NVM_DIR=/root/.nvm
RUN . "$NVM_DIR/nvm.sh" && nvm install ${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm use v${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm alias default v${NODE_VERSION}
ENV PATH="/root/.nvm/versions/node/v${NODE_VERSION}/bin/:${PATH}"
RUN node --version
RUN npm --version
```
- 현재 NodeJS 버전을 입력 해 준다.
- 최신 NVM 버전을 입력 해 준다. (`https://github.com/nvm-sh/nvm`에서 최신 NVM 버전을 확인한다.)

## nodeJS로 빌드하기
- `npm start` 또는 라라벨 mix의 `npm run watch`를 사용할 때 작업이 끝나지 않고 프로세스가 계속 실행되어 버리기 때문에 도커 실행 로직이 중간에 막혀 버린다. 따라서 도커에서 이런 watch 모드를 실행하기 위해서는 별도의 프로세스로 분리해서 실행하는 것이 필요하며, 하나의 프로세스로 켜지며 외부에서 컨트롤 되어야 한다. 따라서 PM2를 사용해서 컨트롤 하는 방식을 사용한다.
- 로컬에서는 `npm run watch` 명령을 실행해야 하며 개발 서버나 프로덕션 서버에서는 `npm run production`을 실행해야 한다.
- `npm run watch`는 서버가 켜질 때 항상 실행되어야 하며, `npm run production`는 서버가 빌드 될 때 1회 실행되어야 한다.

## PM2
- https://pm2.keymetrics.io/docs/usage/quick-start/
- 로컬에서  도커에서 명령을 실행할 폴더로 가서 `pm2 start "npm run watch"`을 실행한다.
- watch 모드를 실행하지 않는다면 굳이 pm2를 통해서 관리할 필요가 없다.
- 로컬 환경에서 도커 기동 시 webpack의 로그를 확인하고 싶다면 `docker-compose exec target_docker_name pm2 logs`를 실행해서 도커에서 실행되고 있는 webpack의 watch 모드의  확인할 수 있다.