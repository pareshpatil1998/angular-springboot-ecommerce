# Stage 1: Build the Angular static assets
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install --legacy-peer-deps
# Install matching Angular 16 CLI locally
RUN npm install @angular/cli@16.2.0 @angular-devkit/build-angular@16.2.0 --legacy-peer-deps
COPY . .
RUN npx ng build --configuration production

# Stage 2: Serve files using Nginx
FROM nginx:alpine
COPY --from=builder /app/dist/* /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]