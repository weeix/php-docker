# Quick reference

-	**Maintained by**:  
	[Wee Sritippho](https://github.com/weeix/php-docker)

-	**Where to get help**:  
	[Server Fault](https://serverfault.com/help/on-topic), [Unix & Linux](https://unix.stackexchange.com/help/on-topic), or [Stack Overflow](https://stackoverflow.com/help/on-topic)

# Tags and respective `Dockerfile` links

-	[`5.2.6-apache`](https://github.com/weeix/php-docker/blob/8c4673ab9d84981360c42911b34d7eb3557cacbb/php-apache/5.2.6/Dockerfile)

# Quick reference (cont.)

-	**Supported architectures**: 
	`amd64`

# What is PHP?

PHP is a server-side scripting language designed for web development, but which can also be used as a general-purpose programming language. PHP can be added to straight HTML or it can be used with a variety of templating engines and web frameworks. PHP code is usually processed by an interpreter, which is either implemented as a native module on the web-server or as a common gateway interface (CGI).

> [wikipedia.org/wiki/PHP](https://en.wikipedia.org/wiki/PHP)

![logo](https://raw.githubusercontent.com/docker-library/docs/01c12653951b2fe592c1f93a13b4e289ada0e3a1/php/logo.png)

# How to use this image

### Create a `Dockerfile` in your PHP project

```dockerfile
FROM weeix/php:5.2.6-apache
COPY src/ /usr/local/apache2/htdoc/
```

Then, run the commands to build and run the Docker image:

```console
$ docker build -t my-php-app .
$ docker run -d -p 80:80 --name my-running-app my-php-app
```

### Default extensions

Some extensions are compiled by default. This depends on the PHP version you are using. Run `php -m` in the container to get a list for your specific version.

# Image Variants

The `php` images come in many flavors, each designed for a specific use case.

Some of these tags may have names like bookworm or bullseye in them. These are the suite code names for releases of [Debian](https://wiki.debian.org/DebianReleases) and indicate which release the image is based on. If your image needs to install any additional packages beyond what comes with the image, you'll likely want to specify one of these explicitly to minimize breakage when there are new releases of Debian.

## `php:<version>-cli`

This variant contains the [PHP CLI](https://secure.php.net/manual/en/features.commandline.php) tool with default mods. If you need a web server, this is probably not the image you are looking for. It is designed to be used both as a throw away container (mount your source code and start the container to start your app), as well as a base from which to build other images.

It also is the only variant which contains the (not recommended) `php-cgi` binary, which is likely necessary for some things like [PPM](https://github.com/php-pm/php-pm).

Note that *all* variants of `php` contain the PHP CLI (`/usr/local/bin/php`).

## `php:<version>-apache`

This image contains Debian's Apache httpd in conjunction with PHP (as `mod_php`) and uses `mpm_prefork` by default.

### Apache with a `Dockerfile`

```dockerfile
FROM weeix/php:5.2.6-apache
COPY src/ /usr/local/apache2/htdoc/
```

Where `src/` is the directory containing all your PHP code. Then, run the commands to build and run the Docker image:

```console
$ docker build -t my-php-app .
$ docker run -d -p 80:80 --name my-running-app my-php-app
```

We recommend that you add a `php.ini` configuration file; see the "Configuration" section for details.

### Apache without a `Dockerfile`

```console
$ docker run -d -p 80:80 --name my-apache-php-app -v "$PWD":/usr/local/apache2/htdoc weeix/php:5.2.6-apache
```

## `php:<version>-fpm`

This variant contains PHP-FPM, which is a FastCGI implementation for PHP. See [the PHP-FPM website](https://php-fpm.org/) for more information about PHP-FPM.

In order to use this image variant, some kind of reverse proxy (such as NGINX, Apache, or other tool which speaks the FastCGI protocol) will be required.

Some potentially helpful resources:

-	[PHP-FPM.org](https://php-fpm.org/)
-	[simplified example by @md5](https://gist.github.com/md5/d9206eacb5a0ff5d6be0)
-	[very detailed article by Pascal Landau](https://www.pascallandau.com/blog/php-php-fpm-and-nginx-on-docker-in-windows-10/)
-	[Stack Overflow discussion](https://stackoverflow.com/q/29905953/433558)
-	[Apache httpd Wiki example](https://wiki.apache.org/httpd/PHPFPMWordpress)

**WARNING:** the FastCGI protocol is inherently trusting, and thus *extremely* insecure to expose outside of a private container network -- unless you know *exactly* what you are doing (and are willing to accept the extreme risk), do not use Docker's `--publish` (`-p`) flag with this image variant.

## `php:<version>-alpine`

This image is based on the popular [Alpine Linux project](https://alpinelinux.org), available in [the `alpine` official image](https://hub.docker.com/_/alpine). Alpine Linux is much smaller than most distribution base images (~5MB), and thus leads to much slimmer images in general.

This variant is useful when final image size being as small as possible is your primary concern. The main caveat to note is that it does use [musl libc](https://musl.libc.org) instead of [glibc and friends](https://www.etalabs.net/compare_libcs.html), so software will often run into issues depending on the depth of their libc requirements/assumptions. See [this Hacker News comment thread](https://news.ycombinator.com/item?id=10782897) for more discussion of the issues that might arise and some pro/con comparisons of using Alpine-based images.

To minimize image size, it's uncommon for additional related tools (such as `git` or `bash`) to be included in Alpine-based images. Using this image as a base, add the things you need in your own Dockerfile (see the [`alpine` image description](https://hub.docker.com/_/alpine/) for examples of how to install packages if you are unfamiliar).

# License

View [license information](http://php.net/license/) for the software contained in this image.

As with all Docker images, these likely also contain other software which may be under other licenses (such as Bash, etc from the base distribution, along with any direct or indirect dependencies of the primary software being contained).

As for any pre-built image usage, it is the image user's responsibility to ensure that any use of this image complies with any relevant licenses for all software contained within.