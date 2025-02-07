# Docker - TP 3

## Qu'est-ce qu'on fait ici ?
- On veut :

  - Construire une application React dans un conteneur.
  - Utiliser un serveur Nginx pour afficher cette application React.  
  On utilise Docker multi-stage build, ce qui signifie qu’on utilise plusieurs étapes pour construire l’image.

## 1. Tous d'abord installer Node.js. Cela nous permet de pouvoir utiliser `npm` et `npx`.

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
## 2. Après avoir installé `npx`, initialiser un projet `React` vierge :

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


## 3. Créer le `Dockerfile` avec Multi-Stage Build
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
- Étape 1 : Construire l'application` React`
  - On prend une image légère de` Node.js `(basée sur `Alpine` Linux) pour compiler notre application `React`.
```bash
FROM node:18-alpine AS build

```
  - On définit `/app `comme dossier de travail dans le conteneur. Tous les fichiers qu'on copie ou crée seront à cet endroit.
```bash
WORKDIR /app

```
  - On copie uniquement` package.json` et` package-lock.json` avant d’installer les dépendances.  
  - 👉 Cela optimise la mise en cache : si les fichiers n'ont pas changé, `Docker `ne réinstalle pas tout.
```bash
COPY package.json package-lock.json ./

```
  - On installe les dépendances `React` (`react`, `react-dom`, etc.).
```bash
RUN npm install

```
  - On copie tout le projet dans le conteneur.
```bash
COPY . .

```
  - On génère les fichiers statiques (`HTML`, `CSS`, `JS `optimisés) dans un dossier appelé` build/`.

```bash
RUN npm run build

```
- Étape 2 : Utiliser un serveur `Nginx`

  - On prend une image légère de `Nginx `pour afficher les fichiers` React`.
  - 👉 Pourquoi` Nginx `? Parce qu’il est rapide et optimisé pour servir des fichiers statiques.
```bash
FROM nginx:alpine

```
  - On copie les fichiers `React `(`build/`) du premier conteneur vers` /usr/share/nginx/html` de` Nginx`.
  - 👉 `Nginx` va alors afficher notre application !
```bash
COPY --from=build /app/build /usr/share/nginx/html

```
  - On dit à `Docker` que le serveur utilise le port `80` (le port` HTTP` standard).
```bash
EXPOSE 80

```
  - On démarre` Nginx `et on le force à rester au premier plan pour que le conteneur ne s'arrête pas.
```bash
CMD ["nginx", "-g", "daemon off;"]

```

### construire l'image et lancer le conteneur
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

## 4. Ajouter un `.dockerignore` pour optimiser la build

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