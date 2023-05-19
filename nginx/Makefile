include *.env

configure:
	sed 's/<docker-host>/${ARCHIVE_HOST}/g;s/<aet>/${AE_TITLE}/g' templates/viewer-conf.js.template > conf/viewer-conf.js;

run:
	docker compose -p dcm4chee up -d

stop:
	docker compose -p dcm4chee stop

start:
	docker compose -p dcm4chee start

delete:
	docker compose -p dcm4chee down

all: 
	make configure
	make run
