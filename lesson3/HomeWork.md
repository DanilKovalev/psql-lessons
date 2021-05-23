После переноса данных psql не видит их в своей директории  
`pg_ctl: directory "/var/lib/postgresql/12/main" is not a database cluster directory`

Идем в конфиг файл в моем случае (/etc/postgresql/12/main/postgresql.conf), и меняем параметр  
`data_directory = '/mnt/data'`  
После этого psql все еще не будет нравиться что пользователи не из группы postgres имеют права на директорию /mnt/data, так что отнимаем у них такую возможность  
`chmod -R o-rwx /mnt/data`

Все теперь инстанс работает
