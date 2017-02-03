## Django notlarım :



[21 - href=" " linklerini {% static .... %} files ile find replace yapmak: :](#21)

[22- .env dosyasında DJANGO_ALLOWED_HOSTS parametresi:](#22)


**<tag id='1'/> 1. Template adlandırma :**
*Basically you have three types of templates:*
1. Templates that were meant to be extended,like our base.html template. Name it __base.html
2. Templates that are meant to be included,like our payments/cardform.html template. Name it _cardform.html


**2. github 'dan (remote repo) daha önce yüklenmiş dosyayı silme :**
*git rm --cached file1.txt (deleteS the file from repo but keep the file in filesystem...)*


**3. signals yerine realpython 2 kitabında slug yaratmak için save metodunu override etmiş:**

```python
from django.db import models
from uuslug import uuslug

class Post(models.Model):
    created_at = models.DateTimeField(auto_now_add=True)
    title = models.CharField(max_length=100)
    content = models.TextField()
    tag = models.CharField(max_length=20, blank=True, null=True)
    image = models.ImageField(upload_to="images", blank=True, null=True)
    views = models.IntegerField(default=0)
    slug = models.CharField(max_length=100, unique=True)

    def __unicode__(self):
        return self.title

    def save(self, *args, **kwargs):
        self.slug = uuslug(self.title, instance=self, max_length=100)
        super(Post, self).save(*args, **kwargs)

```    


**4. try-django 1.10 lecture 15: kısaltılmış bir for loop örneği:**
```python
def code_generator(size=SHORTCODE_MIN, chars=string.ascii_lowercase + string.digits):
    # new_code = ''
    # for _ in range(size):
    #     new_code += random.choice(chars)
    # return new_code
    return ''.join(random.choice(chars) for _ in range(size))

```
burada şuna da dikkat function parametresi olarak chars= iki farklı şeyin toplamı yazılabiliyor.


**5. try-django 1.10 lecture 15: iki dosya da birbirinden funtion import ediyor 
bu durumda birinden birine cannot import diyor python ve aşağıdaki şekilde çözüyor:**
```python
def create_shortcode(instance, size=SHORTCODE_MIN):
    new_code = code_generator(size=size)
    Klass = instance.__class__
    qs_exists = Klass.objects.filter(shortcode=new_code).exists()
    if qs_exists:
        return create_shortcode(size=size)
    return new_code
```
Klass = instance.__class__ 
tabii instance.__class__.__name__ de mevcut.
sınıfı import etmeden import etmek gibi birşey dedi Justin. Çakışma olmasın diye Klass ismini verdi.
Bu arada database 'de veri var mı diye kontrol etmek için yukarıdaki gibi exists() metodunu kullan...
excel importer da bunun aynını kullanarak düzelt importerını...



**6. try-django 1.10 lecture 16: model managers:**

```python
class KirrURLManager(models.Manager):
    def all(self, *args, **kwargs):
        qs_main = super(KirrURLManager, self).all(*args, **kwargs)
        qs = qs_main.filter(active=True)
        return qs

    def refresh_shortcodes(self, items=None):
        qs = KirrURL.objects.filter(id__gte=1)
        if items is not None and isinstance(items, int):
            qs = qs.order_by('-id')[:items]
        new_codes = 0
        for q in qs:
            q.shortcode = create_shortcode(q)
            print(q.id)
            q.save()
            new_codes += 1
        return "New codes made: {i}".format(i=new_codes)
```

model manager 'da bildiğin utility function benzeri birşey yapmış. Neden
bunu utils.py içerisine koymadı anlamadım. shortcode 'lar için bulk işlem
yapmak amacıyla koymuş. tek seferde tamamını değiştiren bir metod bu.
Şunun için yazmış : python manage.py refresh_shortcodes gibi bir komut yazabilmek içim
Ayrıca model içerisinde linklerken 
objects = KirrURLManager() veya kendi kafamıza göre isimlendirme de yapabiliyoruz:
my_manager = KirrURLManager() gibi...


**7. try-django 1.10 lecture 17: custom django management commands:**
https://www.udemy.com/try-django-1-10/learn/v4/t/lecture/5922392


**8. try-django 1.10 lecture 18: class based views / function based views:** 

```python

from django.http import HttpResponse
from django.shortcuts import render
from django.views import View

# Create your views here.
def kirr_redirect_view(request, *args, **kwargs): #function based view FBV
    return HttpResponse("hello")


class KirrCBView(View): #class based view
    def get(self, request, *args, **kwargs):
        return HttpResponse("hello again")
```

aslında class based view 'da override edilmiş get metodu, function based view'ın birebir aynısı.
Aradaki temel fark FBV 'de hem post hem get tüm metodlar mevcut, ama CBV 'de her metodu kendimiz
override etmeliyiz.

**9. try-django 1.10 lecture 19: using parameters from settings:**

```python

from django.conf import settings
from django.db import models

# Create your models here.

from .utils import code_generator, create_shortcode

SHORTCODE_MAX = getattr(settings, "SHORTCODE_MAX", 15)

---
---
---

class KirrURL(models.Model):
    url         = models.CharField(max_length=220, )
    shortcode   = models.CharField(max_length=SHORTCODE_MAX, unique=True, blank=True)

```

models.py  içerisine yukarıdaki şekilde kullanım reusable app yazma konusunda yardımcı oluyor.
Çünkü uygulamamızı kullanan diğer kullanıcılar eğer settings içerisine istediğimiz settimg değerini
set etmişse kullan etmemişse de o zaman 15 değerini kullan diyoruz. Bu notasyon yani getattr notasyonu
1.8 'de geçerli olmayabilr ve sadece 1.10 itibarıyla kullanıma sunulmuş olabilir. Diğer kullanım da 
doğru ama eğer reusable olmasını planlamıyorsak. Aksi taktirde hata verir eğer o değeri bulamazsa.


**10. try-django 1.10 lecture 20: URL keyword arguments:**

```python
from django.conf.urls import url
from django.contrib import admin

from shortener.views import kirr_redirect_view, KirrCBView

# DO NOT DO
# from shortener import views
# from another_app.views import views

urlpatterns = [
    url(r'^new-admin/', admin.site.urls),
    url(r'^a/(?P<shortcode>[\w-]+)/$', kirr_redirect_view),
    url(r'^b/(?P<shortcode>[\w-]+)/$', KirrCBView.as_view()), #joincfe.com/projects/ python regex

    # DO NOT DO 
    #url(r'^abc/$', 'shortener.views.kirr_redirect_view' ),
    # url(r'^abc/$', views.kirr_redirect_view ),
]
```

1.10 'da Justin 'in DO NOT DO dediği kısım çalışmıyor. Ayrıca üstte yer alan DO NOT DO 
django dokumantasyonunda böyle yap diyor, ama birden fazla app 'imiz varsa o zaman bu iyi
bir yöntem değil.
justin 'in regex guide 'ı: https://github.com/codingforentrepreneurs/Guides/blob/master/all/common_url_regex.md

**11. try-django 1.10 lecture 21: Querying database:**

```python

def kirr_redirect_view(request, shortcode=None, *args, **kwargs): #function based view FBV
    # en iyi yöntem: 
    obj = get_object_or_404(KirrURL, shortcode=shortcode)
    
    #ikinci iyi yöntem :
    obj_url = None  # return 'de bunu set etmelisin bunu kullanırsan.
    qs = KirrURL.objects.filter(shortcode__iexact=shortcode)
    if qs.exists():
        obj = qs.first()
        obj_url = obj.url
    
    #üçüncü iyi yöntem:
    try:
        obj = KirrURL.objects.get(shortcode=shortcode)
    except:
        obj = KirrURL.objects.all().first
     
    return HttpResponse("hello {sc}".format(sc=obj.url))

```


**12 - try-django lecture 22: HttpResponse Redirect & URL ordering:**

```python
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^about123/$', test_view),
    url(r'^(?P<shortcode>[\w-]+){6,15}/$', kirr_redirect_view),
    url(r'^b/(?P<shortcode>[\w-]+){6,15}/$', KirrCBView.as_view()), #joincfe.com/projects/ python regex

```

urlpatterns 'de regex olarak süslü parantez içerisinde minimum ve maksimum karakter değeri belirtebiliyormuşuz.
Bunu yapmamızdaki amaç eğer min, max arasında değilse urlpattern bulamadığı için sistem kendisi 404 döndürüyor.
Bizim get_or_404 döndürmemizden farklı bu...

**13 - github çalışılan branch 'ı master yapma:**

http://stackoverflow.com/questions/2862590/how-to-replace-master-branch-in-git-entirely-from-another-branch

```shell
You should be able to use the "ours" merge strategy to overwrite master with seotweaks like this:

git checkout seotweaks
git merge -s ours master
git checkout master
git merge seotweaks

```

**14 - pycharm 'da virtualenv ile local development yaparken environment variables 'ı dosyadan okutan plug-in: **

[https://github.com/Ashald/EnvFile](https://github.com/Ashald/EnvFile)

**14 - TemplateView :**

Projemde statik sayfalar için (about vb.) settings 'in olduğu klasörde views.py dosyası oluşturmuştum ve bu sayfalar için "function based view" FBV kullanmaktaydım. Ancak en mantıklısı TemplateView kullanmak. Böylece views.py dosyasını da silebilirim. TemplateView en kolay kullanımı şu şekilde:

```sh
from django.conf.urls import url
from django.views.generic import TemplateView

urlpatterns = [
    url(r'^about/$', TemplateView.as_view(template_name="about.html")),
]

```

Ayrıca herhangi bir view içerisine import edip daha sonra context değiştirerek bu view 'ları da dinamik yapmak mümkün. 


**15 - Flatpages :**

Django içerisinde flatpages varmış. Şimdiye kadar niye kullanmadım ki? Şurada detaylı bilgi var: [https://simpleisbetterthancomplex.com/tutorial/2016/10/04/how-to-use-django-flatpages-app.html](https://simpleisbetterthancomplex.com/tutorial/2016/10/04/how-to-use-django-flatpages-app.html)


**16 - Model Managers :**

Şu eğitime ait not:
[https://github.com/mlavin/video-examples](https://github.com/mlavin/video-examples)

Herhangi bir model yazdıktan sonra o model için model manager yazabiliriz, örneğin:

```python
class ActiveManager(models.Manager):
    """Manager to exclude non-active records."""

    def get_queryset(self):
        return super().get_queryset().filter(is_active=True)


class DomainCheck(models.Model):
    """Configured website check."""

    PROTOCOL_HTTP = 'http'
    PROTOCOL_HTTPS = 'https'

    PROTOCOL_CHOICES = (
        (PROTOCOL_HTTP, 'HTTP'),
        (PROTOCOL_HTTPS, 'HTTPS'),
    )

    METHOD_GET = 'get'
    METHOD_POST = 'post'
    METHOD_CHOICES = (
        (METHOD_GET, 'GET'),
        (METHOD_POST, 'POST'),
    )

    domain = models.CharField(max_length=253)
    path = models.CharField(max_length=1024)
    protocol = models.CharField(
        max_length=5, choices=PROTOCOL_CHOICES, default=PROTOCOL_HTTP)
    method = models.CharField(
        max_length=4, choices=METHOD_CHOICES, default=METHOD_GET)
    is_active = models.BooleanField(default=True)

    objects = models.Manager()
    active = ActiveManager()
     

```

model manager yazınca 
hem objects=  hem de active= şeklinde manager class 'larını ayrı ayrı belirtmemiz gerek.

Bir de burada chocices alanlarını model içerisinde tutmak daha akıllıca bir yöntem. Daha sonra bu manager ile sorgulama yaparken aşağıdaki gibi yapıyoruz:

```python
active_domains = Domaincheck.active.all()

```

**17 - Custom Querysets :**

Şu eğitime ait not:
[https://github.com/mlavin/video-examples](https://github.com/mlavin/video-examples)

Custom queryset yapmanın avantajı query 'lerin birbirine chain edilebilmeleri. Yani .active.stale.all() şeklinde bir query oluşturmak mümkün. 


```python
import datetime

from django.db import models
from django.db.models import Max, Q
from django.utils.timezone import now


class DomainCheckQuerySet(models.QuerySet):
    """Custom queryset to filter and annotate domain checks."""

    def active(self):
        return self.filter(is_active=True)

    def stale(self, cutoff=datetime.timedelta(hours=1)):
        end_time = now() - cutoff
        return self.annotate(
            last_check=Max('checkresult__checked_on')
        ).filter(
            Q(last_check__lt=end_time) | Q(last_check__isnull=True))


class DomainCheck(models.Model):
    """Configured website check."""

    PROTOCOL_HTTP = 'http'
    PROTOCOL_HTTPS = 'https'

    PROTOCOL_CHOICES = (
        (PROTOCOL_HTTP, 'HTTP'),
        (PROTOCOL_HTTPS, 'HTTPS'),
    )

    METHOD_GET = 'get'
    METHOD_POST = 'post'
    METHOD_CHOICES = (
        (METHOD_GET, 'GET'),
        (METHOD_POST, 'POST'),
    )

    domain = models.CharField(max_length=253)
    path = models.CharField(max_length=1024)
    protocol = models.CharField(
        max_length=5, choices=PROTOCOL_CHOICES, default=PROTOCOL_HTTP)
    method = models.CharField(
        max_length=4, choices=METHOD_CHOICES, default=METHOD_GET)
    is_active = models.BooleanField(default=True)

    objects = DomainCheckQuerySet.as_manager()

    def __str__(self):
        return '{method} {url}'.format(
            method=self.get_method_display(), url=self.url)

    @property
    def url(self):
        return '{protocol}://{domain}{path}'.format(
            protocol=self.protocol, domain=self.domain, path=self.path)


class CheckResult(models.Model):
    """Result of a status check on a website."""

    domain_check = models.ForeignKey(DomainCheck)
    checked_on = models.DateTimeField()
    status_code = models.PositiveIntegerField(null=True)
    response_time = models.FloatField(null=True)
    response_body = models.TextField(default='')

```

Ancak burada query oluştururken objects eklememiz gerek artık, all() koymuyoruz ona dikkat!!!. Aşağıdaki örneklerde mevcut. Eğer birden fazla query 'miz varsa otomatik olarak custom queryset yaratmak en mantıklısı. 

```python
active_domains = Domaincheck.objects.active()
stale_domains = Domaincheck.objects.stale()
active_and_stale_domains = Domaincheck.objects.active().stale()
# ya da Domaincheck.objects.stale().active şeklinde de yazabiliriz. 
# Sıralama önemli değil.

```

**18 - Aggregation / Annotation :**

Şu eğitime ait not:
[https://github.com/mlavin/video-examples](https://github.com/mlavin/video-examples)

Aggregation : Herhangi bir query sonucu dönen değerlerdeki bir alan için toplam, ortlama ya da vb. bir hesaplama sonucu elde edilen bir değerdir. Aggregation sonunda tek bir değer elde edilir.
 
Annotation : Veritabanındaki her bir satıra ilave bir hesaplanmış kolon eklemek gibi düşünülebilir. Annotationda ise her satır için bir değer hesaplandığından tek bir değer elde edilmez. Aradaki temel fark budur. Annotation sonucu eklenmiş kolonlar için de order_by metodu kullanılabilir.


```python
def stale(self, cutoff=datetime.timedelta(hours=1)):
        end_time = now() - cutoff
        return self.annotate(
            last_check=Max('checkresult__checked_on')
        ).filter(
            Q(last_check__lt=end_time) | Q(last_check__isnull=True))

def status(self, cutoff=datetime.timedelta(hours=1)):
    start_time = now() - cutoff
    ping = Q(checkresult__checked_on__gte=start_time)
    success = Q(
        checkresult__checked_on__gte=start_time,
        checkresult__status_code__range=(200, 299))
    return self.annotate(
        last_check=Max('checkresult__checked_on'),
        successes=Count(Case(When(success, then=1))),
        pings=Count(Case(When(ping, then=1))),
    ).annotate(
        success_rate=F('successes') * 100.0 / F('pings')
    ).annotate(
        status=Case(
            When(success_rate__gt=90, then=Value('good')),
            When(success_rate__range=(75, 90), then=Value('fair')),
            When(success_rate__lt=75, then=Value('poor')),
            When(success_rate__isnull=True, then=Value('unknown')),
            output_field=models.CharField())
    )


```


Yukarıdaki custom query 'de annotation kullanılıyor. İlgili CheckResult modelindeki 'checked\_on' tablosuna bak ve maximum yani en son değeri al diyor. Yani aslında yaptığı tam olarak şu: query sonucu DÖNEN TÜM SATIRLARA 'last\_check' değeri ekliyor, sonra da tüm sonuçlar içinde 'last\_check' değeri 'end\_time' olarak girilen değerden küçük olanları ya da hiç last_check değeri olmayanları filtreliyor. 
Bir sonraki status annotation 'ında ise annotation 'ların da chain olarak arka arkaya eklenebildiğini görüyoruz. Döndürdüğü annotate 'lere bakacak olursak satıra 5 adet kolon eklediğini görüyoruz. Eklediği kolonlar aşağıdaki gibi:

* last_check
* successes
* pings
* success_rate
* status

Yukarıdaki kolonlardan ilk üç tanesini bir annotate, ve kalanları da tekil olarak annotate fonksiyonları içerisinde gruplamış olmasının özel bir nedeni mi var anlamadım. Yani kalan ikisini de ilk üçünün içerisine alıp tek bir annotate fonksiyonu içerisine yazabilir miyidik? Sanırım yazardık. Gruplamasının sebebi bence oknurluğu arttırmak.

Yukarıda annotate örneği verdik. Aggregate örneği verecek olursak :

```python
# Average price across all books.
>>> from django.db.models import Avg
>>> Book.objects.all().aggregate(Avg('price'))
{'price__avg': 34.35}

# Max price across all books.
>>> from django.db.models import Max
>>> Book.objects.all().aggregate(Max('price'))
{'price__max': Decimal('81.20')}

```

Özetle aggregate ve annotate kullanarak "raw sql" ile sorulayabileceğimiz birçok durumda kullanmadan veritabanından istediğimiz sonuçları döndürebiliriz.

**19 - Admin Panel : "Customizing Admin Listing"**

Şu eğitime ait not:
[https://github.com/mlavin/video-examples](https://github.com/mlavin/video-examples)

```python
from django.contrib import admin
from django.utils.timesince import timesince

from . import models

@admin.register(models.DomainCheck)
class DomainCheckAdmin(admin.ModelAdmin):

    list_display = (
        'domain', 'path', 'protocol', 'method', 'is_active',
        'status', 'last_checked', )
    list_filter = ('protocol', 'method', 'is_active', )
    search_fields = ('domain', )

    def get_queryset(self, request):
        return super().get_queryset(request).status()

    def status(self, obj):
        return obj.status.title()
    status.admin_order_field = 'success_rate'

    def last_checked(self, obj):
        if obj.last_check:
            return '{} ago'.format(timesince(obj.last_check))
        else:
            return 'Never'
    last_checked.admin_order_field = 'last_check'


@admin.register(models.CheckResult)
class CheckResultAdmin(admin.ModelAdmin):

    date_hierarchy = 'checked_on'
    list_display = ('domain_check', 'status_code', )
    list_filter = ('checked_on', )
```

Dikkat edilmesi gerekli noktalar:

* @admin.register dekoratörü kullanımı.
* list_display ile hangi kolonların gösterileceğini belirliyoruz.
* search_fields alanındaki tuple 'a birden fazla değer yazabiliyoruz, bu durumda "or" kullanarak search yapıyor. Bonus :

```python
search_fields = ('^domain', )  # başına şapka koyarsak "starts_with" ile arar. 
search_fields = ('=domain', )  # başına eşittir koyarsak "exact_match" ile arar. 
```

* get_queryset, status vb. metodlar ile de hangi satırların gösterileceğini belirliyoruz.
* status ve last_checked metodlarının parametrelerine dikkat : (self, obj)
* status.admin\_order\_field = 'success_rate'  ve last\_checked.admin\_order\_field = 'last_check' değerleri admin panelde bu alanlara göre sort işlemi yapabilmemizi sağlıyor. 
* CheckresultAdmin 'deki list_filter = ('checked\_on') django nun yıl, ay ve gün gibi otomatik filtreler eklemesini sağlıyor.
* list_filter her zaman sağda gördüğümüz filtreleri oluşturur.

**20 - django-cookiecutter docker development "This site can’t be reached 192.168.99.103 refused to connect." hatası :**

Bu hata şundan kaynaklanıyor: 

* ***192.168.99.103*** yerine ***192.168.99.103:8000*** yazmalısın. Lokal development 'ta cookiecutter için port yazmak gerekiyor.
* ALLOWED_HOSTS = (192.168.199.103, ) değerini local.py dosyasına eklemelisin. 

**<a name="21"></a>21 - href=" " linklerini {% static .... %} files ile find replace yapmak:**

```
Find what:
  (href|src)="([a-zA-Z0-9/.-]+[^.html])"

Replace with:
  $1=\"{% static '$2' %}"  
```

Yukarıdaki kod PyCharm 'da çalıştı. Yalnız şuna dikkat et: yukarıdaki kod href="...." (çift tırnak ile yazılmışsa çalışır. Tek tırnak ile yazılmışsa ona göre düzeltmek gerekir. 

**<tag id='22'/></a>22- .env dosyasında DJANGO_ALLOWED_HOSTS parametresi:**

Bu parametreyi belirtirken eğer domain adreslerini tırnak içerisinde yazarsan hata veriyor. Python gibi düşünüp tırnak içinde yazma.

```sh
DJANGO_ALLOWED_HOSTS=[abc.com,192.168.99.101,212.144.66.44]
```
yukarıdaki örnekte olduğu gibi yazınca oluyor. Aralarına boşluk da koymamak lazım...


