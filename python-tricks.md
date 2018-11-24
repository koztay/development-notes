### [100 Awesome Web Development Tools & Resources/](http://www.edumobile.org/web-development/100-awesome-web-development-tools-and-resources/)


### [Jupyter Notebook of Complete Python Bootcamp](http://nbviewer.jupyter.org/github/jmportilla/Complete-Python-Bootcamp/tree/master/)

### [Intermediate Python Tips](http://book.pythontips.com/en/latest/index.html)

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

**<a name='4'></a>4 - pyenv "zlib not available" error on OSX Mojave:**

It gives the following error :
```sh
zipimport.ZipImportError: can't decompress data; zlib not available
```

The solution is to install MacOS SDK Headers:

```sh
sudo installer -pkg /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg -target /
```

**<a name='5'></a>5 - pyenv virtualenv creation activation etc.:**

```sh
pyenv virtualenv <version> <env_name>
pyenv virtualenv 2.7.10 my-virtual-env-2.7.10

```

then you can list your environments as below:

```sh
pyenv virtualenvs
```

activate/deactivate 

```sh
$ pyenv activate <name>
$ pyenv deactivate
```

Installing requirements works as described in venv. Unlike in venv a rm -r command is not needed to remove an environment, an uninstall command exists.

**<a name='6'></a>6 - cookiecutter django postgres on local development:**

First run postgres server:

```sh
postgres -D /usr/local/var/postgres
```

then create db:

```sh
createdb <what you've entered as the project_slug at setup stage>
```




