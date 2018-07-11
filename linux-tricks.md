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

```
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





