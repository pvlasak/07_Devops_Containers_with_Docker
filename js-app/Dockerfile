FROM node:20-alpine

ENV MONGO_DB_USERNAME=admin \
    MONGO_DB_PWD=password

RUN mkdir -p /home/app

COPY ./app /home/app

WORKDIR /home/app

RUN npm install
# no need for /home/app/server.js because of WORKDIR
CMD ["node", "server.js"]
