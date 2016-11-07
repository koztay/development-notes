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

yukarıdaki komutu verebilmek içinse dvm kurmak gerekir. Şu linkten kurulabilir:
https://github.com/getcarina/dvm

**2- docker postgres veritabanı sql yedek alma:**
Aşağıdaki komut coreos 'teki pathe yükler :

```
docker exec -t <docker_container_name> pg_dump -c -U <postgres_user_name> <postgres_db> | bzip2 > /home/core/backup.sql.bz2
```

yukarıdaki komutun çalıştığını test ettim. Ancak aşağıdaki komutlar da 1 no.lu maddedeki gibi
env aktivasyonu ile çalıştırılabilir, bu durumda lokale dump eder:

```
docker exec -t <docker_container_name> pg_dumpall -c -U <postgres_user_name> > dump_`date +%d-%m-%Y"_"%H_%M_%S`.sql
```

yukarıda dump_all komutunu kullandık. Bu komut kullanıldığında database adı yazmadan veriyoruz komutu.
Aksi taktirde fazladan komut verildi uyarısı çıkıyor.

**3- docker postgres veritabanı restore etme:**
Aşağıdaki komut env aktifse restore eder, ancak bunu test edemedim henüz:

```
cat your_dump.sql | docker exec -i your-db-container psql -U <postgres_user_name> <postgres_db>
```

yukarıdaki komut mevcutta var olan db 'ye uygulandığında "already exists" vb. bir sürü hata verdi.
Sebebi pg_dumpall komutu ile dump edilen dosyayı yükledik ondan. pg_dump komutu ile aldığımız dump'ı 
sorunsuzca yükleyebiliyoruz. pg_dumpall veritabanı bomboşsa o zaman uygulanabilir belki. Ama tablolar
roller mevcutsa o zaman yükleme yapamıyor. 

**4- python manage.py makemigrations ve migrate vb. komutlar için en güvenli yol:**
Bunun için aşağıdaki komutu ver (docker ps ile container id 'sini bul aşağıya yaz): 

```
docker exec -it 1e7fcb665360 bash
```

sonrasında aşağıaki gibi bir terminal ekranı çıkar:
```
root@1e7fcb665360:/usr/src/app#
root@1e7fcb665360:/usr/src/app# python manage.py makemigrations ya da migrate 

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
