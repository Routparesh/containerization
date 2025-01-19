FROM nginx
LABEL "project"="vprofile"
LABEL "Auther"="Paresh"

RUN rm -rf /etc/nginx/nginx.conf.d/default.conf
COPY nginvproapp.conf /etc/nginx/nginx.conf.d/vproapp.conf
