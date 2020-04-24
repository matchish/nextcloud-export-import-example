## Run
`git clone git@github.com:matchish/nextcloud-export-import-example.git`  
`cd nextcloud-export-import-example`  
`docker-compose up -d`  
# Export
Set maintenance mode  
`docker-compose exec --user www-data app php occ maintenance:mode --on`  
Copy all files  
`docker cp --archive $(docker-compose ps -q app):/var/www/html ./backup/`  
Dump db  
`docker-compose exec db mysqldump --single-transaction -h localhost -u root -ppassword nextcloud > nextcloud-sqlbkp.bak`  
# Import
Clean up files  
`docker-compose exec app rm -rf /var/www/html/`  
Drop and recreate db  
`docker-compose exec db mysql -h localhost -u root -ppassword -e "DROP DATABASE nextcloud"`  
`docker-compose exec db mysql -h localhost -u root -ppassword -e "CREATE DATABASE nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci"`  
Import db  
`docker exec -i $(docker-compose ps -q db) mysql -h localhost -u root -ppassword nextcloud < nextcloud-sqlbkp.bak`  
Restore files  
`docker cp --archive ./backup/. $(docker-compose ps -q app):/var/www/html/`  
Fix owner  
`docker-compose exec app chown -R www-data:www-data /var/www/html`  
Disable maintenance mode  
`docker-compose exec --user www-data app php occ maintenance:mode --off`  
Check restored instance  
`docker-compose exec --user www-data app php occ maintenance:data-fingerprint` 
