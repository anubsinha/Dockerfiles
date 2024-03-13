# Base image
FROM node:18-alpine AS base

# Set a working directory
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./


# Install dependencies in a layer
RUN npm install --force 

# Copy the rest of your app's source code
COPY . .

# Build stage for compiling the app
FROM base AS builder

RUN npm run build:staging

# Final stage
FROM node:18-alpine AS final

RUN npm install -g pm2

# Environment variable for runtime (ensures it's set for the CMD instruction)
ENV NODE_ENV=${NODE_ENV}

WORKDIR /app

# Copy the built app and node_modules from the previous stages
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/package.json ./package.json

# Expose the port the app runs on
EXPOSE 3000

# Start command
CMD pm2-runtime start npm -- start