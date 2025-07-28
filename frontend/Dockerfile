FROM node:lts-slim as build

WORKDIR /app

COPY package*.json ./

# Supprimer node_modules s'il existe (dans le contexte Docker build ça peut être utile si tu copies node_modules)
RUN rm -rf node_modules

RUN npm cache clean --force && npm ci && npm install -f react-scripts

COPY . .

RUN npm run build

EXPOSE 3001

CMD [ "npx", "serve", "-s", "build" ]
#test
