FROM node:24-slim
WORKDIR /app
COPY package.json .
RUN npm i
COPY . .
ENTRYPOINT ["node", "main.mjs"]
