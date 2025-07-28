FROM node:lts-slim as runtime

WORKDIR /app

COPY package*.json ./


#RUN npm ci
RUN npm cache clean --force && \
    npm ci  && \
    rm -rf node_modules 


COPY . .

RUN npm run build

EXPOSE 3001

CMD [ "npx", "serve", "-s", "build" ]
