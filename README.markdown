Introduction
============
acd_fuse is a FUSE filesystem driver for Amazon's Cloud Drive. It uses the [PyAmazonCloudDrive API library](http://code.google.com/p/pyamazonclouddrive/). It supports pretty much everything Cloud Drive does, e.g. directory navigation and file uploading/downloading, but not file permissions. In particular, it supports using rsync to transfer files if the --size-only flag is provided (since you can't change mtimes on Cloud Drive).

Standard Disclaimer
===================
This filesystem is **EXPERIMENTAL** and may **EAT YOUR BABIES** etc. I am not responsible for any loss or damage caused by its use.

ACTUAL THING YOU SHOULD WORRY ABOUT
===================================
Amazon Cloud Drive [limits you to eight devices](http://www.amazon.com/gp/help/customer/display.html/ref=hp_200143320_dlimits?nodeId=200656220#devicelimit) on your account, total, ever. It seems to be the case that loading up this filesystem counts against that limit (although possibly it doesn't start counting until you attempt to download a file). This is what the `sessionfile` mount option is for; if the sessionfile is provided, acd_fuse will store a Python pickle of its session object there and re-use it in the future. I'm not sure about how the interaction works, but I locked out my test account, so **BE CAREFUL** about using this with an account you care about! Also please note, the login name and password are stored **unencrypted** in the session file, so keep it safe.

Requirements
============
- Tested with Python 2.6 on Ubuntu 10.04; it might possibly work elsewhere. Let me know!
- [Python-FUSE](http://sourceforge.net/apps/mediawiki/fuse/index.php?title=FUSE_Python_tutorial) and its dependencies (libfuse, kernel support, etc.)

Usage
=====
Standard FUSE mountpoint stuff:

    acd <mountpoint> -o<options>

Mount Options
-------------
- email: your Amazon Cloud Drive login email
- password: your Amazon Cloud Drive login password
- sessionfile: the full path of a file to store the session cookie in
- cache: whether to cache downloaded files in memory, so "re-downloading" them is instantaneous. Downside: uses way more memory.

License
=======
acd_fuse is released under a **MIT License**, in keeping with the library it uses for accessing Amazon Cloud Drive, [PyAmazonCloudDrive](http://code.google.com/p/pyamazonclouddrive/). That guy (whose name I believe to be Sakurai Youhei <http://d.hatena.ne.jp/sakurai_youhei/>, did all the hard work and deserves all the credit for writing a great API.

Contact
=======
You can email me at comptona@gmail.com.

To report bugs or request features, please use the **[Issues](https://github.com/acd_fuse/poodledo/issues)** feature.