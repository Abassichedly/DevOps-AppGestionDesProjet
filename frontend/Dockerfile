# ---- Étape 1 : Build Angular ----
FROM node:22-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build -- --configuration production

# ---- Étape 2 : Nginx ----
FROM nginx:alpine
COPY --from=build /app/dist/*/browser/ /usr/share/nginx/html/
EXPOSE 80
