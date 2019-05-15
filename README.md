# [Apache Server Configs](https://github.com/h5bp/server-configs-apache/)

[![Build Status](https://travis-ci.org/h5bp/server-configs-apache.svg?branch=master)](https://travis-ci.org/h5bp/server-configs-apache)

**Apache Server Configs** is a collection of configuration snippets that can help
your server improve the web site's performance and security, while also
ensuring that resources are served with the correct content-type and are
accessible, if needed, even cross-domain.


## Getting Started

There are a few options for getting the Apache server configs.

See also the [Apache Getting Started](https://httpd.apache.org/docs/current/getting-started.html).

### Using the httpd configuration

If you have access to the [main server configuration
file](https://httpd.apache.org/docs/current/configuring.html#main)
(usually called `httpd.conf`), you should configure Apache by this way.  
This is usually the recommended way, as using `.htaccess` files [slows
down](https://httpd.apache.org/docs/current/howto/htaccess.html#when)
Apache!  

Getting options:
* Download the [zip archive](https://github.com/h5bp/server-configs-apache/archive/3.2.1.zip)
* Checkout directly:
  ```shell
  apache2ctl stop
  cd /usr/local
  mv apache2 apache2-previous
  git clone https://github.com/h5bp/server-configs-apache.git apache2
  # install-specific edits
  apache2ctl start
  ```

See [Apache httpd configuration usage guide](#Apache-httpd-configuration).

### Using the `.htaccess` file

If you don't have access, which is quite common with hosting services,
just copy the `.htaccess` file in the root of the website.

Getting options:
* Download the `h5bp.htaccess` on the [latest release](https://github.com/h5bp/server-configs-apache/releases/latest)
* Install them via [npm](https://www.npmjs.com/): `npm install --save-dev apache-server-configs`  
  Inside the `dist/` folder, you'll find a ready-to-use `.htaccess` file.


## Usage

### Basic structure

This repository has the following structure:

```
./
├── vhosts/
│   ├── 000-default.conf
│	└── templates/
├── h5bp/
│   ├── basic.conf
│   └── .../
└── nginx.conf
```

* **`vhosts/`**

  This directory should contain all of the server definitions.
  
  Except if they are dot prefixed or non .conf extension, all files in this
  folder **are** loaded automatically.

  * **`templates` folder**

    Files in this folder contain a `<VirtualHost/>` template for secure and non-secure hosts.
    They are intended to be copied in the `vhosts` folder with all `example.com` 
    occurrences changed to the target host.

* **`h5bp/`**

  This directory contains config snippets (mixins) to be included as desired.
  
  There are two types of config files provided, individual config snippets and
  combined config files which provide convenient defaults.

  * **`basic.conf`**
  
    This file loads a small subset of the rules provided by this repository to add
    expires headers, allow cross domain fonts and protect system files from web
    access.
    The `basic.conf` file includes the rules which are recommended to always be
    defined.

* **`httpd.conf`**

  The main Apache config file.


### Enable Apache httpd modules

Some configurations won't have any effect if the
appropriate modules aren't enabled. So, in order for everything
to work as intended, you need to ensure the you have the following
Apache modules enabled:

* [`mod_autoindex.c` (autoindex_module)](https://httpd.apache.org/docs/current/mod/mod_autoindex.html)
* [`mod_deflate.c` (deflate_module)](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
* [`mod_expires.c` (expires_module)](https://httpd.apache.org/docs/current/mod/mod_expires.html)
* [`mod_filter.c` (filter_module)](https://httpd.apache.org/docs/current/mod/mod_filter.html)
* [`mod_headers.c` (headers_module)](https://httpd.apache.org/docs/current/mod/mod_headers.html)
* [`mod_include.c` (include_module)](https://httpd.apache.org/docs/current/mod/mod_include.html)
* [`mod_mime.c` (mime_module)](https://httpd.apache.org/docs/current/mod/mod_mime.html)
* [`mod_rewrite.c` (rewrite_module)](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)
* [`mod_setenvif.c` (setenvif_module)](https://httpd.apache.org/docs/current/mod/mod_setenvif.html)

For more detailed information on configuration files and how to
use them, please check the appropriate Apache documentation:

* <https://httpd.apache.org/docs/current/configuring.html>
* <https://httpd.apache.org/docs/current/howto/htaccess.html>

#### Standalone

These instructions should work on any distribution where `apt-get` has been
used to install Apache.

1. Open up a terminal and type the following command. Enter your password when
   prompted.

    `sudo a2enmod setenvif headers deflate filter expires rewrite include`

2. Restart apache by using the following command so the new configuration takes
   effect.

    `sudo /etc/init.d/apache2 restart`

#### With MAMP/WAMP/XAMPP

* **MAMP PRO**. On the main screen, click the `Apache` tab and ensure that all
   the required modules are 'checked', indicating they are enabled.

* **WampServer**. If you have installed WampServer just click on the icon in the
   task bar then <kbd>Apache</kbd> section then <kbd>modules</kbd> section.
   You will be presented with a list of modules. Simply click on a module name
   to enable it. WampServer will automatically restart the Apache service after
   you enable a module.

* **Others**. Locate the `httpd.conf` file, which is typically found in:
  * **MAMP**: `/Applications/MAMP/conf/apache/httpd.conf`
  * **XAMPP**: `/Applications/XAMPP/etc/httpd.conf`
  * **WAMP**: `C:\apache\conf\httpd.conf`

  Open the file in a text editor and uncomment all of the required modules.
  Once you have done so, reset MAMP/WAMP/XAMPP.
  
### Apache httpd configuration

#### Check `httpd.conf` settings

The first thing to check is that the `httpd.conf` file contains appropriate values for
your specific install. 

Most specific variables are:
* `ServerRoot`
* `User`
* `Group`
* `ErrorLog`
* `CustomLog`

#### Apache test and restart

* To verify Apache config
  ```shell
  $ apache2 -t 
  ```

* To verify Apache config with a custom file
  ```shell
  $ apache2 -t -f httpd.conf
  ```

* To reload Apache and apply new config
  ```shell
  $ apache2ctl reload 
  ```

#### Manage sites

```bash
$ cd /usr/local/apache2/vhosts
```

* Creating a new site
  ```bash
  $ cp templates/example.com.conf .actual-hostname.conf
  $ sed -i 's/example.com/actual-hostname/g' .actual-hostname.conf
  ```

* Enabling a site
  ```bash
  $ mv .actual-hostname.conf actual-hostname.conf
  ```
	
* Disabling a site
  ```bash
  $ mv actual-hostname.conf .actual-hostname.conf
  ```

```bash
$ apache2ctl reload
```


## Custom `.htaccess` builds

Security, mime-type, and caching best practices evolve, and so should do your `.htaccess` file. In the past, with each new *Apache Server Configs* release it was quite tedious to find out which `.htaccess` trick was just new or only had changes in certain nuances.

The [**build script**](#build-script-buildsh) with its re-usable and customizable [**build configuration**](#configuration-file-htaccessconf) lets you easily update your `.htaccess` file. Each new `.htaccess` build will contain the updated *Apache Server Configs* source files, enabled or commented-out according to your settings in the *htaccess.conf* of your project root.

### Configuration file: *htaccess.conf*

Allows you to define which module to [enable](#enabling-modules) or [disable](#disabling-modules) for your project. Just copy the default [**htaccess.conf**](https://github.com/h5bp/server-configs-apache/blob/master/bin/htaccess.conf) from this repo into your project directory. Adjust to your needs, and/or [add custom code](#adding-custom-modules) snippets you need for your project. Its syntax is straight and pretty much self-explanatory:

```
# Example Module

title   "example module"
enable  "src/example-module/images.conf"
enable  "src/example-module/web_fonts.conf"
disable "src/example-module/not-needed.conf"
omit    "src/example-module/not-needed-at-all.conf"

... more modules ...
```

#### Disabling modules

For example, the *“Cross-origin web fonts”* snippet is always included in our pre-built `.htaccess` file and enabled. If your project does not deal with web fonts, you can **disable** or **omit** this section:

This will comment out the section:

```
disable  "src/cross-origin/web_fonts.conf"
```

…and this will exclude the section, saving lines in output:

```
omit  "src/cross-origin/web_fonts.conf"
```

#### Enabling modules

For example, the *“Forcing https://”* snippet is disabled by default, although being included in our pre-built `.htaccess`. To enable this snippet, change the **disable** keyword to **enable:**

```
enable "src/rewrites/rewrite_http_to_https.conf"
```

#### Adding custom modules

Imagine you're passing all requests to non-existing files to your favourite web framework. The according
[_mod_dir_](https://httpd.apache.org/docs/trunk/mod/mod_dir.html#fallbackresource) snippet would go like this:

```
FallbackResource index.php
```

Store this snippet in a file, e.g. **config/framework_rewrites.conf,** and add a reference in your **htaccess.conf:**

```
# PROJECT MODULES
enable "config/framework_rewrites.conf"
```

### Build script: *build.sh*

Dive into your project root and call the build script from wherever you cloned the repo. Here are three examples:

**1. Create a default .htaccess**  
in current work directory. An existing **htaccess.conf** in this directory will be used; if none is present, the [**default configuration**](https://github.com/h5bp/server-configs-apache/blob/master/bin/htaccess.conf) will apply.


```bash
$ path/to/server-configs-apache/bin/build.sh

# Output looks like:
[✔] Build .htaccess
[✔] Moved in place: './.htaccess'
```

**2. Custom output location**  
Just add output path and filename as parameter. By the way, if there's an existing *.htaccess* file, the build script will create a backup.

```bash
$ path/to/server-configs-apache/bin/build.sh htdocs/.htaccess
[✔] Build .htaccess
[✔] Create backup: 'htdocs/.htaccess~'
[✔] Moved in place: 'htdocs/.htaccess'
```

**3. Custom .htaccess configuration**  
Why not maintain your personal **~/htaccess.conf?** This example creates a *.htaccess* in current work directory, according to your favourite settings you may have stored in your `$HOME` directory:

```bash
$ path/to/server-configs-apache/bin/build.sh ./.htaccess ~/htaccess.conf
```


## Support

* Apache v**2.4.0**+


## Contributing

Anyone is welcome to [contribute](.github/CONTRIBUTING.md),
however, if you decide to get involved, please take a moment to review
the [guidelines](.github/CONTRIBUTING.md):

* [Bug reports](.github/CONTRIBUTING.md#bugs)
* [Feature requests](.github/CONTRIBUTING.md#features)
* [Pull requests](.github/CONTRIBUTING.md#pull-requests)


## Acknowledgements

[Apache Server Configs](https://github.com/h5bp/server-configs-apache/) is only possible thanks to all the awesome
[contributors](https://github.com/h5bp/server-configs-apache/graphs/contributors)!


## License

The code is available under the [MIT license](LICENSE.txt).
