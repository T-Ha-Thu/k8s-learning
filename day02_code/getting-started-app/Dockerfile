FROM node:18-alpine

RUN sed -i 's|https://dl-cdn.alpinelinux.org|https://mirrors.aliyun.com|g' /etc/apk/repositories

RUN apk add --no-cache python3 make g++

WORKDIR /app 

COPY . .

RUN npm install --production

CMD ["node", "src/index.js"]

EXPOSE 3000
