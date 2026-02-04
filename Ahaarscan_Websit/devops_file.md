## Backend Dockerfile 
```
# Use official Node.js LTS
FROM node:22

# Set working directory
WORKDIR /app

# Copy full backend source (includes .env)
COPY ./qr-world-backend ./

# Install dependencies (needed for build)
RUN npm install

# Build the AdonisJS app
RUN npm run build

# Copy .env file into the build directory
RUN cp .env build/.env

# Switch to build directory
WORKDIR /app/build

# Install only production dependencies
RUN npm ci --omit=dev

# Expose application port
EXPOSE 3000

# Start the production server
CMD ["node", "bin/server.js"]


```
---
---
## Admin/Customers/Restaurants Frontend Dockerfile 
```
# Use lightweight NGINX image
FROM nginx:alpine

# Copy your React build folder directly into NGINX html folder
COPY qr-world-admin/dist /usr/share/nginx/html

# Copy custom NGINX config
COPY ./nginx.conf /etc/nginx/conf.d/default.conf

# Expose HTTP port
EXPOSE 80

# Run NGINX in foreground
CMD ["nginx", "-g", "daemon off;"]

```
---
---
## Admin/Customers/Restaurants Nginx File 
```
server {
  listen 80;
  server_name _;

  root /usr/share/nginx/html;
  index index.html;

  # Handle React Router (SPA)
  location / {
    try_files $uri /index.html;
  }

  # Cache and compress static assets
  location ~* \.(?:ico|css|js|gif|jpe?g|png|woff2?|eot|ttf|svg)$ {
    expires 1y;
    access_log off;
    add_header Cache-Control "public, max-age=31536000, immutable";
    try_files $uri /index.html;
  }

  # Gzip compression for smaller file sizes
  gzip on;
  gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss image/svg+xml;
  gzip_min_length 256;
  gzip_vary on;
  gzip_proxied any;

  # Block hidden files (.env, .git, etc.)
  location ~ /\. {
    deny all;
  }
}
```
---
---



 
