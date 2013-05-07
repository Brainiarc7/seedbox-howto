Setting up a seedbox
======

What is a seedbox?
----
A seedbox is a computer running a BitTorrent client in a continous or near-continous capacity. Rather than running a Bit Torrent client on your personal desktop or laptop, this computer is used to download and upload content via BitTorrent. This allows transfers to continue uninterrupted. The Bit Torrent client presents a web-based user interface rather than the traditional desktop-environment. This allows BitTorrent transfers to be stopped and started remotely. In addition to the BitTorrent client software, server software is loaded to allow users to retrieve files after BitTorrent transfers complete. The specific software used for file retrieval depends on the users needs.

What is this document?
-----
This document is a guide to configuring an existing Ubuntu 12.04 LTS installation as seedbox. When followed, this document will assist the reader in setting up a seedbox with [rtorrent](http://libtorrent.rakshasa.no/) as the backend and [rutorrent](https://code.google.com/p/rutorrent/) as the user interface. This document presumes the reader has a basic working knowledge of the bash command line environment. Basic network administration tasks such as port-forwarding are also needed if the seedbox is to reside behind a network firewall.

This document is also useful for other GNU-Linux based operating systems, but differences do exist between one distribution to the next. It is up to the reader to identify such differences and deviate where needed.

How to use this document?
----
Follow the main setup. Once that is complete, follow any number of the optional setup sections.

Indemnification
---
By using this document you agree to indemnify the author against any and all lost or damages incurred now and until the end of time.


Compile Software From Source
====
rTorrent is the BitTorrent client of choice for seedboxes. Although precompiled packages exist, this guide includes instructions on how to build them from source. This guide makes use of the latest stable release. At the time of writing that is 0.13.2 for libtorrent and 0.9.2 for rtorrent

Blacklist packages from repository
---

Download
---
* [libtorrent source](http://libtorrent.rakshasa.no/downloads/libtorrent-0.13.2.tar.gz)
* [rtorrent source](http://libtorrent.rakshasa.no/downloads/rtorrent-0.9.3.tar.gz)

Compile libtorrent
----
###Satisfy Dependencies

###Decompress and untar

###Configure and compile

###Install

Compile rtorrent
---
###Satisfy Dependencies

###Decompress and untar

###Configure and compile

###Install

Setup lighttpd
====

**Note:** If the machine being configured is already used for other purposes then it is likely that lighttpd or another webserver is already installed. Following these instructions verbatim may result in the existing configuration becoming useless or in conflict with this configuration.

Install from repositories
----
To install lighttpd and other needed packages from the repositories:

	sudo apt-get install lightpdd php5-cgi
	
Enable authentication
----
Edit the default configuration and add the following to the end of the file:

	auth.backend = "plain"
	auth.backend.plain.userfile ="/etc/lighttpd/lighttpd.passwd"
	auth.debug = 0
	auth.require = ("/rutorrent/" => ( "method" => "basic", "realm" => "rutorrent", "require" => "user=rutorrent"))
	
Next, create the file used by lighttpd for authentication:

	echo "rutorrent:`apg -n 1 -m 20 -x 20`" > /etc/lighttpd/lighttpd.passwd
	chmod 740 /etc/lighttpd/lighttpd.passwd
	chown root:www-data lighttpd.passwd

Restart lighttpd so the new configuration takes effect:
	
	service lighttpd restart
	
To view the password created:
	
	cat /etc/lighttpd/lighttpd.passwd
		
The username is 'rutorrent' and the password is everything after the colon. This is the username and password that will be used to login into the web interface. In the configuration presented this password is passed in plaintext in each and every request. This serves only to stop the least determined of malicious users. If you are using this seedbox over the internet, consider setting up [HTTPS](https.md).

Setup ruTorrent
====
ruTorrent is PHP based software which does not need to becompiled. The latest source code is obtained and decompressed into the 'www' folder. The most recent version at the time of writing is ruTorrent 3.5

Download
---
* [rutorrent core](https://rutorrent.googlecode.com/files/rutorrent-3.5.tar.gz)
* [rutorrent plugins](https://rutorrent.googlecode.com/files/plugins-3.5.tar.gz)

Decompress and untar
---
To decompress and untar rutorrent the correct location

	cd /var/www && tar --ungzip -x -f rutorrent-3.5.tar.gz 
	cd /var/www/rutorrent && tar --ungzip -x -f plugins-3.5.tar.gz
	chown -R www-data:www-data  /var/www/rutorrent

Setup rTorrent
=====

Create a user just for rtorrent
---
As a security consideration, rtorrent is ran as a separate user. To generate a password for this user:

	apg -n 1 -m 20 -x 20
	
Record this password in a safe place.

Create the new user as a member of the www-data group:

	adduser --ingroup www-data rtorrent
	
When prompted, enter the password created in the previous step.







