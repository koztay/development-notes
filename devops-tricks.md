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
cat your_dump.sql | docker exec -i your-db-container psql -U postgres
```

yukarıdaki komut taki postgres username mi, dbname mi belli değil. -U parametresi ile verildiğine göre
username olmalı. Ancak database ismi ile birlikte vermek gerekebilir komutu.