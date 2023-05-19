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