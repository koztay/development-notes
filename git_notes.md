
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

