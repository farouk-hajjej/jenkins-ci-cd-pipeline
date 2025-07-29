FROM node:lts-slim as build

WORKDIR /app

COPY package*.json ./

RUN npm cache clean --force
RUN rm -rf node_modules 

# Installer les dépendances avec npm ci
RUN npm install 

# Donner les droits d'exécution à tous les scripts dans node_modules/.bin
RUN chmod -R u+x ./node_modules/.bin

COPY . .

RUN npm run build

EXPOSE 3001

CMD [ "npx", "serve", "-s", "build" ]
