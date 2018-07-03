# duo-dang
Canned Duo Environment for Web Applications. Deploys Duo Access Gateway, Duo Network Gateway, and OpenLDAP as an authentication source, co-located on one host with Duo Network Gateway front-end. Suitable for lab or testing environments.

Leverages the osixia/docker-openldap image for OpenLDAP services.
https://github.com/osixia/docker-openldap

## Pre-Requisites
1. Web-facing host with docker and docker-compose installed
2. Allow inbound ports 80, 443, 8443, 8888, (optional: 22 if planning on using SSH through DNG)
3. DNS records for your Duo Access Gateway and Duo Network Gateway

## Deploy
```
docker-compose -p dang -f dang.yml up -d
```
## Configure

### Duo Access Gateway
1. Access your Duo Access Gateway management GUI at `https://YOURHOSTNAME:8443`
2. Navigate to 'Authentication Sources' and select OpenLDAP
3. Input the IP address (disregard CIDR) of your 'openldap' container. To find this, run `docker network inspect dang_default` and look for the ip address listed under the 'openldap' container.
4. Specify port 389 and leave 'Transport Type' as 'Clear'
5. Specify the following:

Attributes | uid,mail

Search base | dc=example,dc=org

Search attributes | uid,mail

Search username | cn=readonly,dc=example,dc=org

Search password | readonly

### Duo Network Gateway
Follow directions at https://duo.com/docs/dng#protect-a-web-application-with-duo-network-gateway-, treating Duo Access Gateway as an internal web application. You will need to generate a self-signed certificate for the Duo Access Gateway in order to use HTTPS internally (which the Duo Access Gateway requires). The internal URL will be `https://access-gateway:4443`

### OpenLDAP if using example LDIF "Riverdale"
```
docker cp ./seedldif/riverdale.ldif openldap:/tmp/riverdale.ldif
docker exec -it openldap ldapadd -x -D "cn=admin,dc=example,dc=org" -w admin -H ldap:// -f /tmp/riverdale.ldif
```
