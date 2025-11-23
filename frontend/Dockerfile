# ====== BUILD STAGE ======
FROM node:20-alpine AS build

WORKDIR /app

# Copy package files and install deps
COPY package*.json ./
RUN npm install

# Copy rest of the app and build
COPY . .
# If your build script name is different, change this
RUN npm run build

# ====== RUNTIME STAGE ======
FROM nginx:alpine

# Remove default nginx static content
RUN rm -rf /usr/share/nginx/html/*

# Copy built frontend to nginx html dir
COPY --from=build /app/build /usr/share/nginx/html

# If you need to change API URL, you normally do that via env at build time (REACT_APP_*)
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
