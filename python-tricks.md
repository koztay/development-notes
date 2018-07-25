# [100 Awesome Web Development Tools & Resources/](http://www.edumobile.org/web-development/100-awesome-web-development-tools-and-resources/)


# [Jupyter Notebook of Complete Python Bootcamp](http://nbviewer.jupyter.org/github/jmportilla/Complete-Python-Bootcamp/tree/master/)

# [Intermediate Python Tips](http://book.pythontips.com/en/latest/index.html)

[1 - dictionary 'den en güvenli key çekme yöntemi](#1)

[2 - python3 virtualenv yaratmak :](#2)

[3 - Delete all pyc files and __pycache__ folders in a project:](#3)


**<a name='1'></a>1 - dictionary 'den en güvenli key çekme yöntemi :**

```python
some_dict = {}
some_dict['url']  #eğer url isminde key yoksa hata verir 
some_dict.get('url', "http://www.myurl.com")  # en güvenlisi bu
```

tuple 'daki ikinci eleman default değer o varsa key 'i bulamazsa
o değeri döndürür, bulursa key 'i döndürür. Eğer default yoksa
none döndürür.

**<a name='2'></a>2 - python3 virtualenv yaratmak :**

ilk önce python 3 yolunu bulmalıyız, bunun için aşağıdaki komutu veriyoruz:

```sh
$ which python3
aşağıdaki gibi bir path döner:
/usr/local/bin/python3
```

sonra aşağıdaki komut verererk python3 için virtualenv yaratıyoruz:

```sh
mkvirtualenv --python=/usr/bin/python3 nameOfEnvironment
```

ya da yukarıdaki iki komutun birleşimini tek satırda verebiliriz:

```sh
mkvirtualenv --python=`which python3` nameOfEnvironment
```

sistem paketlerini kullanabilmek için aşağıdaki komut ile oluştur:

```sh
virtualenv --system-site-packages venv
```



**<a name='3'></a>3 - Delete all pyc files and __pycache__ folders in a project:**

```sh
pyclean .
```
 