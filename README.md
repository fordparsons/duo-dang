# duo-dang
Canned Duo Environment for Web Applications



## LDAP Modification
```
docker cp ./seedldif/riverdale.ldif openldap:/tmp/riverdale.ldif
docker exec -it openldap ldapadd -x -D "cn=admin,dc=example,dc=org" -w admin -H ldap:// -f /tmp/riverdale.ldif
```
