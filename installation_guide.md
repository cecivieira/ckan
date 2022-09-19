# Guia de instalação CKAN

1. Clone o repositório git@github.com:cecivieira/ckan.git
2. Dê checkout na branch ckan-2.9.5-ana
3. Navegue até contrib/docker
4. Copie o arquivo .env.template
5. Renomeie a cópia para .env
6. Construa as imagens: docker-compose build
7. Suba as imagens: docker-compose up
8. Crie atalhos para os volumes do docker:
```
# Find the path to a named volume
docker volume inspect docker_ckan_home | jq -c '.[] | .Mountpoint'
# "/var/lib/docker/volumes/docker_ckan_config/_data"

export VOL_CKAN_HOME=`docker volume inspect docker_ckan_home | jq -r -c '.[] | .Mountpoint'`
echo $VOL_CKAN_HOME

export VOL_CKAN_CONFIG=`docker volume inspect docker_ckan_config | jq -r -c '.[] | .Mountpoint'`
echo $VOL_CKAN_CONFIG

export VOL_CKAN_STORAGE=`docker volume inspect docker_ckan_storage | jq -r -c '.[] | .Mountpoint'`
echo $VOL_CKAN_STORAGE
```
9. Configure o banco de dados do datastore: docker exec ckan /usr/local/bin/ckan -c /etc/ckan/production.ini datastore set-permissions | docker exec -i db psql -U ckan
10. Abra o arquivo production.ini: sudo vim $VOL_CKAN_CONFIG/production.ini
11. Na variável ckan.plugins, adicione datastore datapusher
12. Descomente a variável ckan.datapusher.formats
13. Reinicie o container ckan: docker-compose restart ckan
14. Na interface web crie uma usuária
15. Atribua a essa usuária poderes de sysadmin: docker exec -it ckan /usr/local/bin/ckan -c /etc/ckan/production.ini sysadmin add <usuaria>


It is working when I set SSL_VERIFY = False in /usr/lib/ckan/datapusher/src/datapusher/datapusher/jobs.py. It would mean that the parameter ckan.datapusher.ssl_verify = False in my config file is ignored.

datapusher_settings.py

ckan.datapusher.url = http://127.0.0.1:8800/
