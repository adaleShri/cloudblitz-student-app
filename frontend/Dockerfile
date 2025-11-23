# ====== BUILD STAGE ======
FROM node:20-alpine AS build

WORKDIR /app

# Copy package files and install deps
COPY package*.json ./
RUN npm install

# Copy rest of the app and build
COPY . .
RUN npm run build     # Vite writes to /app/dist

# ====== RUNTIME STAGE ======
FROM nginx:alpine

# Remove default nginx static content
RUN rm -rf /usr/share/nginx/html/*

# ⬇️ IMPORTANT: use /app/dist, NOT /app/build
COPY --from=build /app/dist /usr/share/nginx/html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
