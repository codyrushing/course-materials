FROM node:14-stretch

USER node

WORKDIR /home/node/code

EXPOSE 3000

# copy file(s) from source to destination
COPY --chown=node:node . .

RUN rm -rf node_modules
RUN npm ci

CMD ["node", "index.js"]