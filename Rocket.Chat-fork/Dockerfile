# Build Stage
FROM node:18-bullseye AS build

# Install Meteor
# Release 3.0.4 matches typical recent Rocket.Chat versions, but we use the install script to be safe
RUN curl https://install.meteor.com/ | sh

# Install Yarn
RUN corepack enable && corepack prepare yarn@4.11.0 --activate

WORKDIR /app

# Copy package.json and yarn.lock to install dependencies
COPY . .

# Install dependencies
RUN yarn install

# Build the Meteor app
# This runs the "build" script we added to apps/meteor/package.json
# resulting in a Node.js bundle in apps/meteor/dist
RUN yarn workspace @rocket.chat/meteor run build

# Runtime Stage
FROM node:18-bullseye-slim

WORKDIR /app

# Copy the built artifact from the build stage
COPY --from=build /app/apps/meteor/dist/bundle /app/bundle

# Install production dependencies for the Meteor bundle
WORKDIR /app/bundle/programs/server
RUN npm install

WORKDIR /app/bundle

# Environment variables
ENV PORT=3000
ENV ROOT_URL=http://localhost:3000

EXPOSE 3000

# Start the application
CMD ["node", "main.js"]
