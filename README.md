# Docker - TP 3

### Tous d'abord installer Node.js. Cela nous permet de pouvoir utiliser `npm` et `npx`.

### Les définitions :
✅ `Node.js` : Un environnement qui permet d'exécuter du `JavaScript `en dehors du navigateur (serveur, scripts, outils).  
✅ `npm` (Node Package Manager) : Un gestionnaire de paquets pour installer, gérer et partager des bibliothèques `JavaScript`.  
✅ `npx` : Un outil fourni avec `npm `qui permet d'exécuter directement des paquets sans les installer globalement.

- Pour installer Node.js :
```bash
# Ajouter le dépôt officiel :
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -

# Installer Node.js :
sudo apt install -y nodejs

# Vérifier l'installation :
node -v
npm -v
npx -v

```
### Après avoir installé `npx`, initialiser un projet `React` vierge :

```bash
npx create-react-app my-app
cd my-app

```

- À quoi sert `npx` ici ?
  - Il permet d'exécuter des commandes sans installer globalement des paquets.
  - Il télécharge et exécute un paquet temporairement, puis le supprime après exécution.

- Qu'est-ce que `create-react-app` ?
  - create-react-app est un outil officiel de `Facebook` pour générer rapidement un projet React.

- Pourquoi l'utiliser ?
  - Il crée une structure de projet prête à l'emploi.
  - Il configure automatiquement `Webpack`, `Babel` et d'autres outils.
  - Il permet de démarrer un projet `React` sans configuration complexe.

- Comment utiliser `create-react-app` ?

  - Avec `npx` (pas besoin d'installation préalable) :
  - Cela crée un dossier` my-app `avec un projet `React `à l’intérieur.

### Vérifier si l'application fonctionne :

```bash
npm start

```
### L’application doit s’ouvrir sur `http://localhost:3000` !


## Créer le `Dockerfile` avec Multi-Stage Build
- À la racine du projet `my-app`, crée un fichier nommé `Dockerfile` :
```yaml
# Étape 1 : Construction de l’application React
FROM node:18-alpine AS build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

# Étape 2 : Serveur web pour l’application
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
- Dans le dossier `my-app`, exécute les commandes suivantes :

  - Construire l’image :
```bash
docker build -t react-app .
```
  - Lancer le conteneur :
```bash 
docker run -d -p 8080:80 react-app
```

### L’application doit être accessible sur `http://localhost:8080`.

### Ajouter un `.dockerignore` pour optimiser la build

- Crée un fichier `.dockerignore` à la racine du projet avec :
```nginx
node_modules
build
.dockerignore
Dockerfile
.git
```
### Cela permet d'éviter de copier inutilement des fichiers lors de la build.

![image](https://github.com/user-attachments/assets/d2fdf04d-af44-4559-b9c1-acbb77aaf986)