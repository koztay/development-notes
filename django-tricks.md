## Know-How nitelikli notlar :
**1. Template adlandırma :**
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

**13 - dictionary'den e