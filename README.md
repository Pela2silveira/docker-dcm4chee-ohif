# docker-dcm4chee-ohif
Deploy Dcm4chee-arc-light with ohif viewer

Prerequisites:
docker jq make 

Clone this repo in your servers

Set your docker-compose.env variables. See examples in both folders.

If you want, you can do it  in 2 servers, one for reverse proxy and ohif, the other for DCM4CHEE

In PACS server:
$cd pacs
$make all


In Nginx server:
$cd nginx
$make all

## Logs de error WildFly → Loki (opcional)

En la VM del PACS, Alloy tailea `server.log`, agrupa stack traces y **pushea solo**
líneas/bloques con `ERROR` / `Exception` / `FATAL` / `Caused by:` al Loki de
[andes/observabilidad](https://github.com/andes/observabilidad) (LAN, sin auth).

```bash
cd pacs
cp alloy.env.example alloy.env
# editar LOKI_PUSH_URL=http://<host-obs>:8090/loki/api/v1/push

make logs-obs-up      # levanta alloy-logs (overlay)
make logs-obs-logs    # logs del shipper
make logs-obs-down
```

En Grafana Explore (Loki):

```logql
{job="dcm4chee-arc"} |= "Exception"
```

Requiere que exista el volume host `/var/local/dcm4chee-arc/wildfly/log/server.log`
(compose de `arc`).