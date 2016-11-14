**1- scp with password :**

önce sshpass kur:
```shell
brew install https://raw.githubusercontent.com/kadwanev/bigboybrew/master/Library/Formula/sshpass.rb
```

scp'nin çalışma mantığı şu şekilde :
```shell
scp -r source_location target_location
```


sonra aşağıdaki komutu ver (-r parametresi, klasör için recursive anlamında) :
```shell
sshpass -p "password" scp -r user@example.com:/some/remote/path /some/local/path
```

dolayısıyla ben lokal makinemden remote'a kopyalamak için aşağıdaki komutu verdim:

```shell
sshpass -p "password" scp -r centos_backup.sh kemal@10.0.0.1:/home/kemal
```



