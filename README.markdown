NEWS
====
2015-03-31
----------
**IT LIVES**

Since Amazon recently announced unlimited Cloud Drive storage for $60/year, I've gotten several notes asking me to revive this project. Who am I to deny the people what they want? :-)

I've made a couple of changes to the pyacd upstream module, both incorporating the latest version from upstream and adding a couple of tweaks myself to get things working with the new redesign of the login page. I also incorporated [ian1roberts'](https://github.com/ian1roberts) changes from [issue #2](https://github.com/handyman5/acd_fuse/issues/2); thanks for those!

At this point the driver appears to be working again (although my testing has been very limited). I'll try and make the time in the near future to make some more improvements, including perhaps refactoring some of the common code in the pyacd module and making a pass on `acd_fuse` itself to see if there's anything I've learned to do better in the intervening three years.

If things are still not working for you after this update, or if there are any other features you'd like to see, please open an [issue](https://github.com/handyman5/acd_fuse/issues) so I can take a look.

2012-03-01
----------
New feature: proper Unicode support!

I ran into trouble while uploading my MP3 collection, as some files have non-ASCII filenames. This update should properly support those filenames. Logging and error messages should also properly display Unicode data as well.

Realistically, I always find Unicode support to be kind of a crapshoot; if you find a filename that `acd_fuse` can't handle, please open an [issue](https://github.com/handyman5/acd_fuse/issues) with the name so I can try to replicate and fix the problem. Standard disclaimers about data-eating *definitely* apply.

2012-01-09
----------
Some big performance improvements to `acd_fuse` in this update. Specifically:

- The on-disk filecache now has a garbage collection routine that will prevent it from filling the entire filesystem it lives on. By default, it will kick on and start cleaning up files when that filesystem gets below 10% free space; you can change this behavior with the mount option `cachefree`
- The FUSE mount options `direct_io`, `big_writes`, and `large_read` are now enabled by default (and cannot, actually, be disabled). If I'm to be 100% honest, I'm not really sure what the full significance of each of these options is; but I have noticed significant speed improvements with them enabled.

2011-12-31
----------
I've made some significant updates to `acd_fuse`. Its functionality is expanded, and I now believe it to be ready for prime time.

Changes:

- On-disk file caching!
  - Transferred files are cached to a `tempfile.gettempdir()` path for faster re-uploads and re-downloads
  - File caches persist between filesystem invocations and detect when the remote file has changed
- Implemented debug logging to a file, rather than the console
  - Default log file is `/tmp/acd_fuse/debug.log`
- Automatically save and restore session information in a `tempfile.gettempdir()` path; no config option necessary
  - Default session file is `/tmp/acd_fuse/sessionfile`
- Session information now saved in JSON format, and no longer includes login name and password
- rsync now supported cleanly and without hacks, although you'll need to use `--inplace` to use the file caching feature (and `--size-only` avoids copying the files to the disk cache for a significant speedup if you don't expect another cache hit)
- Eight-device limit issue _may_ be resolved; this requires further testing
- Various performance improvements

Introduction
============
`acd_fuse` is a FUSE filesystem driver for Amazon's Cloud Drive. It uses the [PyAmazonCloudDrive API library](http://code.google.com/p/pyamazonclouddrive/). It supports pretty much everything Cloud Drive does, e.g. directory navigation and file uploading/downloading, but not file permissions. In particular, it supports using rsync to transfer files, and also Unicode filenames.

Standard Disclaimer
===================
This filesystem is **EXPERIMENTAL** and may **EAT YOUR BABIES** etc. I am not responsible for any loss or damage caused by its use.

ACTUAL THING YOU SHOULD WORRY ABOUT
===================================
Amazon Cloud Drive [limits you to eight devices](http://www.amazon.com/gp/help/customer/display.html/ref=hp_200143320_dlimits?nodeId=200656220#devicelimit) on your account, total, ever. It seems to be the case that loading up this filesystem counts against that limit (although possibly it doesn't start counting until you attempt to download a file). `acd_fuse` attempts to mitigate this by storing your login information (cookies and such) in a session file which is automatically populated and re-used between sessions. I think this problem is resolved, but I locked out my test account, so **BE CAREFUL** about using this with an account you care about!

Requirements
============
- Tested with Python 2.6 on Ubuntu 10.04; it might possibly work elsewhere. Let me know!
  - Works on Gentoo: `emerge fuse-python`
  - Works on Amazon (AMI) Linux: `yum install fuse fuse-python`
  - Works with Python 2.7 (but not 3.x yet)
- [Python-FUSE](http://sourceforge.net/apps/mediawiki/fuse/index.php?title=FUSE_Python_tutorial) and its dependencies (libfuse, kernel support, etc.)

Usage
=====
Standard FUSE mountpoint stuff:

    acd <mountpoint> -o<options>

Mount Options
-------------
- `email`: your Amazon Cloud Drive login email
- `password`: your Amazon Cloud Drive login password
- `cachefree`: the percentage of free space to maintain on the filecache's file system; *10* by default

License
=======
acd_fuse is released under a **MIT License**, in keeping with the library it uses for accessing Amazon Cloud Drive, [PyAmazonCloudDrive](http://code.google.com/p/pyamazonclouddrive/). That guy (whose name I believe to be Sakurai Youhei <http://d.hatena.ne.jp/sakurai_youhei/>), did all the hard work and deserves all the credit for writing a great API.

Contact
=======
You can email me at comptona@gmail.com.

To report bugs or request features, please use the **[Issues](https://github.com/handyman5/acd_fuse/issues)** feature.
