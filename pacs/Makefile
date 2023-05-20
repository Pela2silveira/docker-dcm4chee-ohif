include *.env

configure:
	sed 's/<andes-secret>/${ANDES_SECRET}/g' templates/andes.json.template > conf/andes.json;
	sed 's/<docker-host>/${ARCHIVE_HOST}/g' templates/ui.json.template > conf/ui.json;
	sed 's/<aet>/${AE_TITLE}/g' templates/dicom-dcm4chee-arc.properties.template > conf/dicom-dcm4chee-arc.properties;
	sed 's/<aet>/${AE_TITLE}/g' templates/weasis-pacs-connector.properties.template > conf/weasis-pacs-connector.properties;

run:
	make configure
	docker compose -p dcm4chee up -d

stop:
	docker compose -p dcm4chee stop

start:
	docker compose -p dcm4chee start

delete:
	docker compose -p dcm4chee down
	rm -r conf/*
	sudo rm -rf /var/local/dcm4chee-arc

add_clients:
	docker exec -it dcm4chee-keycloak-1 kcadm.sh config credentials --server http://dcm4chee-keycloak-1:8880/auth --realm master --user ${KEYCLOAK_USER} --password ${KEYCLOAK_PASSWORD};
	#docker cp conf/ohif.json dcm4chee-keycloak-1:ohif.json;
	docker cp conf/andes.json dcm4chee-keycloak-1:andes.json;
	docker cp conf/ui.json dcm4chee-keycloak-1:ui.json;
	docker exec -it dcm4chee-keycloak-1 kcadm.sh create clients -r dcm4che -f andes.json;
	#docker exec -it dcm4chee-keycloak-1 kcadm.sh create clients -r dcm4che -f ohif.json;
	docker exec -it dcm4chee-keycloak-1 kcadm.sh create clients -r dcm4che -f ui.json;
	docker exec -it dcm4chee-keycloak-1 kcadm.sh create roles -r dcm4che -s name=user
	docker exec -it dcm4chee-keycloak-1 kcadm.sh add-roles --uusername service-account-andes --rolename user -r dcm4che

add_user:
	docker exec -it dcm4chee-keycloak-1 kcadm.sh config credentials --server http://dcm4chee-keycloak-1:8880/auth --realm master --user ${KEYCLOAK_USER} --password ${KEYCLOAK_PASSWORD};
	docker exec -it dcm4chee-keycloak-1 kcadm.sh create users -r dcm4che -s username=imagenes -s enabled=true;

deploy_weasis:
	docker exec -it dcm4chee-arc-1 jboss-cli.sh -c --controller=localhost:9990
	
all:
	mkdir conf
	make configure
	make run
	sleep 20
	make add_clients
