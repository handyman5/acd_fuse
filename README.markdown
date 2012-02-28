NEWS
====

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
- rsync now supported cleanly and without hacks, although you'll need to use `--inplace` to use the file caching feature
- Eight-device limit issue _may_ be resolved; this requires further testing
- Various performance improvements

Introduction
============
`acd_fuse` is a FUSE filesystem driver for Amazon's Cloud Drive. It uses the [PyAmazonCloudDrive API library](http://code.google.com/p/pyamazonclouddrive/). It supports pretty much everything Cloud Drive does, e.g. directory navigation and file uploading/downloading, but not file permissions. In particular, it supports using rsync to transfer files.

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
  - Works on Amazon (AMI) Linux: `yum install fuse-python`
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
