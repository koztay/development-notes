
[1- docker-machine genel](#1--docker-machine-genel)

[2- docker postgres veritabanı sql yedek alma](#2--docker-postgres-veritabanı-sql-yedek-alma)

[3- docker postgres veritabanı restore etme](#3--docker-postgres-veritabanı-restore-etme)

[4- python manage.py makemigrations ve migrate vb. komutlar için en güvenli yol](4--python-manage.py-makemigrations-ve-migrate-vb.-komutlar-için-en-güvenli-yol)

[5- redis server uyarı mesajları (docker envionment)](5--redis-server-uyarı-mesajları-(docker-envionment))

[6- docker-compose sadece belirli servisleri build etmek](6--docker-compose-sadece-belirli-servisleri-build-etmek)


### 1- docker-machine genel:
Eğer docker-machine eval ile env 'yi aktive edersek o zaman sanki remote makine 'de 
çalışır gibi docker komutları verebiliriz.

```shell
$ eval $(docker-machine env <machine-name>)

```

yukarıdaki şekilde env  aktif edildikten sonra docker ps vb. her türlü docker komutu çalışır.
Ancak clinet versiyon farkı nedeniyle hata verir. Onun için de aşağıdaki komut ile aynı versiyon
kullanımı sağlanır:

```shell
$ dvm use 1.10.3
```

yukarıdaki komutu verebilmek içinse dvm kurmak gerekir. Şu linkten kurulabilir:
https://github.com/getcarina/dvm

### 2- docker postgres veritabanı sql yedek alma:
Aşağıdaki komut coreos 'teki pathe yükler :

```shell
$ docker exec -t <docker_container_name> pg_dump -c -U <postgres_user_name> <postgres_db> | bzip2 > /home/core/backup.sql.bz2
```

yukarıdaki komutun çalıştığını test ettim. Ancak aşağıdaki komutlar da 1 no.lu maddedeki gibi
env aktivasyonu ile çalıştırılabilir, bu durumda lokale dump eder:

```shell
$ docker exec -t <docker_container_name> pg_dumpall -c -U <postgres_user_name> > dump_`date +%d-%m-%Y"_"%H_%M_%S`.sql
```

yukarıda dump_all komutunu kullandık. Bu komut kullanıldığında database adı yazmadan veriyoruz komutu.
Aksi taktirde fazladan komut verildi uyarısı çıkıyor.

### 3- docker postgres veritabanı restore etme:
Aşağıdaki komut env aktifse restore eder, ancak bunu test edemedim henüz:

```shell
$ cat your_dump.sql | docker exec -i <your-db-container> psql -U <postgres_user_name> <postgres_db>
```

yukarıdaki komut mevcutta var olan db 'ye uygulandığında "already exists" vb. bir sürü hata verdi.
Sebebi pg_dumpall komutu ile dump edilen dosyayı yükledik ondan. pg_dump komutu ile aldığımız dump'ı 
sorunsuzca yükleyebiliyoruz. pg_dumpall veritabanı bomboşsa o zaman uygulanabilir belki. Ama tablolar
roller mevcutsa o zaman yükleme yapamıyor. 

Bir de pg_restore komutu kullanılan aşağıdaki yöntem var (https://gist.github.com/jgillman/8191d6f587ffc7207a88e987e034b675) :

```shell
docker exec -i "${DOCKER_CONTAINER_NAME}" pg_restore -C --clean --no-acl --no-owner -U "${DB_USER}" -d "${DB_HOSTNAME}" < "${LOCAL_DUMP_PATH}"
```

yukarıdaki komutu denemedim, kullandığı parametreler göz önüne alındığında belki database 'de
tablolar mevcut iken de restore edebiliyordur. Bunu denemeliyim.
- [ ] Yukarıdaki metodu dene.

### 4- python manage.py makemigrations ve migrate vb. komutlar için en güvenli yol:

Bunun için aşağıdaki komutu ver (docker ps ile container id 'sini bul aşağıya yaz): 

```shell
$ docker exec -it 1e7fcb665360 bash
```

sonrasında aşağıdaki gibi bir terminal ekranı çıkar:

```shell
root@1e7fcb665360:/usr/src/app#
root@1e7fcb665360:/usr/src/app# python manage.py makemigrations ya da collectstaitic ya da migrate vb.

Operations to perform:
  Synchronize unmigrated apps: messages, staticfiles, django_filters, suit, data_importer, crispy_forms, tinymce
  Apply all migrations: sessions, carts, sites, admin, orders, importer, analytics, static_pages, products, taggit, blog, contenttypes, registration, auth, visual_site_elements, newsletter
Synchronizing apps without migrations:
  Creating tables...
    Running deferred SQL...
  Installing custom SQL...
Running migrations:
  No migrations to apply.

```

bu komutları bu şekilde vermemiz yani docker-compose ile vermiyor olmamız bir sürü zombi kontainer 
oluşmasına engel oluyor. 

### 5- redis server uyarı mesajları (docker envionment):

Redis server 'ı kurunca aşağıdaki uyarıları veriyor:

```sh
WARNING overcommit_memory is set to 0! Background save may fail 
under low memory condition. 
To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf 
and then reboot or run the command 'sysctl vm.overcommit_memory=1' for 
this to take effect.
2016-11-22T09:10:38.301413909Z 1:M 22 Nov 09:10:38.301 # WARNING you have
Transparent Huge Pages (THP) support enabled in your kernel. This will create
latency and memory usage issues with Redis. To fix this issue run the command
'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it
to your /etc/rc.local in order to retain the setting after a reboot. 
Redis must be restarted after THP is disabled.
```

yukarıdaki uyarıda verdiği komutları container 'da değil coreos 'ta çalıştırıyoruz. Sonrasında sadece aşağıdaki uyarı kalıyor:

```sh
WARNING: The TCP backlog setting of 511 cannot be enforced because 
/proc/sys/net/core/somaxconn is set to the lower value of 128.
```

ancak bu önemsiz. 128 değeri de bizim için yeterli.

**5- redis server güvenlik:**

Redis server normalde dış dünyaya açık olarak kuruluyor ve son derece güvensiz. Dolayısıyla güvenlikli hale getirilmesi için password vb. ile çalışması sağlanabiliyor. Ayrıca redis.conf 'da bind 0.0.0.0 yerine bind 127.0.0.1 ayarı yapılınca dış dünyaya kapanıyor. Fakat ben bunu henüz test edemedim, ayrıca diğer containerlar erişemeyebilir bu durumda, test etmek lazım. Kendim Dockerfile yazınca redis otomatik başlamadı. redis.conf dosyası oluşturmam lazım önce. Bunu redis sitesinden indirdim.

Şöyle çözüyoruz:

1- redis 'i ayrı bir klasöre alıp customize ederek build etmemiz kazım. Onun için redis isminde bir klasör açıp şu şekilde bir dockerfile oluşturuyoruz:

```sh
#Dockerfile
FROM redis:3.2.5
COPY redis.conf /usr/local/etc/redis/redis.conf
```

2- docker-compose olarak da aşağıdaki şekilde uyguluyoruz:

```sh
#docker-compose.yml
  redis:
    build: ./redis
    restart: always
#    ports: 
#      - "6379"
# ports kısmını yazınca remote port açıyor serverda ve uzaktan erişilebiliyor.
    command: redis-server /usr/local/etc/redis/redis.conf
```

3- son olarak redis.conf dosyasında aşağıdaki değişikliği yapıyoruz:

```sh
#redis.conf
# bind 127.0.0.1
bind 0.0.0.0
```

### 6- docker-compose sadece belirli servisleri build etmek:

Aşağıdaki kodu şu linkte buldum: [https://github.com/docker/compose/issues/1383](https://github.com/docker/compose/issues/1383)

```sh
# recreate only 2 services
$ docker-compose up -d --no-deps SERVICE1 SERVICE2

# stream logs for all of them
$ docker-compose logs
```

burada up parametresini vermiş, acaba build de veriliyor mu denemedim.
Şu işe yarayabilir, development sonrası sitede iyileştirmeler yapmak zorunda kaldığımızda örneğin redisin veya nginx 2in versiyonunu yükseltmek istediğimizde diğer servislere hiç dokunmadan yapabiliriz bu sayede.

 
