## Know-How nitelikli notlar :
**Naming Convention :**
*Basically you have three types of templates:*
1. Templates that were meant to be extended,like our base.html template. Name it __base.html
2. Templates that are meant to be included,like our payments/cardform.html template. Name it _cardform.html

**github 'dan (remote repo) daha önce yüklenmiş dosyayı silme :**
*git rm --cached file1.txt (deleteS the file from repo but keep the file in filesystem...)*

**signals yerine realpython 2 kitabında slug yaratmak için save metodunu override etmiş:**

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

