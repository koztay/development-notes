**1- docker postgres veritabanı sql yedek alma:**
```
docker exec -t <docker_container_name> pg_dump -c -U <postgres_user_name> <psotgres_db>  | bzip2 > /home/core/backup.sql.bz2
```
