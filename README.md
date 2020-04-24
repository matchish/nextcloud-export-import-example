## Run
`docker-compose up -d`
# Export
Set maintenance mode  
`docker-compose exec --user www-data app php occ maintenance:mode --on`
Copy all files  
`docker cp --archive nextcloudexportimportexample_app_1:/var/www/html ./backup/`
Dump db  
`docker-compose exec db mysqldump --single-transaction -h localhost -u root -ppassword nextcloud > nextcloud-sqlbkp.bak`
# Import
????
