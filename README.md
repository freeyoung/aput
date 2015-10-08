aput
====

`aput` is a package upload tool for debian repository managed by [aptly](http://www.aptly.info).

howto
=====

This howto assumes that you are starting from scratch.

If you are an experienced user, just pick what you need.

- Install Aptly

  Follow the instructions [here](http://www.aptly.info/download/).

- Install nginx package

  ```
  $ sudo apt-get install nginx
  ```

- Add login shell for `www-data` (the user nginx runs as)

  ```
  $ sudo chsh -s /bin/bash www-data
  ```

  Then you are able to switch to `www-data` with bash shell.

  ```
  $ sudo su - www-data
  ```

- Configure GPG for `www-data`

  Follow the manual of GnuPG [here](https://www.gnupg.org/gph/en/manual/c14.html).

- Create your aptly repo

  ```
  $ aptly repo create myrepo  # change "myrepo" to your own repo name
  ```

- Create your config file for `aput`

  ```
  $ cat ~/.aput.cfg

  APUT_HOST=deb.yourdomain.com
  APUT_SSH_USER=username
  APUT_SSH_PORT=22
  APUT_REPO_OWNER=www-data
  APUT_REPO_NAME=myrepo
  APUT_REPO_DIST=trusty
  APUT_REPO_ARCH=i386,amd64,all,source
  ```

- Add a new nginx config file for the repo

  ```
  $ cat /etc/nginx/conf.d/repo.conf

  server {
    listen 80;
    server_name deb.yourdomain.com;

    root /var/www/.aptly/public;
    autoindex on;

    location = /robots.txt {
        return 200 "User-agent: *\nDisallow: /\n";
    }
  }
  ```

  Remember to reload your nginx server.

- Symlink `aput` to your PATH

  ```
  $ git clone https://github.com/freeyoung/aput
  $ cd aput
  $ sudo ln -sf aput /usr/local/bin/aput
  ```

- Build and upload your package

  ```
  $ dpkg-buidpackage -us -uc -sa -rfakeroot
  ...
  ...
  $ aput your_package_0.0.1_version_arch.changes
  ```

  Enjoy!
