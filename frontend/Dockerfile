# Name the node stage "builder"
FROM node:latest AS builder
# Set working directory
WORKDIR /frontend
# Copy all files from current directory to working dir in image
COPY . .
# install node modules and build assets
RUN npm install && npm run build


FROM nginx:stable-alpine as production-stage
WORKDIR /usr/share/nginx/html
RUN rm -rf ./*
COPY --from=builder /frontend/dist .
ENTRYPOINT ["nginx", "-g", "daemon off;"]
