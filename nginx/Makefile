include *.env

configure:
	sed 's/<docker-host>/${ARCHIVE_HOST}/g;s/<aet>/${AE_TITLE}/g' templates/viewer-conf.js.template > conf/viewer-conf.js;
	mkdir -p conf/sites-enabled
	sed 's/<pacs-host>/${PACS_HOST}/g;s/<docker-host>/${ARCHIVE_HOST}/g' templates/sites-enabled/auth > conf/sites-enabled/auth;
	sed 's/<pacs-host>/${PACS_HOST}/g;s/<docker-host>/${ARCHIVE_HOST}/g' templates/sites-enabled/pacs > conf/sites-enabled/pacs;
	sed 's/<pacs-host>/${PACS_HOST}/g;s/<docker-host>/${ARCHIVE_HOST}/g' templates/sites-enabled/visor2 > conf/sites-enabled/visor2;
	sed 's/<docker-host>/${ARCHIVE_HOST}/g' templates/default.conf.template > conf/conf.d/default.conf;

run:
	docker compose -p nginx-ohif up -d

stop:
	docker compose -p nginx-ohif stop

start:
	docker compose -p nginx-ohif start

restart:
	make stop
	make start

delete:
	docker compose -p nginx-ohif down

all: 
	make configure
	make run
