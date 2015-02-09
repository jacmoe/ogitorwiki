<!--
title: Linux
template: wiki
order: 3
-->
## Prerequisites
Before compiling and installing Ogitor you must first install the following packages

### For Ubuntu / Debian

```bash
sudo apt-get install cmake pkg-config libfreeimage-dev libfreetype6-dev libz-dev libzzip-dev libgl-dev libxrandr-dev libxaw7-dev libxt-dev nvidia-cg-toolkit doxygen graphviz libois-dev libboost-dev mercurial wget unzip build-essential
```
## Obtaining Ogre 1.9
Ogitor 0.5 requires Ogre 1.9 if you don't have Ogre 1.9 installed then you can build Ogre from source. Uninstall Ogre from your package manager then build and install Ogre 1.9 with the following commands.
```bash
hg clone ssh://hg@bitbucket.org/sinbad/ogre -b v1-9
cd ogre
cmake .
make
sudo make install
```

## Getting Ogitor
The following commands will get you the most current Ogitor source and Media files required for using Ogitor.
```bash
hg clone ssh://hg@bitbucket.org/ogitor/ogitor -b default
wget https://bitbucket.org/ogitor/ogitor/downloads/media.zip
unzip media.zip -d ./ogitor/RunPath/ 
wget https://bitbucket.org/ogitor/ogitor/downloads/projects.zip
unzip projects.zip -d ./ogitor/RunPath/ 
```

## Compiling Ogitor
First generate the make files with CMake.
```bash
mkdir ogitor-build
cd ogitor-build
cmake ../ogitor
```

You can run cmake from within your ogitor mercurial repository however it is advisable to build Ogitor in a directory separate from it. This way you can manage your ogitor mercurial repository without the build tools putting junk data into it.

Now run make and make install.
```bash
make
sudo make install
```

## Running Ogitor
After performing make install you can run Ogitor by typing "qtOgitor".