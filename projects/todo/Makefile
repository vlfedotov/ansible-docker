include .env

PROJECT_NAME = todobackend-${ROLE}

DOCKER_REGISTRY = ${DOCKER_REGISTRY}
DOCKER_USER ?= todobackend
DOCKER_PASSWORD ?= 123456

PROD_COMPOSE_FILE = ./docker-compose.prod.yml

PROJECT_VERSION = ${VERSION}

MAIN_DATABASE ?= todobackend

.PHONY: login logout pull start_db configure start status stop recover_data backup_all

login:
	${INFO} "Logging into Docker registry $(DOCKER_REGISTRY)..."
	@ docker login -u $(DOCKER_USER) -p $(DOCKER_PASSWORD) $(DOCKER_REGISTRY)
	${INFO} "Logged into Docker registry $(DOCKER_REGISTRY)"

logout:
	${INFO} "Logging out of Docker registry $(DOCKER_REGISTRY)..."
	@ docker logout $(DOCKER_REGISTRY)
	${INFO} "Logged out of Docker registry $(DOCKER_REGISTRY)"

pull:
	${INFO} "Pulling project with $(PROJECT_VERSION)..."
	@ docker-compose -p $(PROJECT_NAME) -f $(RUN_COMPOSE_FILE) pull
	${INFO} "Done pulling project"

start_db:
	${INFO} "Creating db..."
	@ docker volume create --name=todobackend-${ROLE}-mysqldata
	${INFO} "Starting db..."
	@ docker-compose -p $(PROJECT_NAME) -f $(RUN_COMPOSE_FILE) up -d todobackend-db
	@ sleep 10
	${INFO} "Creating db..."
	@ echo 'create database if not exists ${MAIN_DATABASE} character set utf8 collate utf8_general_ci;' | docker exec -i $$(docker-compose -p ${PROJECT_NAME} -f ${RUN_COMPOSE_FILE}  ps -q todobackend-db) /usr/bin/mysql -u root --password=123456
	${INFO} "Db started and configured"

configure:
	${INFO} "Configuring project..."
	@ docker-compose -p $(PROJECT_NAME) -f $(RUN_COMPOSE_FILE) run --rm todobackend-app python manage.py migrate --noinput
	${INFO} "Done configuring project"

start:
	${INFO} "Running project..."
	@ docker-compose -p $(PROJECT_NAME) -f $(RUN_COMPOSE_FILE) up -d
	${INFO} "Project successfully started..."

status:
	@ docker-compose -p $(PROJECT_NAME) -f $(RUN_COMPOSE_FILE) ps

stop:
	${INFO} "Stopping project..."
	@ docker-compose -p $(PROJECT_NAME) -f $(RUN_COMPOSE_FILE) down

recover_data:
	${INFO} "Start recovering data..."
# !!! take the most recent archives to recover from !!!
	@ gunzip < ~/backups/todobackend-${ROLE}.${MAIN_DATABASE}.`date +%F`.sql.gz | docker exec -i $$(docker-compose -p ${PROJECT_NAME} -f ${RUN_COMPOSE_FILE}  ps -q todobackend-db)  /usr/bin/mysql -u root --password=123456 ${MAIN_DATABASE}
	@ tar xzf ~/backups/todobackend-${ROLE}.media.sql.gz
	${INFO} "Done recovering data"

backup_all:
	${INFO} "Start dumping data..."
	@ docker exec $$(docker-compose -p ${PROJECT_NAME} -f ${RUN_COMPOSE_FILE}  ps -q todobackend-db) /usr/bin/mysqldump -u root --password=123456 ${MAIN_DATABASE} | gzip > ~/backups/todobackend-${ROLE}.${MAIN_DATABASE}.`date +%F`.sql.gz
	@ gzip ~/backups/todobackend-${ROLE}.media.`date +%F`.tgz
	${INFO} "Done dumping data"

YELLOW := "\e[1;33m"
NC := "\e[0m"

INFO := @bash -c '\
    printf $(YELLOW); \
    echo "=> $$1"; \
    printf $(NC)' VALUE
