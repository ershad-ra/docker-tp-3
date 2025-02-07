# Étape 1 : Construction de l’application React
# Utilise l'image Node.js 18 basée sur Alpine Linux (légère) et nomme cette étape "build"
FROM node:18-alpine AS build  
# Définit le répertoire de travail dans le conteneur à "/app"
WORKDIR /app  
# Copie uniquement les fichiers package.json et package-lock.json pour optimiser la mise en cache des dépendances
COPY package.json package-lock.json ./ 
# Installe les dépendances du projet React 
RUN npm install  
# Copie tous les fichiers du projet dans le conteneur
COPY . .  
# Exécute la commande pour compiler l’application React (génère les fichiers statiques dans le dossier "build")
RUN npm run build  


# Étape 2 : Serveur web pour l’application
# Utilise l'image Nginx basée sur Alpine Linux pour héberger les fichiers React
FROM nginx:alpine  
# Copie les fichiers générés à partir de l’étape "build" vers le dossier HTML de Nginx
COPY --from=build /app/build /usr/share/nginx/html  
# Indique que le conteneur écoute sur le port utilisé par Nginx
EXPOSE 80  
# Démarre Nginx et empêche le processus de s'exécuter en arrière-plan pour que le conteneur reste actif
CMD ["nginx", "-g", "daemon off;"]  
