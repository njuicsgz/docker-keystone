# How to build
```bash
docker  build --rm -t="krystism/openstack-keystone" .
```

# Environment Variables
you can pass these arguments using **docker run -e** to override default value.
* MYSQL_ROOT_PASSWORD
* OS_TENANT_NAME : default "admin"
* OS_USER_NAME : default "admin"
* OS_PASSWORD : default "ADMIN_PASS"
* OS_ADMIN_EMAIL : default "admin@example.com"

# Start a keystone instance

Before start a keystone instance, we also need mysql database to store data.
```
mkdir -p /pdata/docker/mysql
docker run --name mysql -p 23306:3306 -v /pdata/docker/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=Letmein123 -h mysql -d njuicsgz/mysql:5.5
```
Then we should link the database, create a new keystone instance as follow:
```
docker run -d \
  --link mysql:mysql\
  -e OS_TENANT_NAME=admin \
  -e OS_USERNAME=admin \
  -e OS_PASSWORD=ADMIN_PASS \
  -p 35357:35357\
  -p 5000:5000 \
  --name keystone -h keystone krystism/openstack-keystone
```
It may takes some time to execute initscript, you just need to do is wait about 5s, you can use docker logs to fetch
some info from the instance, once the work is done, you can check if it really works:
```
docker exec -t -i keystone bash
cd /root
source admin-openrc.sh
keystone user-list
```
You need to set /etc/hosts if you want access keystone from external host domain:
```
# echo '10.2.240.12 keystone' >> /etc/hosts
```
PS: if you have config KEYSTONE_HOST=10.2.240.12 when starting container 'http://${KEYSTONE_HOST}:35357/v2.0', the WIP can not be accessed in the container。

Enjoy!
