FROM node:latest
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 8103
CMD [ "npm", "run", "start:node" ]