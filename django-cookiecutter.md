## django-cookiecutter

[1. docker development "This site can’t be reached 192.168.99.103 refused to connect." hatası :](#1)

[2 .env dosyasında DJANGO_ALLOWED_HOSTS parametresi:](#2)

[3. Add 192.168.99.100 to ALLOWED_HOSTS hatası :](#3)

[4. .env dosyasında DATABASE_URL ayarı:](#4)

[5. cookiecutter proje ayarları:](#5)


**<a name='1'></a>1. django-cookiecutter docker development "This site can’t be reached 192.168.99.103 refused to connect." hatası :**

Bu hata şundan kaynaklanıyor:

* ***192.168.99.103*** yerine ***192.168.99.103:8000*** yazmalısın. Lokal development 'ta cookiecutter için port yazmak gerekiyor.

**<a name='2'></a>2. .env dosyasında DJANGO_ALLOWED_HOSTS parametresi:**

Bu parametreyi belirtirken eğer domain adreslerini tırnak içerisinde yazarsan hata veriyor. Python gibi düşünüp tırnak içinde yazma.

```sh
DJANGO_ALLOWED_HOSTS=[abc.com,192.168.99.101,212.144.66.44]
```
yukarıdaki örnekte olduğu gibi yazınca oluyor. Aralarına boşluk da koymamak lazım...

Dikkat!!! => cookiecutter-django kullanıldığında yukarıdaki de çalışmıyor, çünkü django-environ kullanıyor. Köşeli parantezsiz aşağıdaki şekilde yazmak lazım bu durumda:

```sh
DJANGO_ALLOWED_HOSTS=abc.com,192.168.99.101,212.144.66.44
```

**<a name='3'></a>3. django-cookiecutter Add 192.168.99.100 to ALLOWED_HOSTS hatası :**

cookiecutter 'da dev.yml seçilince bu hata orataya çıkıyor ve lokal development 'da .env kullanımı yok. Dolayısıyla .env 'ye girilen değerler hiçbir şekilde kaale alınmıyor. Bu yüzden ne yazarsak yazalım ALLOWED_HOSTS hatası oluşuyor. Bunun çözümü dev.yml dosyasına bu parametreyi eklemek. Aşağıdaki şekilde :

```yml

django:
    build:
      context: .
      dockerfile: ./compose/django/Dockerfile-dev
    command: /start-dev.sh
    depends_on:
      - postgres
    environment:
      - POSTGRES_USER=consultailetisim
      - USE_DOCKER=yes
      - DJANGO_ALLOWED_HOSTS=192.168.99.100
    volumes:
      - .:/app
    ports:
      - "8000:8000"
    links:
      - postgres

```

Tabii yukarıdaki değeri girmek yetmiyor bir de local.py 'a bu değeri okumasını da söylemek lazım. Cookie-cutter 'ın bunu düzeltmesi lazım. Yeni django versiyonlarında DEBUG=True olsa bile ALLOWED_HOSTS değerini de illa gireceksin diyor. Yani özetle local.py 'a aşağıdaki değeri giriyoruz:

```py
ALLOWED_HOSTS = env.list('DJANGO_ALLOWED_HOSTS', default=['yourdomain.com', ])
```
bu şekilde ekleyince sorun çözülüyor. Ancak anlamadığım dev.yml kullanırken settings 'de 'local.py' kullanması gerektiğini nasıl biliyor?


**<a name='4'></a>4. .env dosyasında DATABASE_URL ayarı:**

Bu ayar bana kafayı yedirtti. .env dosyasına şu şekilde giriyoruz ayarı:

```python
# PostgreSQL
POSTGRES_PASSWORD=yourpostgrespassword
POSTGRES_USER=karnascookiecutter
# DATABASE_URL="postgres://<pg_user_name>:<pg_user_password>@127.0.0.1:<pg_port>/<pg_database_name>"

DATABASE_URL=postgres://karnascookiecutter:rA55sNYA3tTS@127.0.0.1:5432//karnascookiecutter

```

yukarıdaki password ve user ayarı ile docker user_name ile aynı isimde veritabanı yaratıyor. O nedenle veritabanı adını da user_name ile aynı giriyoruz. Burada farklı bir veritabanı adı girdiğimde çalışmadı. 

Fakat burada en can alıcı nokta makemigrations ve migrate komutlarının nasıl verildiği. ssh ile bağlanıp "docker exec -it <container_name> bash" ile container içerisinden makemigrations ve migrate komutlarını çalıştıramadım bir türlü ancak docker-compose ile verdiğimde çalıştı.
ssh ile bağlanıp makemigrations komutu aşağıdaki hatayı veriyor:

```sh
Traceback (most recent call last):
  File "/usr/local/lib/python3.5/site-packages/django/db/backends/base/base.py", line 199, in ensure_connection
    self.connect()
  File "/usr/local/lib/python3.5/site-packages/django/db/backends/base/base.py", line 171, in connect
    self.connection = self.get_new_connection(conn_params)
  File "/usr/local/lib/python3.5/site-packages/django/db/backends/postgresql/base.py", line 176, in get_new_connection
    connection = Database.connect(**conn_params)
  File "/usr/local/lib/python3.5/site-packages/psycopg2/__init__.py", line 130, in connect
    conn = _connect(dsn, connection_factory=connection_factory, **kwargs)
psycopg2.OperationalError: could not connect to server: No such file or directory
	Is the server running locally and accepting
	connections on Unix domain socket "/var/run/postgresql/.s.PGSQL.5432"?

```

Şu şekilde:

```sh
docker-compose run django python manage.py makemigrations
docker-compose run django python manage.py migrate
```

Yukarıdaki komutlar ile alınan çıktı aşağıdaki gibi:

```sh
Postgres is up - continuing...
Operations to perform:
  Apply all migrations: account, admin, auth, contenttypes, sessions, sites, socialaccount, users
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0001_initial... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying users.0001_initial... OK
  Applying account.0001_initial... OK
  Applying account.0002_email_max_length... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying sessions.0001_initial... OK
  Applying sites.0001_initial... OK
  Applying sites.0002_alter_domain_unique... OK
  Applying sites.0003_set_site_domain_and_name... OK
  Applying socialaccount.0001_initial... OK
  Applying socialaccount.0002_token_max_lengths... OK
  Applying socialaccount.0003_extra_data_default_dict... OK

```

cookiecutter 'da hiçbir management komutu ssh ile çalışmıyor. Tamamını compose ile vermek lazım. Yani:

```sh
docker-compose run django python manage.py createsuperuser
docker-compose run django python manage.py collectstatic
```

gibi...

##DİKKAT ! :
**postgres  şifresinde  normal olmayan karakter kullanma' yani '!(*]' vb gibi karakterler kullanma.**

**<a name='5'></a>5. cookiecutter proje ayarları:**

Sen ne yaparsan yap default olarak production.py dosyasında AMAZON S3 kullanacak şekilde set ediyor kendini. Dolayısıyla bunu engellemek için proje ayarlarında mutlaka WHITENOISE seçili olması gerekiyor. Bunu seçmezsen ondan sonra saçı başı yolduruyor.


**<a name='6'></a>6. compressed files:**

Template değişikliği yaptığımızda compress enable ise salakça bir şekilde eski template'e bakıp staticfiles 'ı bulamadım diyor. O nedenle default olarak cookiecutter ile gelen templates ve static klasörünü rename et.

**<a name='7'></a>7. SSL kullanmama nedeniyle oluşan Forbidden (403) CSRF verification failed hatası:**

Bunu nasıl çözeceğiz bakalım?


**<a name='8'></a>8. AWS S3 / AWS CLI :**

1. Aşağıdaki linkten User grup yarat: [https://console.aws.amazon.com/iam/home?region=us-west-2#/groups]()
2. Sonra bu gruba AdministratorAccess permissionı ver.
3. Sonra aşağıdaki linki kullanarak bir IAM User yarat (Yaratırken 'Programatic access' seçeneğini seç): [https://console.aws.amazon.com/iam/home?region=us-west-2#/users]()
4. User yaratılınca bu user'a ait key dosyasını indir.
5. Bu user'ı ilk maddedeki gruba al. Artık bu ayarlar ile yarattığımız user oluşturulan key'leri ile her türlü erişimi yapabilir. Yani bucket yaratabilir, silebilir vs.
6. Şimdi aşağıdaki komutu komut satırından vererek AWS CLI kur:

```sh
$ brew install awscli

# check if it is installed 
$ aws --version
AWS CLI 1.11.44 (Python 3.4.3)

```
7. sonra erişim için yarattığımız user 'ın key bilgilerini set :

```sh
$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-west-2
Default output format [None]: ENTER
```
yukarıda Default output format haricindeki hiçbir şeyi boş bırakma.

8. Şimdi EU bölgesi için signature v4 kullanım ayarını yapmalıyız. Bunu yapmazsan eğer  x-amz-content-sha256 hatası verir. Çünkü EUROPE bölgesi v4 signature kullanıyor. Bunun için aşağıdaki ayarları yap :

```sh
vim ~/.aws/config  # dosyayı vim ile aç

# aşağıdaki satırı ekle save et ve çık.
signature_version = s3v4
```

şimdi bir bucket silmek için aşağıdaki komutu verebilirsin:

```sh
aws s3 rb s3://istebu-backup --force --region=eu-central-1

# region==eu-central-1 yazmazsan Frankfurt bölgesi bucketleri silemiyor.
# Ayrıca region doğru girilmezse x-amz-content-sha256 hatası vermeye devam ediyor.
```





