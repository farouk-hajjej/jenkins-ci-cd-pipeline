FROM node:lts-slim as runtime

WORKDIR /app

COPY package*.json ./

RUN npm cache clean --force && npm ci

# Donner les droits d'exécution aux binaires dans node_modules/.bin
RUN chmod -R 755 ./node_modules/.bin

COPY . .

RUN npm run build

EXPOSE 3001

CMD [ "npx", "serve", "-s", "build" ]
