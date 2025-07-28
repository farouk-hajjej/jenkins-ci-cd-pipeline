# Étape 1 : builder
FROM node:lts-slim as build

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build

# Étape 2 : serveur statique
FROM node:lts-slim as runtime

WORKDIR /app

# On copie uniquement le build final depuis l'étape précédente 
COPY --from=build /app/build ./build

EXPOSE 3001

CMD [ "npx", "serve", "-s", "build" ]
