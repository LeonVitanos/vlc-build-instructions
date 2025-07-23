**Configure and build VLC from scratch**
========================================

Tested on Ubuntu 22.04 & 25.04

**Qt**
------

*   Required for running Qt installation file:
    
    *   Ubuntu 22.04: sudo apt install libxcb-xinerama0
        
    *   Ubuntu 25.04: sudo apt install libxcb-cursor0 libxcb-cursor-dev
        
*   Download Qt online installer: [https://download.qt.io/official\_releases/online\_installers/](https://download.qt.io/official_releases/online_installers/)
    
    *   To be able to run the setup file, you will need to right click it -> Properties -> Permissions -> Enable 'Allow executing file as a program'
        
    *   Select custom installation
        
    *   Click filter -> 'Archives'
        
    *   Select to install for 5.15.2 
        
    *   Select to install for 6.x the desktop gcc 64-bit, Qt Shader Tools and Qt5 Compatibility mode
        
        *   where x is the current latest version supported to run vlc
            
*   Make sure the prefix of the files in home/leon/Qt/6.x/gcc\_64/lib/pkgconfig/ are correct:
    
    *   sed -i -e "s#prefix=/home/qt/work/install#prefix=/home/leon/Qt/6.8.0/gcc\_64#" /home/leon/Qt/6.8.0/gcc\_64/lib/pkgconfig/\*.pc
        
*   Set Qt environment variables
    
    *   export LD\_LIBRARY\_PATH=$LD\_LIBRARY\_PATH:/home/leon/Qt/6.x/gcc\_64/lib
        
    *   export PATH=$PATH:/home/leon/Qt/6.x/gcc\_64/bin

## Install Required Packages

```bash
sudo apt install git build-essential pkg-config libtool automake autopoint gettext
sudo apt install cmake yasm python3-venv # Required for building 'contrib'
sudo apt install libxcb-xkb-dev libxcb-damage0-dev # Required for building 'vlc'
sudo apt-get install qml-module-qtgraphicaleffects qml-module-qtqml-models2 qml-module-qtquick-controls2 qml-module-qtquick-layouts qml-module-qtquick-templates2 # Required for running 'vlc'
```

Enable sources on Ubuntu (https://askubuntu.com/questions/496549/error-you-must-put-some-source-uris-in-your-sources-list)
```bash
sudo apt-get build-dep vlc
sudo apt install python-fontforge # NOTE: This is required for generating 'vlc' icons font.
```

**Clone VLC**
-------------

*   Optional:
    
    *   fork https://code.videolan.org/videolan/vlc
        
*   Connecting to GitLab with SSH
    
    *   Generate rsa: ssh-keygen -t rsa -b 2048 -C "{YOUR\_EMAIL}"
        
    *   Upload {YOUR\_HOME}/.ssh/id\_rsa.pub to code.videolan.org
        
    *   For more information read: https://docs.github.com/en/authentication/connecting-to-github-with-ssh
        
*   git clone https://code.videolan.org/videolan/vlc
    
    *   or in case you forked: git clone https://code.videolan.org/{YOUR\_FORK}/vlc
        

**Build VLC tools**
-------------------
```bash
cd vlc/extras/tools
./bootstrap
make -j4
```  

**Build VLC contrib**
---------------------
```bash
cd ../../contrib
mkdir contrib-nativ
cd contrib-nativ
../bootstrap --disable-gcrypt --disable-bluray
make -j4
```

**Build VLC**
-------------
```bash
cd ../..
./bootstrap
mkdir build-qt11
cd build-qt11
../configure '--disable-optimizations' '--enable-debug' '--disable-nls' '--without-kde-solid' '--enable-qt-qml-cache' '--enable-qt-qml-debug' '--disable-skins2' '--disable-upnp' '--disable-chromecast' '--disable-srt' '--disable-aom' '--disable-bluray' 'CFLAGS=-ggdb -O0 -fno-omit-frame-pointer' 'PKG\_CONFIG\_PATH=/home/leon/Qt/6.8.0/gcc\_64/lib/pkgconfig/:/home/leon/vlc/contrib/x86\_64-linux-gnu/lib/pkgconfig/'
make -j4
```  

**Configure Qt Creator Project**
--------------------

*   Open Qt Creator
    
*   File -> New Project -> Import existing project -> Change 'Location' to {YOUR\_HOME}/vlc
    
    *   In 'File Selection' page it might be a good idea to:

        *   Add *.qml; to the files matching selection
     
        *   Not include the 'contrib', 'extras' and 'build-qt11' folder as you don't need QtCreator to index them
        
*   Go to the 'Projects' page
    
    *   Make sure the build environment is the Qt version you want to build with (e.g Desktop 6.9.1)
        
    *   On the 'Build' page, change the build directory to {YOUR\_HOME}/vlc/build-qt11
        
    *   On the 'Run' page, change the executable to {YOUR\_HOME}/vlc/build-qt11/vlc
