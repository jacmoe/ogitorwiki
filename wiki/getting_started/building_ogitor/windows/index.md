<!--
title: Windows
template: wiki
order: 3
-->
Ogitor builds with Visual Studio and QtCreator, provided that CMake is used to generate the set of build scripts / project files.

## Dependencies
### Qt Toolkit
Ogitor is using the Qt Toolkit for it's graphical interface. 

Grab the the download that matches your OS and install the SDK.

**Note:** Previously (before Qt was moved to Digia) you had in most cases built Qt yourself. See the old notes for that scenario below:

You don't need to build the OpenGL parts of Qt, as Ogitor is not using any of it.

Be sure to add bin to your PATH. And a QTDIR environment variable needs to be set to the Qt root directory: setx QTDIR 

To compile Qt from source: 
1. Open a Visual Studio command prompt and navigate to your Qt source. 
2. Run configure -platform win32-msvc2008 -nomake demos -nomake examples -no-opengl -no-qt3support.(Change platform to whatever compiler you are using).
3. Run nmake. 

### Ogre
Ogitor 0.5 depends on Ogre 1.9.x (Ghamadon - unstable). If you compiled Ogre from source, be sure to build the INSTALL project whcih will generate your SDK folder.

You need to add an OGRE_SDK or OGRE_HOME environment variable pointing to your matching Ogre SDK, e.g. like so: setx OGRE_BYATIS \sdk

### Boost
Ogitor development is using Boost Regex and Boost Filesystem. In order for it to be found by CMake please create the following two environment variables:
`BOOST_ROOT` - pointing to your root boost folder, e.g. `D:\boost_1_51_0\\`
`Boost_REGEX_LIBRARY` - pointing to the boost lib folder, e.g. `D:\boost_1_51_0\lib\\`
`Boost_FILESYSTEM_LIBRARY` - same as above.

## Ogitor
### Getting the source
Get the source from: (link: https://bitbucket.org/ogitor/ogitor)

Or, using the command-line:

```bash
hg clone https://bitbucket.org/ogitor/ogitor.
```

The directories `"RunPath/Media"` and `"RunPath/Projects"` are now excluded from repository. Instead they are supplied as extra downloads from: http://www.ogitor.org/media.zip and http://www.ogitor.org/projects.zip 
Please download and extract them to your SOURCE path's `"RunPath"` directory in order to build and install Ogitor.

### Building Ogitor

- Run cmake-gui.
- Choose the Ogitor source directory and a build directory.
- The build directory should be different from you source directory.
- Click the 'configure' button. 
- After choosing your build environment, you'll see red CMake entries.
- Click the 'configure' button one more time, and examine the results.
- If everything look alright, click the 'generate' button.
- Close CMake GUI. 
- Find your build directory and open the Ogitor solution. 