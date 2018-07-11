# Setup Gstreamer Development Environment
## For Linux
### Step - 1 : Install Gstreamer from scratch to ElementaryOS

Aşağıdaki komutu Gstreamer sitesinden aldım:

```
sudo apt-get install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-doc gstreamer1.0-tools
```

Yukarıdaki yöntem bence en uygunu, aşağıda derlerken hata verdi.

```
wget http://gstreamer.freedesktop.org/src/gstreamer/gstreamer-1.4.0.tar.xz
tar -xvf gstreamer-1.4.0.tar.xz
cd gstreamer-1.4.0

./configure --prefix=/usr \
            --with-package-name="GStreamer 1.4.0 BLFS" \
            --with-package-origin="http://www.linuxfromscratch.org/blfs/view/svn/" && make
sudo make install
```

### Step - 2 : Install Python Bindings

Eski versiyon EementaryOS 'ta (ubuntu12.04 tabanlı) sadece aşapıdaki komut çalıştı:

```
sudo apt-get install python-gst0.10
```

ElementaryOS_0.4.1 ((ubuntu16.04 tabanlı) içinse aşağıdaki komutların tamamı çalıştı (ancak en alt satırdaki gst0.10 versiyonunu yüklemeye gerek yok bence):

```
sudo apt-get install python-gst-1.0
sudo apt-get install python-gst-1.0-dbg
sudo apt-get install python-gst0.10
```

gstreamer plugin 'lerini de yüklemek lazım (yukarıda ilk stepte zaten yüklüyoruz, ayrıca aşağıdakiler 0.10 versiyonu için):

```
sudo apt-get install gstreamer0.10-plugins-base gstreamer0.10-plugins-good gstreamer0.10-plugins-ugly gstreamer0.10-plugins-bad

```

ugly ve bad olanlar kurulmadı, ve hep hata veriyor kod. Compile etmek gerek, bunun için aşağıdaki linklerden bad ve ugly source tarball 'ları indir:

[https://gstreamer.freedesktop.org/src/gst-plugins-bad/](https://gstreamer.freedesktop.org/src/gst-plugins-bad/)

[https://gstreamer.freedesktop.org/src/gst-plugins-ugly/](https://gstreamer.freedesktop.org/src/gst-plugins-bad/)


sonra sırasıyla aşağıdaki adımları uygula:

>adımları buraya yaz

ikinci yol github clone ile kurmak

```$ git clone https://github.com/GStreamer/gst-plugins-bad.git```

ve 

```$ git clone https://github.com/GStreamer/gst-plugins-ugly.git```

komutları ile download et. Sonra autogen.dh çalıştır. Hata veriyor gtk-doc kurulu olması lazımmış, onun için ```$sudo apt-get install gtk-doc-tools```kur sonra tekrar autogen.sh çalıştır. GStreamer versiyonları farklı imiş. Elementary 'deki 1.8.3 sen 1.15 plugini kuruyorsun hatası verdi. Bunun için tüm repoyu klonladığımızdan sıkıntı yok. ```git checkout 1.8.3```komutu verip bir daha autogen.sh çalıştır. Şimdi de base plugin yok diyor. Bakalım nasıl olacak. 
```git clone https://github.com/GStreamer/gst-plugins-base.git``` ile klonlayıp kuralım bakalım ne olacak?




### Step - 3 : Check if everything is OK!

```
Python 2.7.3 (default, Oct 26 2016, 21:04:23) 
[GCC 4.6.3] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import pygst
>>> pygst.require("0.10")
>>> import gst
>>> gst
<module 'gst' from '/usr/lib/python2.7/dist-packages/gst-0.10/gst/__init__.pyc'>
```

if there is no error on above then check if gobject is installed

```
>>> import gobject
>>>
```
if still there is no error the everything is OK!

## For MacOSX

### Step - 1 : Download gstreamer packages
Download all the 3 packages from the following link :

[https://gstreamer.freedesktop.org/data/pkg/osx/1.14.1/](https://gstreamer.freedesktop.org/data/pkg/osx/1.14.1/)

* gstreamer-1.0-1.14.1-x86_64-packages.dmg
* gstreamer-1.0-1.14.1-x86_64.pkg
* gstreamer-1.0-devel-1.14.1-x86_64.pkg

### Step -2 : Install pygst for mac with brew
Follow the instructions from the following link : 
> 
> [https://brewinstall.org/Install-gst-python-on-Mac-with-Brew/](https://brewinstall.org/Install-gst-python-on-Mac-with-Brew/)
> 
> 
> 
> Launch Terminal by pressing command+space, type terminal and hit Enter key.
> 
> ```
> $ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" < /dev/null 2> /dev/null
> ```
> 
> Run the above code if brew not installed. If installed simply run the following:
> 
> ```
> $brew install gst-python
> ```
> Çalışmıyor bu python bulamıyor ???
> 

### Yukarıda çözememiştim şimdi çözdüm:
 
1- Install gstreamer from their website ...normally and then

2- `brew install gstreamer gst-plugins-base gst-plugins-good gst-plugins-bad gst-plugins-ugly gst-libav`

3- `brew install gst-python --with-python3`

Note however that the 'gtk' for some reason doesn't come pre-bundled so we go to step 2.

4- `brew install gtk+3`


### Play audio file from command line:

```
$gst-launch-0.10 filesrc location=/path/to/audio.mp3 ! decodebin !
audioconvert ! autoaudiosink
```

```
$ gst-launch-0.10 filesrc location='/home/kemal/Music/yann_tiersen.mp3' ! decodebin ! audioconvert ! autoaudiosink
Setting pipeline to PAUSED ...
Pipeline is PREROLLING ...
Pipeline is PREROLLED ...
Setting pipeline to PLAYING ...
New clock: GstPulseSinkClock
```
şeklinde çıktı vererek müzik çalmaya başlar...

**Ubuntu 16.04 tabanlı dağıtımlarda (örnek bendeki ElementaryOS) komut değişiyor, çünkü versiyon değişiyor, aşağıdaki şekilde yazmazsan patlıyor:**

```
$ gst-launch-1.0 filesrc location='/home/kemal/Music/yann_tiersen.mp3' ! decodebin ! audioconvert ! autoaudiosink

```

> The autoaudiosink automatically detects the correct audio device on your computer to play the audio. If there is any error playing an audio, check if the audio device on your computer is working properly. You can also try using element sdlaudiosink that outputs to the sound card via SDLAUDIO . If this doesn't work, and you want to install a plugin for audiosink—here is a partial list of GStreamer plugins: http://www.gstreamer.net/data/doc/gstreamer/head/gst- plugins-good-plugins/html/
> Mac OS X users can try installing osxaudiosink if the default autoaudiosink doesn't work.


### Play audio file with python
#### Method 1:

```python
import thread
import time
import gobject
import pygst
pygst.require("0.10")
import gst


class AudioPlayer:
    def __init__(self):
        self.construct_pipeline()
        self.is_playing = False
        self.connect_signals()

    def construct_pipeline(self):
        my_pipeline_string = "filesrc location=/home/kemal/Music/yann_tiersen.mp3 " \
                             "! decodebin ! audioconvert ! autoaudiosink"
        self.player = gst.parse_launch(my_pipeline_string)

    def connect_signals(self):
        # In this case, we only capture the messages and put on the bus.
        bus = self.player.get_bus()
        bus.add_signal_watch()
        bus.connect("message", self.message_handler)

    def play(self):
        self.is_playing = True
        self.player.set_state(gst.STATE_PLAYING)
        while self.is_playing:
            time.sleep(1)
        evt_loop.quit()

    def message_handler(self, bus, message):
        # Capture the messages on the bus a
        # set the appropriate flag.
        msg_type = message.type
        if msg_type == gst.MESSAGE_ERROR:
            self.player.set_state(gst.STATE_NULL)
            self.is_playing = False
            print("\n Unable to play audio. Error: ", message.parse_error())
        elif msg_type == gst.MESSAGE_EOS:
            self.player.set_state(gst.STATE_NULL)
            self.is_playing = False


# Now run the program
player = AudioPlayer()
thread.start_new_thread(player.play, ())
gobject.threads_init()
evt_loop = gobject.MainLoop()
evt_loop.run()

```

#### Method 2:

```python
import thread
import time
import gobject
import pygst
pygst.require("0.10")
import gst


class AudioPlayer:
    def __init__(self):
        self.construct_pipeline()
        self.is_playing = False
        self.connect_signals()

    def construct_pipeline(self):
        self.player = gst.Pipeline()
        self.filesrc = gst.element_factory_make("filesrc")
        self.filesrc.set_property("location",
                                  "/home/kemal/Music/yann_tiersen.mp3")
        self.decodebin = gst.element_factory_make("decodebin", "decodebin")

        # Connect decodebin signal with a method
        # You can move this call to self.connect_signals
        self.decodebin.connect("pad_added", self.decodebin_pad_added)
        self.audioconvert = gst.element_factory_make("audioconvert", "audioconvert")
        self.audiosink = gst.element_factory_make("autoaudiosink", "a_a_sink")

        # Construct the pipeline
        self.player.add(self.filesrc, self.decodebin, self.audioconvert, self.audiosink)

        # Link elements in the pipeline.
        gst.element_link_many(self.filesrc, self.decodebin)
        gst.element_link_many(self.audioconvert, self.audiosink)

    def connect_signals(self):
        # In this case, we only capture the messages and put on the bus.
        bus = self.player.get_bus()
        bus.add_signal_watch()
        bus.connect("message", self.message_handler)

    def play(self):
        self.is_playing = True
        self.player.set_state(gst.STATE_PLAYING)
        while self.is_playing:
            time.sleep(1)
        evt_loop.quit()

    def message_handler(self, bus, message):
        # Capture the messages on the bus a
        # set the appropriate flag.
        msg_type = message.type
        if msg_type == gst.MESSAGE_ERROR:
            self.player.set_state(gst.STATE_NULL)
            self.is_playing = False
            print("\n Unable to play audio. Error: ", message.parse_error())
        elif msg_type == gst.MESSAGE_EOS:
            self.player.set_state(gst.STATE_NULL)
            self.is_playing = False

    def decodebin_pad_added(self, decodebin, pad):
        caps = pad.get_caps()  # caps means capabilities
        compatible_pad = self.audioconvert.get_compatible_pad(pad, caps)
        pad.link(compatible_pad)


# Now run the program
player = AudioPlayer()
thread.start_new_thread(player.play, ())
gobject.threads_init()
evt_loop = gobject.MainLoop()
evt_loop.run()

```

### Önemli :

> In gstreamer-1.0, ffmpegcolorspace was renamed to videoconvert. You need to give your decodebin the name you want to refer to it later. Add name=dec to be able to refer to it when linking the audio branch.

Also, you might want to consider just using playbin if you just need playback. It will assemble the pipeline for you and will support multiple formats automatically.



## xdmx example 
* Önce her iki makineye de xdmx kuruyoruz:
```
$ sudo apt-get install xdmx
```
sonra Xorg.conf dosyasında 24 bit olan renk derinliğini düşürüyoruz. Bu normal cihazlar kullanıldığında gereksiz olacak. Ancak benim cihazımın bir tanesi sanal makine olduğu için 24-bit renk derinliği sağlayamaz ise yapmak gerekecek. Şimdilik pas geçiyorum.

* Benim konfigürasyonumda sanal makine kontroller yani master olacak. Master Cihazımın IP adresi 10.0.0.43. Diğer cihazımın IP adresi ise 10.0.0.100 bu da ikinci makinem olacak. Master makinemde Xubuntu 14 yüklü diğer makinemde de Ubuntu 18.04 yüklü. Ubuntu 18.04  yüklü makinede aşağıdaki komutu veriyoruz :
```
$ xhost + 10.0.0.43
10.0.0.43 being added to access control list # şeklinde çıktı verdi.
```
* Şimdi ana kontrol cihazında aşağıdaki komutu ip 'leri yazarak vereceğiz:
```
startx `which twm` --         \
  /usr/bin/X11R6/Xdmx :1      \
  -display control_node_ip:0  \
  -display client_node_1_ip:0 \
  -ignorebadfontpaths     
```
yani :
```
startx `which twm` -- /usr/bin/Xdmx :1 -display 10.0.0.43:0  -display 10.0.0.100:0 -ignorebadfontpaths +xinerama    
```
```
startx `which twm` -- /usr/bin/Xdmx :1 -display 10.0.0.100:0  -display 10.0.0.43:0 -ignorebadfontpaths +xinerama    
```

bu şekilde çalışmıyor ama ssh ile bağlanlıp çalıştırıldığında oluyor. Aşağıda nasıl olduğunu anlatan bir video var:

> https://www.youtube.com/watch?v=YOzRCBGDVaE&feature=youtu.be&app=desktop
> 
> First thing you want to do is install xdmx on every machine that you are going to use, for me its my main workstation and my old laptop.  The problem is that the xdmx package that is in the ubuntu repositories is broken, it throws seg faults left and right.  So go here http://packages.debian.org/etch/xdmx and download xdmx.

> Install it with

> sudo dpkg -i xdmx_1.1.1-21etch5_i386.deb

> To run xdmx securely you are going to want to run it through an ssh tunnel.  You can also run xdmx by turning on X11 tcp listening but this is a huge security hole.  It will allow anyone on your network to see every keystroke you make in an X environment.  So on the computer that you are going to use as the xserver (my workstation) you need to have an ssh-server installed.

>sudo apt-get install openssh-server

>Now from my old laptop (your second display) ssh into your main workstation.  To make things faster use a blowfish cypher.
>
>ssh -X -c blowfish-cbc user@maincomputer

>Now type:

>export |grep DISP

>you see something similar to this: (the numbers might be different)


>paradox@griver:~$ export |grep DISP
>
>declare -x DISPLAY="localhost:10.0"


>Now you need to start an xdmx xserver.  You need to run the command on the main workstation but from the laptop.  So from your ssh shell run:

>startx  -- /usr/bin/Xdmx :1 +xinerama -display :0.0 -display localhost:10.0 -norender -noglxproxy

>the startx  -- /usr/bin/Xdmx starts and Xdmx xserver.  The :1 is the display number.  If you are already running a desktop it is probably on :0.  +xinerama makes both computers act as a giant screen.  The first -display :0.0 is the display for your main machine and the second is the display for the old laptop.  The -norender and -noglxproxy makes everything render smoothly, but it will prevent you from running openGL stuff and you wont have any graphic effects.  I had to do this because my laptop has and ATI card and my workstation has an NVIDIA.  It they both have a similar card you probably don't need these.

* Check open ports ```$ sudo ufw status verbose``` the port number 6000 should be opened
* Ancak X-server 'ın bu portu dinlemesi için de ayar yapmak gerek. Örneğin Ubuntu 12.04 için aşağıdaki ayarı yapmak lazım :

>
>After an upgrade to 12.04, I had the same issue. This time, the culprit is the lightdm that the system uses. The file that needs to be updated is /etc/lightdm/lightdm.conf and the required addition is a xserver-allow-tcp=true in the [SeatDefaults] section.

sonrasında ayarın aktif olması için (ubuntu 'da OK, ama diğerlerinde değil):

```
$ sudo service gdm restart  # veya
$ sudo systemctl restart lightdm.service
```

Ubuntu 18.04 içinse aşağıdaki şekilde :

>
>Then I edited /etc/gdm3/custom.conf and added a line:
>
>```
>[security]
>DisallowTCP=false
>```




# Faydalı Linkler:
* [https://github.com/brettviren/pygst-tutorial-org](https://github.com/brettviren/pygst-tutorial-org) : pygst 1.o versiyonu ile ilgili tutoriallar mevcut.
* [HW accelerated GUI apps on Docker](https://medium.com/@pigiuz/hw-accelerated-gui-apps-on-docker-7fd424fe813e)
* [We can virtualize even GUI Text Editor with Docker](https://dev.to/acro5piano/we-can-virtualize-even-gui-text-editor-with-docker-container--5bhh)
* [Running GUI Applications inside Docker Containers](https://medium.com/@SaravSun/running-gui-applications-inside-docker-containers-83d65c0db110)
* [Python version of official GStreamer tutorials ](https://github.com/gkralik/python-gst-tutorial)
* [gst0.10 to Gst1.0 Conversion](https://adnanalamkhan.wordpress.com/2015/03/01/using-gstreamer-1-0-with-python/)
* [Gstreamer streaming server with Docker](https://github.com/mcfletch/gstreamer-tutorial) - Çalışmıyor
* [Gstreamer Media Player](https://github.com/dschreij/media_player_gst)
* [Gstreamer h264 streamer](https://github.com/gebart/livestream)
* [Examples of Programming Multimedia Applications in Gstreamer developed in Python](https://github.com/robertogerson/tutorial-webmedia16-py)
* [RTP server ve Client kodları var](https://github.com/edipdemirbilek/GStreamerMultimediaQualityTestbed) - Bu çalışabilir temiz koda benziyor...
* [Python examples for grabbing video devices (e.g. webcam, video, ...) and interacting with GStreamer (Shared Memory, RTP Stream)](https://github.com/madams1337/python-opencv-gstreamer-examples)
* [Python Gstreamer Video Player with decodebin](https://github.com/dilipshirke/Python_Gstreamer_Player) -- bu çalışmıyor.
* [PyGTK 2.* notebook](https://github.com/majorsilence/pygtknotebook/tree/master)
* [Xlib Örnek](https://unix.stackexchange.com/questions/5999/setting-the-window-dimensions-of-a-running-application)
* [How X Window Managers Work, And How To Write One (Part I)](https://seasonofcode.com/posts/how-x-window-managers-work-and-how-to-write-one-part-i.html)
* [GStreamer Daemon is a GStreamer framework for controlling audio and video streaming using TCP messages.](https://github.com/RidgeRun/gstd-1.x)
* [GStreamer Daemon Anasayfası](https://developer.ridgerun.com/wiki/index.php?title=GStreamer_Daemon)
* [https://github.com/UbiCastTeam/gstmanager-1.0 for pipleline manipulation](https://github.com/UbiCastTeam/gstmanager-1.0)
* [Gstreamer basic real time streaming tutorial](http://www.einarsundgren.se/gstreamer-basic-real-time-streaming-tutorial/)
* [Vala Gstreamer Examples](https://wiki.gnome.org/Projects/Vala/GStreamerSample)
* [OPO Python Videowall](https://github.com/douglasbagnall/opo)
* [Hardware acceleretaed video playback on RaspberryPi](https://wiki.matthiasbock.net/index.php/Hardware-accelerated_video_playback_on_the_Raspberry_Pi)
* [Streaming H.264 via RTP](https://github.com/matthiasbock/gstreamer-phone/wiki/Streaming-H.264-via-RTP)
* [Using Gstreamer to capture screen and show it in a window?](https://stackoverflow.com/questions/33747500/using-gstreamer-to-capture-screen-and-show-it-in-a-window)
* [The DMX server, Xdmx](https://codeyarns.com/2015/07/02/multihead-display-using-xdmx/)
* [Distributed multihead support with Linux and Xdmx (IBMs tutorial)](https://www.ibm.com/developerworks/library/os-mltihed/index.html)
* [Sage2 Videowall Projesi](https://sage2.sagecommons.org/)
* [9 Screen Wall 1](https://nurdspace.nl/9_Screen_Wall_1)
* [Fake Xdmx](https://www.gilesorr.com/blog/fake-xdmx.html)
* [Creating a Public Web Server on Raspberry Pi](http://alexdberg.blogspot.com/2012/11/creating-public-web-server-on-raspberry.html)