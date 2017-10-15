FROM nginx:latest
MAINTEINER Company <company.ru>

ADD ./etc/nginx/conf.d/* /etc/nginx/conf.d/

RUN mkdir /opt/app-ui
COPY ./static/app-ui /opt/app-ui
