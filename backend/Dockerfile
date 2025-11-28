FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

RUN npm install cors

COPY . .

EXPOSE 8081

CMD ["node","server.js"]
