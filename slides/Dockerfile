# 기본이 되는 도커 이미지를 지정하고 base 라는 alias 를 지정한다.
FROM node:18-alpine AS base

# pnpm 을 설치한다.
RUN npm i -g pnpm

# base 이미지를 사용해 dependencies 라는 alias 를 지정하고 새로운 레이어를 생성한다. 
FROM base AS dependencies

# 기본 디렉토리를 /app 을 설정하고 copy 및 run 을 실행한다.
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN pnpm install

# base 이미지를 사용해 build 레이어를 생성한다.
FROM base AS build

# 새로운 이미지이기에 다시 workdir 설정한다.
WORKDIR /app

# install 된 파일들을 복사하고 dependency 들도 복제 해온다.
COPY . .
COPY --from=dependencies /app/node_modules ./node_modules
RUN pnpm build && pnpm prune --prod

# 마지막으로 nginx 기반의 이미지를 가져와서 최종 deploy 용으로 사용한다
FROM nginx:1.23.3-alpine AS deploy

# 마찬가지로 새로운 workdir 을 설정한다.
WORKDIR /usr/share/nginx/html

# 기존에 있던 것들을 지우고 빌드된 js 들을 복제한다.
RUN rm -rf ./*
COPY --from=build /app/dist/ .

# CMD 보다는 ENTRYPOINT 를 사용하세요.
ENTRYPOINT [ "nginx", "-g", "daemon off;"]