## 1- scp with password :

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

```sh
sshpass -p "password" scp -r centos_backup.sh kemal@10.0.0.1:/home/kemal
```

## Setup Development Environment with  vim and tmux

###Resources :

[Vim Graphical Cheat Sheet](http://www.viemu.com/a_vi_vim_graphical_cheat_sheet_tutorial.html)

[http://martinbrochhaus.com/toolbelt.html](http://martinbrochhaus.com/toolbelt.html)

[.vimrc file from MartinBrochhaus](https://github.com/mbrochh/vim-as-a-python-ide/blob/master/.vimrc)

[Vim as Python IDE - Martin Brochhaus](https://www.youtube.com/watch?v=YhqsjUUHj6g)

[https://realpython.com/blog/python/vim-and-python-a-match-made-in-heaven
](https://realpython.com/blog/python/vim-and-python-a-match-made-in-heaven)

[https://www.fullstackpython.com/vim.html](https://www.fullstackpython.com/vim.html)

[Powerline Common segments](http://powerline.readthedocs.io/en/master/configuration/segments/common.html)

**1- install/update vim latest version (OSX):**

Aşağıdaki komut ile kur. Asla mevcut sistemdeki vim 'e dokunma.

```sh
brew install vim -–with-python3 --with-lua
```
Mevcut vim path OSX 'te :

```sh
/usr/bin/vim
```

pathinde. Homebrew tarafından kurulan path ise aşağıdaki gibi:

```sh
/usr/local/bin/vim
```

dolayısıyla .zsrc dosyasına alias tanımlayarak yeni -vim versiyonunun kullanılmasını sağlamalalıyız. Bunun için .zshr dosyasına aşağıdaki satırı ekliyoruz:

```sh
alias vim='/usr/local/bin/vim'
```

sonrasında bu şekilde yeni vim kullanılmaya başlamış oluyor.

**2- Install vundle:**

Install it with the following command:

```sh
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

configure plugins setup vundle to work in ~/.vimrc file:

```sh
set nocompatible              " be iMproved, required
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'VundleVim/Vundle.vim'

" The following are examples of different formats supported.
" Keep Plugin commands between vundle#begin/end.
" plugin on GitHub repo
Plugin 'tpope/vim-fugitive'
" plugin from http://vim-scripts.org/vim/scripts.html
Plugin 'L9'
" Git plugin not hosted on GitHub
Plugin 'git://git.wincent.com/command-t.git'
" git repos on your local machine (i.e. when working on your own plugin)
" Plugin 'file:///home/gmarik/path/to/plugin'
" The sparkup vim script is in a subdirectory of this repo called vim.
" Pass the path to set the runtimepath properly.
Plugin 'rstacruz/sparkup', {'rtp': 'vim/'}
" Install L9 and avoid a Naming conflict if you've already installed a
" different version somewhere else.
" Plugin 'ascenator/L9', {'name': 'newL9'}
" do not use this it conflicts with L9

" All of your Plugins must be added before the following line
call vundle#end()            " required
filetype plugin indent on    " required
" To ignore plugin indent changes, instead use:
"filetype plugin on
"
" Brief help
" :PluginList       - lists configured plugins
" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
" :PluginSearch foo - searches for foo; append `!` to refresh local cache
" :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal
"
" see :h vundle for more details or wiki for FAQ
" Put your non-Plugin stuff after this line
set nu 							" this is for line numbering
```

after that give the follwing command to install the plugins:

```sh
vim +PluginInstall
```

sonrasında şu pluginler öneriliyor, önerilenleri .vimrc dosyasına aşağıdaki gibi sırayla ekleyebilirsin, ancak şöyle bir öneri var. Tüm plugin 'leri hurraa diye ekleme, teker teker deneyip ne iş yaptıklarını anlaya anlaya ekle :

```sh
" Plugin 'davidhalter/jedi-vim'   " code completion plugin forget this use below
Plugin 'Valloric/YouCompleteMe'			  " awesome code completion plugin
Plugin 'tmhedberg/SimpylFold' 			  " code folding plugin
Plugin 'vim-scripts/indentpython.vim'	  " auto-indetation plugin
Plugin 'scrooloose/syntastic'				  " syntax checking plugin
Plugin 'nvie/vim-flake8'					  " PEP8 checking plugin
Plugin 'jnurmine/Zenburn'					  " color schemes
Plugin 'altercation/vim-colors-solarized' " color schemes


```

Afte adding YouCompleteMe plugin via vundle ther is one more stepe to compile it. And this step is mandatory. If you don' apply this step then it does not work. After putting the above Plugins to .vimrc apply the follwing command to vundle install it to vim:

```sh
vim +PluginInstall
```
and after that compile YouCompleteMe to work with vim. The official YouCompleteMe documentation says that install macvim but I tried it and didn't like. So I decided to use the standart vim. Give the following commands to compile YouCompleteMe:

```sh
cd ~/.vim/bundle/YouCompleteMe
./install.py --clang-completer  # with semantic support for C-family languages
# or give just without the --clang-completer flag as below:
./install.py 
```

after runing the compile script it compiles to work with vim properly.

Add the following lines to .vimrc :

**for the plugin configurations above:**

```sh
let g:SimpylFold_docstring_preview=1 	" to see the docstrings in folded code
let python_highlight_all=1					" syntax checking settings
syntax on										" syntax checking settings

" set color schemes according to vim mode
if has('gui_running')
set background=dark
colorscheme solarized
else
colorscheme zenburn
endif

call togglebg#map('<F5>')			" toggle solarized light and dark themes by F5

```

Install the following plugin for virtualenvironment support:

```sh
Plugin jmcantrell/vim-virtualenv
```

and you can prove that you are in the environment with the following vim command:


```sh
:echo system('which python')
```

one more thing if there is an activated virtualenvironment we should see it on the powerline status bar. But virtualenv support is disabled by default. So we should enable it by ourselves. First create a following folders accordingly in your home directory:

```sh
└── ~ (your home directory)
	└── .config
		└── powerline
			└── themes
				└── vim
					└── default.json
```

and copy the default.json file from the following path:

```
~/.vim/bundle/powerline/powerline/config_files/themes/vim/default.json
```

becuase we don't want to destroy any defualt settings.
And put the following settings in the json file (I put it in the right side of the status bar as you see below:

```json
"right": [
			{
				"function": "powerline.segments.common.env.virtualenv",
				"draw_soft_divider": false,
				"exclude_modes": ["nc"],
				"priority": 60
			},
			{
			....
			},
			{
			....
			},
			....
```


I also installed solarized powerline theme for vim:

```
Plugin stephenmckinney/vim-solarized-powerline

```

and set the following settings of it in .vimrc file:

```
g:Powerline_theme='short'
g:Powerline_colorscheme='solarized256_dark'
```

That's it!!! It works now...

## Fix Nothing in register * error while duplicating line with 'yy'(copy line) + 'p'(paste after) or 'P'(paste before) in vim with tmux

1. Install Homebrew
1. Install reattach-to-user-namespace: "brew install reattach-to-user-namespace"
1. in .vimrc: set clipboard=unnamed
1. Tell tmux to use the system clipboard: In .tmux.conf: set-option -g default-command "reattach-to-user-namespace -l bash" or replace bash with zsh if you are using zshell.


## Copy OSX home folder with booting live Ubuntu/Xubuntu USB:

1. Turn on your Mac by pressing the power button by choosing Restart in the Apple menu, or restart it if it's already on.
2. Press and hold the Option (⌥) key immediately upon hearing the startup chime. Release the key after Startup Manager appears.
3. After booting open a terminal window and type:

 ```$ sudo -s```
 
 adn press enter
 
4. locate the source path => home folder of OSX  file system (usually it is /Users directory)
5. cd into source

```$ cd /media/xubuntu/<some-magic-numbers>/Users```

6. locate the target => usually the external HDD 
7. type the rsync command to copy all the files from source as below:

```$ rsync -avzh <user-folder-of-the-osx> /media/xubuntu/<EXTERNAL-DRIVE-PATH>```

that's it! It should start copying now...

## Login with ssh_keys for ubuntu:

1. run ssh-copy_id 

```
$ ssh-copy-id user@123.45.56.78

user@123.45.56.78's password:

Number of key(s) added:        1

Now try logging into the machine, with:   "ssh 'root@172.104.132.120'"
and check to make sure that only the key(s) you wanted were added.
```
thats it. Now it works without password. 

## Disable root login with password:
In order to do this, open up the SSH config file:

```
$ sudo nano /etc/ssh/sshd_config
```

Within that file, find the line that includes PermitRootLogin and modify it to ensure that users can only connect with their SSH key:

```
PermitRootLogin without-password
PasswordAuthentication no
```
Put the changes into effect:

```
$ reload ssh  # Ubuntu 14.04 için
$ systemctl restart sshd.service  # ubuntu 16.04,  Centos7 vb. için
```


## mailinabox installation:
1. run the following script:

```
$ curl -s https://mailinabox.email/setup.sh | sudo bash
```

2. e-posta adresi olarak mutlaka kurulacak domaine ait bir e-posta gir.
3. domain olarak da box.domain.com olsun. 
4. timezone => Europe => Istanbul entera bas
5. kurulumu bitene kadar bekle.

SpamAssasin whitelist/blacklist ayarları:
[https://www.akadia.com/services/postfix_spamassassin.html](https://www.akadia.com/services/postfix_spamassassin.html)

```sh
$ cd /etc/mail/spamassassin
$ nano local.cf

# içeriğe aşağıdaki formatta ekleme yap
blacklist_from *@example1.com *@example2.com *@example2.com
whitelist_from *@example1.com *@example2.com *@example2.com

```

update ve upgrade Ubuntu update 'leri:

```
sudo apt-get update && sudo apt-get upgrade
sudo reboot
```
mailinabox update:

```
curl -s https://mailinabox.email/setup.sh | sudo bash
```

### SSL sertifikası yenileyememe sorunu çözümü:
```sh
# ssl klasörünü move et
mv /home/user-data/ssl /home/user-data/ssl_old
# boş bir ssl klasörü aç
mkdir /home/user-data/ssl
# kurulumu çalıştır
sudo mailinabox
# kurulum bitince ssl yenileyemez ise sertifika yaratma programını çalıştır
cd ~/mailinabox/management
./ssl_certificates.py
# şimdi sertifikanın yenilenmiş olması lazım.
# ssl_old klasörünü sil
rm -rf /home/user-data/ssl_old
```

### Upgrade from ubuntu 14.04 to 18.04 (v0.30 to v0.40):
https://discourse.mailinabox.email/t/mail-in-a-box-version-v0-40-and-moving-to-ubuntu-18-04/4289

1. Create a snapshot on Linode for OLD BOX.
2. Make a backup on OLD BOX:

```sh
cd mailinabox
sudo management/backup.py
```

3. Copy backup files and folders from OLD BOX to local PC:

```sh
scp -r root@box.takvim.in:/home/user-data/backup/encrypted .
scp -r root@box.takvim.in:/home/user-data/backup/secret_key.txt .
```
4. Create a new Ubuntu 18.04 machine for your new Mail-in-a-Box
5. Instal MailInABox with ssh 'in to the machine:

```sh
curl -s https://mailinabox.email/setup.sh | sudo -E bash
```
6. Restore your backup

First, move aside the new machine’s empty Mail-in-a-Box user-data directory.

```sh
mv /home/user-data /tmp/user-data.empty
```

then :

```sh
scp -r encrypted root@172.104.240.118:/home
scp -r secret_key.txt root@172.104.240.118:/home

ssh root@172.104.240.118
export PASSPHRASE=$(cat /path/to/secret_key.txt)
sudo -E duplicity restore --force file:///home/encrypted /home/user-data/
# bunda şu hatayı verdi çünkü yukarıda user-data klasörünü move etmeyi unutmuşum:
Error '[Errno 17] File exists' processing ssl/ssl_certificate.pem
```

restore from amazon (frankfurt için) :
```sh
export AWS_ACCESS_KEY_ID=paste your AWS access key ID here
export AWS_SECRET_ACCESS_KEY=paste your AWS secret access key here
export PASSPHRASE=$(cat /home/secret_key.txt)
duplicity restore --force s3://s3.eu-central-1.amazonaws.com/karnas-mailinabox/ /home/user-data/
```

7. Re-configure the box:

Re-run Mail-in-a-Box setup now that your old files are back:

```sh
sudo mailinabox
```

## vsftpd ile USB HDD 'ye erişip yazmak:
hiçbirşekilde başka yere yazmaya izin vermiyor. Bunun için önce home folder da bir klasör açıyoruz. Sonra o klasöre USB HDD 'de yazacağımız yeri mount ediyoruz:

```
$ mount --bind <USB_HDD_PATH> <FTP_USERS_PATH>
$ mount --bind /media/source_disk/HENKEL /home/admin_henkel/USB_HDD
```
 
## Ubuntu VNC bağlananama sorunu:
Turn off Vino encryption :

```
$ gsettings set org.gnome.Vino require-encryption false
```
şu linkte de anlatmış : [https://websiteforstudents.com/access-ubuntu-18-04-lts-beta-desktop-via-vnc-from-windows-machines/](https://websiteforstudents.com/access-ubuntu-18-04-lts-beta-desktop-via-vnc-from-windows-machines/)


## Restart/Autorestart Script with Supervisor
https://www.digitalocean.com/community/tutorials/how-to-install-and-manage-supervisor-on-ubuntu-and-debian-vps

The program configuration files for Supervisor programs are found in the ```/etc/supervisor/conf.d``` directory, normally with one program per file and a .conf extension. A simple configuration for our script, saved at ```/etc/supervisor/conf.d/long_script.conf```, would look like so:

for python add it as below:

```
[program:test]
command=/usr/bin/python /home/ubuntu/test.py
directory=/home/ubuntu
autostart=true
autorestart=true
startretries=3
stderr_logfile=/home/ubuntu/test.err.log
stdout_logfile=/home/ubuntu/test.out.log
user=ubuntu
```

supervisor komutları :

```
$ sudo supervisorctl reread
$ sudo supervisorctl update
$ sudo supervisorctl



```

To enter the interactive mode, start supervisorctl with no arguments:

```
$ supervisorctl
long_script                      RUNNING    pid 12614, uptime 1:49:37
supervisor>
```

When started, supervisorctl will initially print the status and uptime of all programs, followed by showing a command prompt. Entering help will reveal all of the available commands that we can use:

```
supervisor> help

default commands (type help ):
=====================================
add    clear  fg        open  quit    remove  restart   start   stop  update
avail  exit   maintail  pid   reload  reread  shutdown  status  tail  version
```

To start in a simple manner, we can start, stop and restart a program with the associated commands followed by the program name:

```
supervisor> stop long_script
long_script: stopped
supervisor> start long_script
long_script: started
supervisor> restart long_script
long_script: stopped
long_script: started
```

Using the tail command, we can view the most recent entries in the stdout and stderr logs for our program:

```
supervisor> tail long_script
Sun Jul 21 00:36:10 UTC 2013
Sun Jul 21 00:36:11 UTC 2013
Sun Jul 21 00:36:12 UTC 2013
Sun Jul 21 00:36:13 UTC 2013
Sun Jul 21 00:36:14 UTC 2013
Sun Jul 21 00:36:15 UTC 2013
Sun Jul 21 00:36:17 UTC 2013

supervisor> tail long_script stderr
error!
error!
error!
error!
error!
error!
error!

```

Using status we can view again the current execution state of each program after making any changes:

```
supervisor> status
long_script                      STOPPED    Jul 21 01:07 AM
```

Finally, once we are finished, we can exit supervisorctl with Ctrl-C or by entering quit into the prompt:

```
supervisor> quit
```

And that's it! You've mastered the basics of managing persistent programs through Supervisor and extending this to your own programs should be a relatively simple task. If you have any questions or further advice, be sure to leave it in the comments section.

## Supervisor Set Environment Variables
https://stackoverflow.com/questions/17055951/how-to-set-environment-variables-in-supervisor-service

```
# To add a single environment variable, You can do something like this.

[program:django]
environment=SITE=domain1
command = python manage.py command

# But, if you want to export multiple environment variables, you need to separate them by comma.

[program:django]
environment = 
    SITE=domain1,
    DJANGO_SETTINGS_MODULE=foo.settings.local,
    DB_USER=foo,
    DB_PASS=bar
command = python manage.py command

```

## Klasör size 'larını görmek:
```
$ du -sh -- * .*
```

## Mailinabox Volume eklemek:

https://discourse.mailinabox.email/t/mailboxes-storage-location-and-quota/3704/7


önce ssh root@box.domain.com ile login oluyoruz ve mail server'ı aşağıdaki komutlar ile kapatıyoruz ki, mail vs gelmesin biz işlem yaparken:

```sh
sudo ufw reset
sudo ufw allow 22
sudo ufw enable
```
Akabinde Linode 'da Volume yaratıyoruz.
Yarattığımız Volume 'u önce /mnt/tmp klasörü altına mount edip mevcut user mail datasını buraya kopyalamamız gerekiyor:

```sh
mkfs.ext4 /dev/disk/by-id/scsi-0Linode_Volume_userdata
mkdir /mnt/temp
mount /dev/disk/by-id/scsi-0Linode_Volume_userdata /mnt/temp
cd /home/user-data
cp -av * .* /mnt/temp
```

permissionları fix 'liyoruz:

```sh
chown user-data.user-data /mnt/temp
```

şimdi yeni volume 'ümüzü unmount edip mevcut mail datasını backup olarak .old uzantısı ile move ediyoruz.

```sh
umount /mnt/temp
cd /home
mv user-data user-data.old
```


Şimdi yeni Volume 'ü user-data klasörüne mount etmeliyiz:

```sh
mkdir user-data
mount /dev/disk/by-id/scsi-0Linode_Volume_userdata user-data
```

son olarak reboot ettiğimizde de aynı değişkiliklerin geçerli olması için /etc/fstab  dosyasına aşağıdaki satırı ekliyoruz :

```sh
/dev/disk/by-id/scsi-0Linode_Volume_userdata /home/user-data ext4 defaults,noatime 0 2
```

son olarak tekrar firewall kurallarını eski haline getirip normal olarak çalışması için mailinabox komutunu çalıştırıyoruz :

```sh
mailinabox
```
akabinde tüm güncellemelerin yapılması ve mail serverımızın normal olarak çalışması gerek.

## redis transparent huge pages (THP) hatası

Sırasıyla sanal makinede (docker container ile alakalı değil bu konu) aşağıdaki adımları uyguluyoruz.

```sh
$ echo never > /sys/kernel/mm/transparent_hugepage/enabled
# satırını çalıştırdık ve sonrasında bu satırı /etc/rc.local
$ nano /etc/rc.local # ile dosyayı aç
# echo never > /sys/kernel/mm/transparent_hugepage/enabled
# satırını yapıştırdık

```

Şimdi ikinci uyarı olarak aşağıdakini verdi:
WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect. ssh ile sanal makineye tekrar login ol:

```sh
$ nano /etc/sysctl.conf
# vm.overcommit_memory = 1
# satırını ekle
# ardından da aşağıdaki komutu çalıştır:
sysctl vm.overcommit_memory=1

```

sonrasında da redis 'i restart ediyoruz.


