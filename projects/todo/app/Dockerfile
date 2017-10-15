FROM vlfedotov/b2b-python27:latest
MAINTAINER CompanyInc <company.ru>

RUN mkdir /opt/app
COPY ./app /opt/app

WORKDIR /opt/app
RUN pip install -r requirements.txt && pip install gunicorn

COPY ./etc/entrypoint.sh /opt/app/entrypoint.sh
RUN chmod +x ./entrypont.sh

ENTRYPOINT ["/bin/sh", "entrypoint.sh"]