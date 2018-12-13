**Forked master branch from https://github.com/wikimedia/mediawiki and git cloned from https://github.com/RXT067/mediawiki**

```
git clone git@github.com:RXT067/mediawiki.git
Cloning into 'mediawiki'...
The authenticity of host 'github.com (140.82.118.3)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,140.82.118.3' (RSA) to the list of known hosts.
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.
Please make sure you have the correct access rights
and the repository exists.
```

**Forgott root access using sudo:**

```
kreyren@dreamon ~ $ sudo git clone git@github.com:RXT067/mediawiki.git
Password: 
Cloning into 'mediawiki'...
Enter passphrase for key '/root/.ssh/id_rsa': 

remote: Enumerating objects: 354, done.
remote: Counting objects: 100% (354/354), done.
remote: Compressing objects: 100% (230/230), done.
remote: Total 751387 (delta 177), reused 220 (delta 124), pack-reused 751033
Receiving objects: 100% (751387/751387), 771.41 MiB | 14.52 MiB/s, done.
Resolving deltas: 100% (624604/624604), done.
```

**cd in mediawiki**

```
ls
api.php                     CREDITS       img_auth.php  maintenance          RELEASE-NOTES-1.32  thumb.php
autoload.php                docs          includes      mw-config            RELEASE-NOTES-1.33  UPGRADE
cache                       extensions    index.php     opensearch_desc.php  resources
CODE_OF_CONDUCT.md          FAQ           INSTALL       package.json         SECURITY
composer.json               Gruntfile.js  jsduck.json   profileinfo.php      skins
composer.local.json-sample  HISTORY       languages     README               tests
COPYING                     images        load.php      README.mediawiki     thumb_handler.php
```

**mediawiki/.git/config seems OK**

```
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
[remote "origin"]
        url = git@github.com:RXT067/mediawiki.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
        remote = origin
        merge = refs/heads/master
```
        

