# Minimal demo image for slides 58-59 ("Do it live with any small image").
# nginx serving a static page on port 8081 - same port as webapp-api-svc targetPort.
FROM nginx:1.27
COPY default.conf /etc/nginx/conf.d/default.conf
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 8081
