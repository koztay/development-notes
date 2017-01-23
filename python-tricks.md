# [100 Awesome Web Development Tools & Resources/](http://www.edumobile.org/web-development/100-awesome-web-development-tools-and-resources/)


# [Jupyter Notebook of Complete Python Bootcamp](http://nbviewer.jupyter.org/github/jmportilla/Complete-Python-Bootcamp/tree/master/)


**1 - dictionary 'den en güvenli key çekme yöntemi :**

```python
some_dict = {}
some_dict['url']  #eğer url isminde key yoksa hata verir 
some_dict.get('url', "http://www.myurl.com")  # en güvenlisi bu
```

tuple 'daki ikinci eleman default değer o varsa key 'i bulamazsa
o değeri döndürür, bulursa key 'i döndürür. Eğer default yoksa
none döndürür.

**2 - python3 virtualenv yaratmak :**

ilk önce python 3 yolunu bulmalıyız, bunun için aşağıdaki komutu veriyoruz:

```shell
$ which python3
aşağıdaki gibi bir path döner:
/usr/local/bin/python3
```

sonra aşağıdaki komut verererk python3 için virtualenv yaratıyoruz:

```shell
mkvirtualenv --python=/usr/bin/python3 nameOfEnvironment
```

ya da yukarıdaki iki komutun birleşimini tek satırda verebiliriz:

```shell
mkvirtualenv --python=`which python3` nameOfEnvironment
```


