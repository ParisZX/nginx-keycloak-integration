FROM nginx:latest

RUN apt-get update && \
  apt-get install -y libnginx-mod-http-auth-jwt

COPY nginx.conf /etc/nginx/nginx.conf
