# Build stage
FROM node:13-alpine as build

WORKDIR /client

# Copy package.json and package-lock.json for npm ci
COPY package*.json ./
RUN npm i --only=production

# Copy the rest of the application code
COPY . .

# Production stage
FROM node:13-alpine

WORKDIR /client

# Copy the entire application code from the build stage
COPY --from=build /client .

CMD ["npm", "start"]