**![Mediawiki requires external dependancies](https://github.com/RXT067/mediawiki/blob/master/RXT067/media-wiki%20stuff.png)**

**Based on info from https://www.mediawiki.org/wiki/Download_from_Git#Fetch_external_libraries emerged composer from https://packages.gentoo.org/packages/dev-php/composer version 1.7.2**

**Invoked `composer update --no-dev` which results in:**

```
composer update --no-dev
Do not run Composer as root/super user! See https://getcomposer.org/root for details
> ComposerHookHandler::onPreUpdate
Loading composer repositories with package information
Updating dependencies
Your requirements could not be resolved to an installable set of packages.

  Problem 1
    - squizlabs/php_codesniffer 3.3.1 requires ext-xmlwriter * -> the requested PHP extension xmlwriter is missing from your system.
    - mediawiki/mediawiki-codesniffer v22.0.0 requires squizlabs/php_codesniffer 3.3.1 -> satisfiable by squizlabs/php_codesniffer[3.3.1].
    - Installation request for mediawiki/mediawiki-codesniffer 22.0.0 -> satisfiable by mediawiki/mediawiki-codesniffer[v22.0.0].

  To enable extensions, verify that they are enabled in your .ini files:
    - /etc/php/cli-php7.2/php.ini
    - /etc/php/cli-php7.2/ext-active/opcache.ini
  You can also run ``php --ini`` inside terminal to see which files are used by PHP in CLI mode.
Running update with --no-dev does not mean require-dev is ignored, it just means the packages will not be installed. If dev requirements are blocking the update you have to resolve those problems.                                
```

**Noticed composer warning**
```
Cannot create cache directory /home/kreyren/.cache/composer/repo/https---repo.packagist.org/, or directory is not writable. Proceeding without cache
Cannot create cache directory /home/kreyren/.cache/composer/files/, or directory is not writable. Proceeding without cache
```

Resolved by invoking:
```
kreyren@dreamon ~/mediawiki $ sudo chown -R kreyren /home/kreyren/.cache/composer/repo/https---repo.packagist.org/

kreyren@dreamon ~/mediawiki $ sudo chown -R kreyren /home/kreyren/.cache/composer/files/
```



Noted that composer is not meant to run as root.

**Checking `squizlabs/php_codesniffer 3.3.1 requires ext-xmlwriter`**

* Found https://packagist.org/packages/squizlabs/php_codesniffer == Nothing relevant
* Found https://github.com/docksal/ci-agent/issues/6 == Solution `Install php7-xmlwriter in the php pipeline container`, thanks aczietlow.
   * Found xmlwriter useflag for dev-lang/php in gentoo repo results in:
    ```
    composer update --no-dev
    Cannot create cache directory /home/kreyren/.cache/composer/repo/https---repo.packagist.org/, or directory is not writable. Proceeding without cache
    Cannot create cache directory /home/kreyren/.cache/composer/files/, or directory is not writable. Proceeding without cache
    > ComposerHookHandler::onPreUpdate
    Loading composer repositories with package information
    Updating dependencies
    Package operations: 34 installs, 0 updates, 0 removals
    
                                                                               
  [RuntimeException]                                                      
  /home/kreyren/mediawiki/vendor does not exist and could not be created.  
                                                                           
   
    update [--prefer-source] [--prefer-dist] [--dry-run] [--dev] [--no-dev] [--lock] [--no-custom-installers] [--no-autoloader] [--no-scripts] [--no-progress] [--no-suggest] [--with-dependencies] [--with-all-dependencies] [-v|vv|vvv|--verbose] [-o|--optimize-autoloader] [-a|--classmap-authoritative] [--apcu-autoloader] [--ignore-platform-reqs] [--prefer-stable] [--prefer-lowest] [-i|--interactive] [--root-reqs] [--] [<packages>]...    
    ```
   * checking vendor
   * based on https://github.com/panique/huge/issues/392 invoked `sudo chmod -R 777 /home/kreyren/mediawiki` which fixed the issue.

**Updating wikimedia using composer again**

```
composer update --no-dev
> ComposerHookHandler::onPreUpdate
Loading composer repositories with package information
Updating dependencies
Package operations: 34 installs, 0 updates, 0 removals
  - Installing wikimedia/composer-merge-plugin (v1.4.1): Downloading (100%)         
  - Installing cssjanus/cssjanus (v1.2.0): Downloading (100%)         
  - Installing liuggio/statsd-php-client (v1.0.18): Downloading (100%)         
  - Installing oojs/oojs-ui (v0.29.5): Downloading (100%)         
  - Installing oyejorge/less.php (v1.7.0.14): Downloading (100%)         
  - Installing pear/pear_exception (v1.0.0): Downloading (100%)         
  - Installing pear/console_getopt (v1.4.1): Downloading (100%)         
  - Installing pear/pear-core-minimal (v1.10.6): Downloading (100%)         
  - Installing pear/mail (v1.4.1): Downloading (100%)         
  - Installing pear/mail_mime (1.10.2): Downloading (100%)         
  - Installing pear/net_socket (v1.2.2): Downloading (100%)         
  - Installing pear/net_smtp (1.8.0): Downloading (100%)         
  - Installing wikimedia/assert (v0.2.2): Downloading (100%)         
  - Installing wikimedia/base-convert (v2.0.0): Downloading (100%)         
  - Installing wikimedia/cdb (1.4.1): Downloading (100%)         
  - Installing wikimedia/cldr-plural-rule-parser (v1.0.0): Downloading (100%)         
  - Installing wikimedia/html-formatter (1.0.2): Downloading (100%)         
  - Installing wikimedia/ip-set (1.2.0): Downloading (100%)         
  - Installing wikimedia/object-factory (1.0.0): Downloading (100%)         
  - Installing psr/log (1.0.2): Downloading (100%)         
  - Installing wikimedia/at-ease (v1.2.0): Downloading (100%)         
  - Installing wikimedia/php-session-serializer (v1.0.6): Downloading (100%)         
  - Installing wikimedia/purtle (v1.0.7): Downloading (100%)         
  - Installing wikimedia/relpath (2.1.1): Downloading (100%)         
  - Installing wikimedia/utfnormal (v2.0.0): Downloading (100%)         
  - Installing wikimedia/remex-html (2.0.1): Downloading (100%)         
  - Installing wikimedia/running-stat (v1.2.1): Downloading (100%)         
  - Installing wikimedia/wait-condition-loop (v1.0.1): Downloading (100%)         
  - Installing wikimedia/wrappedstring (v3.0.1): Downloading (100%)         
  - Installing wikimedia/timestamp (v2.2.0): Downloading (100%)         
  - Installing wikimedia/scoped-callback (v3.0.0): Downloading (100%)         
  - Installing wikimedia/xmp-reader (0.6.1): Downloading (100%)         
  - Installing zordius/lightncandy (v0.23): Downloading (100%)         
  - Installing composer/semver (1.4.2): Downloading (100%)         
Writing lock file
Generating optimized autoload files
> ComposerHookHandler::onPreUpdate
Loading composer repositories with package information
Updating dependencies
Generating optimized autoload files
> ComposerVendorHtaccessCreator::onEvent
> ComposerVendorHtaccessCreator::onEvent
```

**Submitted Pull Request on https://github.com/wikimedia/mediawiki/pull/94 to master**


**Errors, Errors, Errors..**
![Mediawiki requires external dependancies](https://raw.githubusercontent.com/RXT067/mediawiki/master/RXT067/mediawiki_something.png)

* Checking for sources on mediawiki.org

* Found https://www.mediawiki.org/wiki/Topic:Qa8kvcyjpxsm1s62 == Seems relevant
```
$ locate "LocalSettings.php"
/home/kreyren/.local/share/Trash/files/mediawiki-1.31.1/extensions/MultimediaViewer/tests/browser/LocalSettings.php
/home/kreyren/.local/share/Trash/files/mediawiki-1.31.1/includes/NoLocalSettings.php
/home/kreyren/Desktop/mediawiki-master/includes/NoLocalSettings.php
/home/kreyren/mediawiki/includes/NoLocalSettings.php
```
Those are old installation in Trash, LocalSettings.php did NOT come with the git from the master.

profileinfo.php line 10 possible typo? LocalSetings.php != LocalSettings.php.

includes/DefaultSettings.php line 6377 possible typo? LocalSetings.php != LocalSettings.php.

Based on https://github.com/wikimedia/mediawiki/search?q=LocalSettings.php&unscoped_q=LocalSettings.php LocalSettings.php is not present in master branch.
EDIT: it has 24 pages, filtering by php 42 results ware found (https://github.com/wikimedia/mediawiki/search?l=PHP&q=LocalSettings.php), Based on provided info LocalSettings.php is not present in masterbranch nor REL_31.

Created /tmp and /session in /data resolved the fatal issues.

commenting `putenv` in setup.php also removes the warning. putenv is disabled by hosting and doesn't seem to affect the wiki.

```
Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/includes/libs/HttpStatus.php on line 112

Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/includes/WebResponse.php on line 46

Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/includes/WebResponse.php on line 46

Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/includes/WebResponse.php on line 46

Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/includes/WebResponse.php on line 46

Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/includes/WebResponse.php on line 46

Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki-stable/includes/WebResponse.php on line 46
```

New errors! Trying to make stable work first since master is PITA.

meh fuck it lets just parse LocalSettings from stable to master and see what happends.

baaadd.. idea 
```
Fatal error: Uncaught Exception: /data/web/virtuals/161195/virtual/www/subdom/wiki/skins/MonoBook/skin.json does not exist! in /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/registration/ExtensionRegistry.php:107 Stack trace: #0 /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/GlobalFunctions.php(87): ExtensionRegistry->queue('/data/web/virtu...') #1 /data/web/virtuals/161195/virtual/www/subdom/wiki/LocalSettings.php(126): wfLoadSkin('MonoBook') #2 /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/Setup.php(103): require_once('/data/web/virtu...') #3 /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/WebStart.php(77): require_once('/data/web/virtu...') #4 /data/web/virtuals/161195/virtual/www/subdom/wiki/index.php(39): require('/data/web/virtu...') #5 {main} thrown in /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/registration/ExtensionRegistry.php on line 107
```

meh

### New installation
Fixed issues above using REL_32, everything seems to work excluding installation:
https://i.imgur.com/6ZkaTFM.png

SOLUTION: Used wrong account for database.


**NEW PROBLEM**

```
Warning: putenv() has been disabled for security reasons in /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/Setup.php on line 132

Warning: putenv() has been disabled for security reasons in /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/Setup.php on line 132

Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/libs/HttpStatus.php on line 112

Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/WebResponse.php on line 72

Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/WebResponse.php on line 72

Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/WebResponse.php on line 72

Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/WebResponse.php on line 72

Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/WebResponse.php on line 72

Warning: Cannot modify header information - headers already sent by (output started at /data/web/virtuals/161195/virtual/www/subdom/wiki/LocalSettings.php:1) in /data/web/virtuals/161195/virtual/www/subdom/wiki/includes/WebResponse.php on line 72
```

There was 

```
<br />
<b>Warning</b>:  putenv() has been disabled for security reasons in <b>/data/web/virtuals/161195/virtual/www/subdom/wiki/includes/Setup.php</b> on line <b>132</b><br />
<php?
```

Removing everything about php flag solved the issue.
