# Stage 1: Build the application
FROM node:20 AS builder
# FROM tells Docker which base image to use.
# node:20 is the official Node.js 20 image from Docker Hub.
# AS builder gives this stage a name so we can refer to it later (multi-stage builds).

WORKDIR /app
# Sets the working directory inside the container.
# All following commands will run as if they were inside /app

# Copy package.json and package-lock.json (or yarn.lock)
COPY package*.json ./
# Copies package.json and package-lock.json (or yarn.lock) from your host (local machine) to the container.
# The * is a wildcard that matches both files.
# This is done first because it allows caching of dependency installation, which speeds up rebuilds.

# Install dependencies
RUN npm install
# Installs the app's dependencies using npm.
# This runs inside the container, not on your local machine.

# Copy all source files
COPY . .
# Copies all files from your project folder into the container (. → . inside /app).
# Now the container has the full app source code, including src/, public/, vite.config.ts, etc.

# Run the Vite build
RUN npm run build
# Executes the Vite build process inside the container.
# Generates a dist/ folder with production-ready static files (HTML, CSS, JS, etc.).

# Stage 2: Serve with Nginx
FROM nginx:stable-alpine
# Starts a new build stage, using the official Nginx image based on Alpine Linux (tiny and fast).
# This container will only run the compiled files, not the Node.js environment — so it's smaller and faster.

# Remove the default nginx index page
RUN rm -rf /usr/share/nginx/html/*
# Deletes the default Nginx welcome page and placeholder files.
# We want to replace them with our app files.

# Copy built app from previous stage
COPY --from=builder /app/dist /usr/share/nginx/html
# Copies the built app (from Stage 1’s /app/dist) into Nginx's default public directory (/usr/share/nginx/html).
# This is where Nginx looks for files to serve.

# Copy custom nginx config (optional)
# COPY nginx.conf /etc/nginx/nginx.conf

# Expose port 80
EXPOSE 80
# Informs Docker that this container listens on port 80 (standard HTTP port).
# This doesn't actually publish the port — that's done via docker run -p.

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
# Sets the default command when the container starts.
# nginx -g "daemon off;" tells Nginx to run in the foreground, not as a background service.
# Docker needs this so the container doesn't exit immediately.