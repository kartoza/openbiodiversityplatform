PROD_SERVER := bims-blog.kartoza.com
PROJECT_ID := bims-blog
SHELL := /bin/bash

SHELL := /bin/bash

default: start-web
setup-web: build start-web pause migrate collectstatic

build:
	@echo
	@echo "------------------------------------------------------------------"
	@echo "Building uwsgi"
	@echo "------------------------------------------------------------------"
	@docker-compose build uwsgi

permissions:
	@if [ ! -d "logs" ]; then mkdir logs; fi
	@if [ ! -d "media" ]; then mkdir media; fi
	@if [ ! -d "static" ]; then mkdir static; fi
	@if [ ! -d "backups" ]; then mkdir backups; fi
	@mkdir -p reports
	@mkdir -p sql
	@if [ -d "logs" ]; then sudo chmod -R a+rwx logs; fi
	@if [ -d "media" ]; then sudo chmod -R a+rwx media; fi
	@if [ -d "static" ]; then sudo chmod -R a+rwx static; fi
	@if [ -d "pg" ]; then sudo chmod -R a+rwx pg; fi
	@if [ -d "backups" ]; then sudo chmod -R a+rwx backups; fi
	@sudo chmod -R a+rwx reports sql

uwsgi:
	@echo
	@echo "------------------------------------------------------------------"
	@echo "Spin up UWSGI container"
	@echo "------------------------------------------------------------------"
	@docker-compose up -d uwsgi

start-web:
	@echo
	@echo "------------------------------------------------------------------"
	@echo "Spin up NGINX web and DB backups"
	@echo "------------------------------------------------------------------"
	@docker-compose up -d web
	@# Dont confuse this with the dbbackup make command below
	@# This one runs the postgis-backup cron container
	@# We add --no-recreate so that it does not destroy & recreate the db container
	@docker-compose up --no-recreate --no-deps -d dbbackups

db:
	@echo
	@echo "------------------------------------------------------------------"
	@echo "Running db"
	@echo "------------------------------------------------------------------"
	@docker-compose up -d db

# ----------------------------------------------------------------------------
#  Development
# ----------------------------------------------------------------------------

setup-dev: build uwsgi pause migrate collectstatic build-devweb devweb
start-dev: devweb

devweb: db
	@echo
	@echo "------------------------------------------------------------------"
	@echo "Running in DEVELOPMENT mode"
	@echo "------------------------------------------------------------------"
	@docker-compose up --no-deps -d devweb

build-devweb: db
	@echo
	@echo "------------------------------------------------------------------"
	@echo "Building devweb"
	@echo "------------------------------------------------------------------"
	@docker-compose build devweb

pause:
	@echo "Allow 10s for db to start up"
	@sleep 10

migrate:
	@echo
	@echo "------------------------------------------------------------------"
	@echo "Running migrate"
	@echo "------------------------------------------------------------------"
	@docker-compose exec uwsgi python manage.py migrate


collectstatic:
	@echo
	@echo "------------------------------------------------------------------"
	@echo "Collecting static"
	@echo "------------------------------------------------------------------"
	@docker-compose exec uwsgi python manage.py collectstatic --noinput

stop:
	@echo
	@echo "------------------------------------------------------------------"
	@echo "Stoppping and removing containers"
	@echo "------------------------------------------------------------------"
	@docker-compose down
