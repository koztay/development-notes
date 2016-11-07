**1- docker-machine genel:**
Eğer docker-machine eval ile env 'yi aktive edersek o zaman sanki remote makine 'de 
çalışır gibi docker komutları verebiliriz.
```
$ eval $(docker-machine env <machine-name>)

```
yukarıdaki şekilde env  aktif edildikten sonra docker ps vb. her türlü docker komutu çalışır.
Ancak clinet versiyon farkı nedeniyle hata verir. Onun için de aşağıdaki komut ile aynı versiyon
kullanımı sağlanır:
```
dvm use 1.10.3
```


**2- docker postgres veritabanı sql yedek alma:**
Aşağıdaki komut coreos 'teki pathe yükler :

```
docker exec -t <docker_container_name> pg_dump -c -U <postgres_user_name> <psotgres_db>  | bzip2 > /home/core/backup.sql.bz2
```

