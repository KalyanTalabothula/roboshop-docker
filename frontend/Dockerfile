FROM nginx:1.29.0-alpine
RUN rm /etc/nginx/nginx.conf /etc/nginx/conf.d/default.conf
RUN mkdir -p /var/cache/nginx/client_temp && \
        mkdir -p /var/cache/nginx/proxy_temp && \
        mkdir -p /var/cache/nginx/fastcgi_temp && \
        mkdir -p /var/cache/nginx/uwsgi_temp && \
        mkdir -p /var/cache/nginx/scgi_temp && \
        chown -R nginx:nginx /var/cache/nginx && \
        chown -R nginx:nginx /etc/nginx/ && \
        chmod -R 755 /etc/nginx/ && \
        chown -R nginx:nginx /var/log/nginx
RUN mkdir -p /etc/nginx/ssl/ && \
    chown -R nginx:nginx /etc/nginx/ssl/ && \
    chmod -R 755 /etc/nginx/ssl/
RUN touch /var/run/nginx.pid && \
    chown -R nginx:nginx /var/run/nginx.pid /run/nginx.pid
ADD nginx.conf /etc/nginx/nginx.conf
ADD static /usr/share/nginx/html/
USER nginx
CMD ["nginx", "-g", "daemon off;"]


# FROM nginx
# RUN rm -rf /usr/share/nginx/html/index.html
# RUN rm -rf /etc/nginx/nginx.conf 
# RUN rm -rf /etc/nginx/conf.d/default.conf
# ADD nginx.conf /etc/nginx/nginx.conf
# ADD static/ /usr/share/nginx/html/
# # (*) ante only static folder lo unde files ni matarame tisukuntumdhi, so nadhuke * tisesam
# # CMD ekkada avasram ledu yendhuku ante.. Adi nginx image lo ney undhi nginx start chestumdhi.. nginx hey oka server malli speicial gha start avvu ani cheppakkra ledu..
