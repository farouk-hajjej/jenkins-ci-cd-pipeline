FROM node:lts-slim as build

WORKDIR /app

COPY package*.json ./

RUN npm cache clean --force

# Installer les dépendances avec npm ci
RUN npm ci

# Donner les droits d'exécution à tous les scripts dans node_modules/.bin
chmod +x node_modules/.bin/react-scripts

COPY . .

RUN npm run build

EXPOSE 3001

CMD [ "npx", "serve", "-s", "build" ]
