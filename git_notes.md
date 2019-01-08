
### Safest Way of merge : merge brach to master
```sh
$ git checkout master
$ git pull
$ git checkout test
$ git pull
$ git rebase -i master
$ git checkout master
$ git merge test

# after that we can delete local branch
$ git branch -d test

# and also delete remote 
$ git push origin :test


```

### Çoklu PC 'den Github:
github 'a başka bir cihazdan erişince ssh eklemek yeterli olmuyor. Eğer o repoyu clonlarken https ile klonlamışsak o zaman https ile bağlanmaya çalışıyor ve bu duurmda da sürekli kullanıcı adı ve şifre soruyor. Bunu engellemek için aşağıdaki komut ile ssh 'e switch etmek gerek:

```
git remote set-url origin git@github.com:username/repo.git
```

### github 'dan (remote repo) daha önce yüklenmiş dosyayı silme :**
*git rm --cached file1.txt (deleteS the file from repo but keep the file in filesystem...)*


### github remote repo silme / ekleme:**

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

### https ten ssh 'e çevirme:

```sh
git remote -v  # https mi ssh mi bakmak için
git remote set-url origin git@github.com:koztay/development-notes.git
```
