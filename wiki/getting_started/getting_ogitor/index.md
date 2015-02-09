<!--
title: Getting Ogitor
template: wiki
order: 2
-->
## Binary Releases
The current version of Ogitor is 0.5 and it has not been officially released yet, so there is no installers available for neither platform.

There is a preliminary development snapshot - which may or may not work - from our Bitbucket download area: (link: %snapshot_download% text: %snapshot_name%)

If you want to be sure, you need to grab the source code and build it yourself. :)

## Source Code

Ogitor is split into source code in a Mercurial repository and editor resources.

```bash
hg clone https://bitbucket.org/ogitor/ogitor
```

## Resources

Due to Source Control Changes for heavy binaries, `"RunPath/Media"` and `"RunPath/Projects"` are now excluded from repository.

Instead they are supplied as extra downloads from:

* (link: https://bitbucket.org/ogitor/ogitor/downloads/media.zip)
* (link: https://bitbucket.org/ogitor/ogitor/downloads/projects.zip)

Please download and extract them to your SOURCE path's "RunPath" directory in order to build and install Ogitor.

## Next Move

(wikilink: Building Ogitor)