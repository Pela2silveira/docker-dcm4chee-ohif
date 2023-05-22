include *.env

configure:
	echo "generating configuration files..."
	mkdir -p conf
	sed 's/<andes-secret>/${ANDES_SECRET}/g' templates/andes.json.template > conf/andes.json;
	sed 's/<docker-host>/${ARCHIVE_HOST}/g' templates/ui.json.template > conf/ui.json;
	sed 's/<aet>/${AE_TITLE}/g' templates/dicom-dcm4chee-arc.properties.template > conf/dicom-dcm4chee-arc.properties;
	sed 's/<aet>/${AE_TITLE}/g' templates/weasis-pacs-connector.properties.template > conf/weasis-pacs-connector.properties;
	sed 's/<archive-name>/${ARCHIVE_DEVICE_NAME}/g' templates/weasis.ldif.template > conf/weasis.ldif;

run:
	make configure
	docker compose -p dcm4chee up -d

stop:
	docker compose -p dcm4chee stop

start:
	docker compose -p dcm4chee start

make delete:
	docker compose -p dcm4chee down
	echo "deleting configuration files.."
	sudo rm -r conf/*
	echo "deleting archive and keycloak servers..."
	sudo sudo rm -rf /var/local/dcm4chee-arc/wildfly
	sudo rm -rf /var/local/dcm4chee-arc/keycloak

delete_all:
	docker compose -p dcm4chee down
	echo "deleting configuration files.."
	rm -r conf/*
	echo "this will delete all images and running servers..."
	sudo rm -rf /var/local/dcm4chee-arc

add_clients:
	echo "connecting to keycloak..."
	docker exec -it dcm4chee-keycloak-1 kcadm.sh config credentials --server http://dcm4chee-keycloak-1:8880/auth --realm master --user ${KEYCLOAK_USER} --password ${KEYCLOAK_PASSWORD};
	#docker cp conf/ohif.json dcm4chee-keycloak-1:ohif.json;
	echo "copying configuration files to container..."
	docker cp conf/andes.json dcm4chee-keycloak-1:andes.json;
	docker cp conf/ui.json dcm4chee-keycloak-1:ui.json;
	echo "creating clients..."
	docker exec -it dcm4chee-keycloak-1 bash -c  'kcadm.sh create clients -r dcm4che -f andes.json' || echo 'client exist';
	#docker exec -it dcm4chee-keycloak-1 kcadm.sh create clients -r dcm4che -f ohif.json;
	docker exec -it dcm4chee-keycloak-1 bash -c 'kcadm.sh create clients -r dcm4che -f ui.json' || echo 'client exist';
	docker exec -it dcm4chee-keycloak-1 bash -c 'kcadm.sh create roles -r dcm4che -s name=user' || echo 'role exist'
	docker exec -it dcm4chee-keycloak-1 bash -c 'kcadm.sh add-roles --uusername service-account-andes --rolename user -r dcm4che' || echo 'client role exists'

add_user:
	docker exec -it dcm4chee-keycloak-1 kcadm.sh config credentials --server http://dcm4chee-keycloak-1:8880/auth --realm master --user ${KEYCLOAK_USER} --password ${KEYCLOAK_PASSWORD};
	docker exec -it dcm4chee-keycloak-1 kcadm.sh create users -r dcm4che -s username=imagenes -s enabled=true;

deploy_weasis:
	echo "copying deployments inside container..."
	docker cp deployments/weasis-ext.war dcm4chee-arc-1:weasis-ext.war
	docker cp deployments/weasis.war dcm4chee-arc-1:weasis.war
	docker cp deployments/weasis-i18n.war dcm4chee-arc-1:weasis-i18n.war
	docker cp deployments/weasis-pacs-connector.war dcm4chee-arc-1:weasis-pacs-connector.war
	echo "deploying weasis applications..."
	docker exec -it dcm4chee-arc-1 jboss-cli.sh -Djavax.net.ssl.trustStore=/opt/wildfly/domain/configuration/https.store -c --controller=localhost:9990 --command="deploy --force weasis-pacs-connector.war"
	docker exec -it dcm4chee-arc-1 jboss-cli.sh -Djavax.net.ssl.trustStore=/opt/wildfly/domain/configuration/https.store -c --controller=localhost:9990 --command="deploy --force weasis-i18n.war"
	docker exec -it dcm4chee-arc-1 jboss-cli.sh -Djavax.net.ssl.trustStore=/opt/wildfly/domain/configuration/https.store -c --controller=localhost:9990 --command="deploy --force weasis-ext.war"
	docker exec -it dcm4chee-arc-1 jboss-cli.sh -Djavax.net.ssl.trustStore=/opt/wildfly/domain/configuration/https.store -c --controller=localhost:9990 --command="deploy --force weasis.war"
	echo "copying weasis configuration file to container"
	docker cp conf/weasis-pacs-connector.properties dcm4chee-arc-1:/tmp/weasis-pacs-connector.properties
	docker cp conf/dicom-dcm4chee-arc.properties dcm4chee-arc-1:/tmp/dicom-dcm4chee-arc.properties
	echo "changing configuration in weasis pacs connector deployment"
	docker exec -it dcm4chee-arc-1 jboss-cli.sh -Djavax.net.ssl.trustStore=/opt/wildfly/domain/configuration/https.store -c --controller=localhost:9990 --command="deployment-overlay add --name=dcm4chee-arc --deployments=weasis-pacs-connector.war --content=WEB-INF/classes/weasis-pacs-connector.properties=/tmp/weasis-pacs-connector.properties,WEB-INF/classes/dicom-dcm4chee-arc.properties=/tmp/dicom-dcm4chee-arc.properties --redeploy-affected"
	echo "configuring permision for user to viewer.."
	docker cp conf/weasis.ldif dcm4chee-ldap-1:weasis.ldif
	docker exec -it dcm4chee-ldap-1 ldapmodify -x -w "${LDAP_SECRET}" -D "cn=admin,dc=dcm4che,dc=org" -H ldap://localhost:389 -f weasis.ldif

all:
	make configure
	make run
	sleep 20
	make add_clients
	make deploy_weasis
