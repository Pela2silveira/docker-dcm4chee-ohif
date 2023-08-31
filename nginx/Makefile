include *.env

configure:
	sed 's/<docker-host>/${ARCHIVE_HOST}/g;s/<aet>/${AE_TITLE}/g' templates/viewer-conf.js.template > conf/viewer-conf.js;
	mkdir -p conf/sites-enabled
	sed 's/<pacs-host>/${PACS_HOST}/g;s/<docker-host>/${ARCHIVE_HOST}/g' templates/sites-enabled/auth > conf/sites-enabled/auth;
	sed 's/<pacs-host>/${PACS_HOST}/g;s/<docker-host>/${ARCHIVE_HOST}/g' templates/sites-enabled/pacs > conf/sites-enabled/pacs;

run:
	${DOCKER_COMPOSE_CMD} -p nginx-ohif up -d

stop:
	${DOCKER_COMPOSE_CMD} -p nginx-ohif stop

start:
	${DOCKER_COMPOSE_CMD} -p nginx-ohif start

delete:
	${DOCKER_COMPOSE_CMD} -p nginx-ohif down

all: 
	make configure
	make run
