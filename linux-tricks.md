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


 
