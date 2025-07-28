FROM node:lts-slim as runtime

WORKDIR /app

# Copier package.json et package-lock.json
COPY package*.json ./

# Installer les dépendances
RUN npm cache clean --force && npm install -f

# Donner les droits d'exécution aux binaires dans node_modules/.bin
RUN chmod -R 755 ./node_modules/.bin

# Copier le reste du projet
COPY . .

# Builder l'application React
RUN npm run build

EXPOSE 3001

CMD [ "npx", "serve", "-s", "build" ]
