FROM nginx
RUN rm -rf /usr/share/nginx/html/index.html
RUN rm -rf /etc/nginx/nginx.conf 
ADD nginx.conf /etc/nginx/nginx.conf
ADD static/* /usr/share/nginx/html/
# (*) ante only static folder lo unde files ni matarame tisukuntumdhi, so nadhuke * tisesam
# CMD ekkada avasram ledu yendhuku ante.. Adi nginx image lo ney undhi nginx start chestumdhi.. nginx hey oka server malli speicial gha start avvu ani cheppakkra ledu..
