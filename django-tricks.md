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



**6. try-django 1.0 lecture 16: model managers:**

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



```
 

