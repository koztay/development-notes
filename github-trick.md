## github notlarım :

[1. github 'dan (remote repo) daha önce yüklenmiş dosyayı silme :](#1)

[2. github remote repo silme / ekleme:](#2)


**<a name='1'></a>1. github 'dan (remote repo) daha önce yüklenmiş dosyayı silme :**
*git rm --cached file1.txt (deleteS the file from repo but keep the file in filesystem...)*


**<a name='2'></a>2.  github remote repo silme / ekleme:**

repo ekleme :

```
$ git remote add origin https://github.com/koztay/repo_adi.git
```

fatal: remote origin already exists.  hatası verirse remote repoyu silmek gerek.

```
$ git remote -v
# View current remotes
origin  https://github.com/OWNER/REPOSITORY.git (fetch)
origin  https://github.com/OWNER/REPOSITORY.git (push)
destination  https://github.com/FORKER/REPOSITORY.git (fetch)
destination  https://github.com/FORKER/REPOSITORY.git (push)

$ git remote rm destination  # veya aşağıdaki gibi origin 'i de silebiliriz.
$ git remote rm origin
# Remove remote
$ git remote -v
# Verify it's gone
origin  https://github.com/OWNER/REPOSITORY.git (fetch)
origin  https://github.com/OWNER/REPOSITORY.git (push)
```