# build stage
FROM node:14-stretch as build-stage

USER node

WORKDIR /target

COPY --chown=node:node . .
RUN npm ci
RUN npm run build

# runtime stage
FROM nginx:1.17
COPY --from=build-stage /target/build /usr/share/nginx/html