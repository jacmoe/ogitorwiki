<!--
title: Building Ogitor
template: wiki
order: 3
-->
Ogitor depends on the Qt Framework, OGRE, a few specific plugins that come bundled with OGRE, and Boost-regex.

Currently:

-  **Qt** version 4.6 and up (dedicated Qt5 branch in development)
-  **Ogre** 1.9 'Ghadamon' 
 - OctreeSceneManager Plugin
 - ParticleFX Plugin
 - DirectX9 Rendering System (if on Windows)
 - OpenGL Rendering System (if on non-Windows)
 - CGProgramManager Plugin
-  **Boost**
 - Regex
 - Filesystem

----

You need to install [Cmake](http://www.cmake.org/cmake/resources/software.html "CMake"), as it's used to configure the build system.

Version 2.8+ is recommended, but 2.6 will probably do if you are not targeting Qt5.

Click a link below for specific instructions for your OS.

----

(wikilink: Windows title: Building Ogitor - Windows)
(wikilink: Linux title: Building Ogitor - Linux)
