 


[1- docker-machine genel](#1)

[2- docker postgres veritabanı sql yedek alma](#2)

[3- docker postgres veritabanı restore etme](#3)

[4- python manage.py makemigrations ve migrate vb. komutlar için en güvenli yol](#4)

[5- redis server uyarı mesajları (docker environment)](#5)

[6- redis server güvenlik](#6)

[7- docker-compose sadece belirli servisleri build etmek](#7)

[8- copy files from remote container : (cookiecutter postgres backups)](#8)

[9- digital-ocean vestacp :](#9)

[10- docker-compose genel :](#10)

[11- SSL :](#11)

**<a name='1'></a>1- docker-machine genel:**

Eğer docker-machine eval ile env 'yi aktive edersek o zaman sanki remote makine 'de
çalışır gibi docker komutları verebiliriz.

```shell
$ eval $(docker-machine env <machine-name>)

```

yukarıdaki şekilde env  aktif edildikten sonra docker ps vb. her türlü docker komutu çalışır.
Ancak client versiyon farkı nedeniyle hata verir. Onun için de aşağıdaki komut ile aynı versiyon
kullanımı sağlanır:

```shell
$ dvm use 1.10.3
```

yukarıdaki komutu verebilmek içinse dvm kurmak gerekir. Şu linkten kurulabilir:
https://github.com/getcarina/dvm

**<a name='2'></a> 2- docker postgres veritabanı sql yedek alma:**

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

**<a name='3'></a> 3- docker postgres veritabanı restore etme:**

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

**<a name='4'></a> 4- python manage.py makemigrations ve migrate vb. komutlar için en güvenli yol:**

Bunun için aşağıdaki komutu ver (docker ps ile container id 'sini bul aşağıya yaz):

```shell
$ docker exec -it 1e7fcb665360 bash
```

sonrasında aşağıdaki gibi bir terminal ekranı çıkar:

```shell
root@1e7fcb665360:/usr/src/app#
root@1e7fcb665360:/usr/src/app# python manage.py makemigrations ya da collectstatic ya da migrate vb.

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

bu komutları bu şekilde vermemiz yani docker-compose ile vermiyor olmamız bir sürü zombi container
oluşmasına engel oluyor.

**<a name='5'></a> 5- redis server uyarı mesajları (docker environment):**

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

**<a name='6'></a> 6- redis server güvenlik:**

Redis server normalde dış dünyaya açık olarak kuruluyor ve son derece güvensiz. Dolayısıyla güvenlikli hale getirilmesi için password vb. ile çalışması sağlanabiliyor. Ayrıca redis.conf 'da bind 0.0.0.0 yerine bind 127.0.0.1 ayarı yapılınca dış dünyaya kapanıyor. Fakat ben bunu henüz test edemedim, ayrıca diğer containerlar erişemeyebilir bu durumda, test etmek lazım. Kendim Dockerfile yazınca redis otomatik başlamadı. redis.conf dosyası oluşturmam lazım önce. Bunu redis sitesinden indirdim.

Şöyle çözüyoruz:

1- redis 'i ayrı bir klasöre alıp customize ederek build etmemiz lazım. Onun için redis isminde bir klasör açıp şu şekilde bir dockerfile oluşturuyoruz:

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

**<a name='7'></a> 7- docker-compose sadece belirli servisleri build etmek:**

Aşağıdaki kodu şu linkte buldum: [https://github.com/docker/compose/issues/1383](https://github.com/docker/compose/issues/1383)

```sh
# recreate only 2 services
$ docker-compose up -d --no-deps SERVICE1 SERVICE2

# stream logs for all of them
$ docker-compose logs
```

burada up parametresini vermiş, acaba build de veriliyor mu denemedim.
Şu işe yarayabilir, development sonrası sitede iyileştirmeler yapmak zorunda kaldığımızda örneğin redisin veya nginx 'in versiyonunu yükseltmek istediğimizde diğer servislere hiç dokunmadan yapabiliriz bu sayede.


**<a name='8'></a> 8- copy files from remote container : (cookiecutter postgres backups)**

```sh
docker cp <containerId>:/backups /host/path/target
```

örnek:


```sh
docker cp ae9:/backups postgres_backups
```

ae9 ile başlayan remote container 'daki 'backups' klasörünü bu komutu çalıştırdığım klasörün altındaki 'postgres_backups' klasörüne komple kopyalar...

Özetle remote 'tan lokale ve lokalden remote 'a aşağıdaki şekilde kopyalıyoruz:

```sh
docker cp foo.txt mycontainer:/foo.txt  # lokalden remote'a
docker cp mycontainer:/foo.txt foo.txt  # remote 'dan lokale.
```

Tabii bu komutları verebilmek için eval

```sh
eval $(docker-machine env <machine-name>)
```
komutu ile çalışacağımız makinayı önceden seçmiş olmalıyız. Ancak eğer docker-machine silinmişse yani "docker-machine ls" ile göremiyorsak o zaman ssh ile login olmak zorundayız. Bu durumda adımlar aşağıdaki gibi :

1. Önce ssh ile VPS'e login ol. 
2. docker ps ile çalışan servisler bak. postgres servisini bul. Örneğin '54adxxxx..." olsun. 
3. sonra postgres backup komutunu çalıştır (cookiecutter ise)
 
```sh
$ docker -exec 54ad backup
```
4. yukarıdaki komut container volume içinde backup yarattı. Örneğin yarattığı dosya 'backup_2018_07_24T13_49_23.sql.gz' olsun. Şimdi bu dosyayı container 'dan lokal (ssh ile bağlandığımız makinenin lokali) sisteme alacağız. Bunun için komut:

```sh
docker cp 5eb4:/backups/backup_2018_07_24T13_49_23.sql.gz .
```

5. Şimdi dosya VPS 'in /home/user_name/ klasöründe. Buradan lokale şöyle almak içinse önce logout oluyoruz.
6. Sonra lokal PC 'mizde aşağıdaki komutu veriyoruz:

```sh
scp <username>@<IP_ADDRESS>:<PATH> .
# örnek :
scp root@165.152.129.79:backup_2018_07_24T13_49_23.sql.gz .

```

İşlem tamam!!!


**<a name='9'></a> 9- digital-ocean vestacp :**

Kurulum bittikten sonra packages bölümüne gel DNS template 'i child-ns olarak değiştir. Bundan sonra domain ekleddiğimizde child-ns otomatik seçilir. Ancak eklenen domainde SOA record kısmı düzgün olmuyor. Orayı editleyip ns1.domainadi.com olarak düzeltmeliyiz. İşlemler bittikten sonra server 'ı reboot etmeden değişiklikler yansımıyor. **O nedenle server 'ı mutlaka reboot et.**

Ayrıca VestaCP 'de mutlaka custom install script ile kurulum yap ve mail kısımıda sadece 'exim + dovecot' seçili olsun. Aksi taktirde clamav veya spamassassin artık hangisi bilmiyorum, çoğu yerden gelen mailleri blokluyor.

exim white_list :

```sh
touch /etc/exim_whitelist_senders
cp -p /etc/exim/exim.conf /etc/exim/exim.conf.BKP
vi /etc/exim/exim.conf

# Add the line on top of the file
addresslist whitelist_senders = wildlsearch;/etc/exim_whitelist_senders

#Now search for the line
require verify = sender/callout 

#Comment that line and add the following below that.
!verify = sender/callout=30s,defer_ok,maxwait=60s
!senders = +whitelist_senders

#Save conf file.

#Add email address to the /etc/exim_whitelist_senders file one by one. Wildcard is also acceptable here, Eg: 
*@domain.com
```


**<a name='10'></a> 10- docker-compose genel :**

* docker-compose.yml dosyasının bulunduğu klasörün dışına yazma imkanı yok. Dolayısıyla bu klasörün dışına yazmak için bu dosyayı bir üst klasöre taşıyıp diğer path 'leri de buna göre dosya içerisinde modifiye etmek gerekir.
* Dockerfile dosayaları içerisinde daima docker-compose.yml dosyasının bulunduğu yer referans alınır. Yani COPY . /app direktifi alt klasör olarak nerede hangi klasörde verilirse verilsin . demek docker-compose.yml dosyasının bulunduğu klasör demektir.
* nginx.conf dosyasında volume 'ler için alias tanımlarken mutlaka sonuna / koymak gerekir.
* Volume yaratırken eğer o volume klasörü bizim klasörümüzde mevcut değilse kesinlikle django klasöre yazamıyor. O klasörün owner 'ı root root olarak gözüküyor. Fakat bizim proje klasörümü içerisinde klasör varsa o zaman sorun yok. Bu yüzden sürekli permission hatası alıyordum collectsatic komutu verince. 

**<a name='11'></a> 11- SSL :**

* Önce nginx klasörünün içerisine gidiyoruz:

cookiecutter 'da ```cd $PROJECT_PATH/compose/nginx```sonra aşağıdaki komutu veriyoruz (sertifika request CSR yaratıyoruz):
```openssl req -new -newkey rsa:2048 -nodes -keyout yourkeyname.key -out yourkeyname.csr```

