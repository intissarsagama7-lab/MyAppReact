FROM node:lts-alpine
WORKDIR /app
COPY package*.json ./
RUN rm -f package-lock.json && npm install
COPY . .
RUN npm run build
RUN npm install -g serve
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]
