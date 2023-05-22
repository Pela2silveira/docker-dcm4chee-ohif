include *.env

configure:
	sed 's/<docker-host>/${ARCHIVE_HOST}/g;s/<aet>/${AE_TITLE}/g' templates/viewer-conf.js.template > conf/viewer-conf.js;
	mkdir -p conf/sites-enabled
	sed 's/<pacs-host>/${PACS_HOST}/g' templates/sites-enabled/auth > conf/sites-enabled/auth;
	sed 's/<pacs-host>/${PACS_HOST}/g' templates/sites-enabled/pacs > conf/sites-enabled/pacs;

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
