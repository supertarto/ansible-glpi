# Ansible GLPI
[![CI](https://github.com/supertarto/ansible-glpi/actions/workflows/ci.yml/badge.svg)](https://github.com/supertarto/ansible-glpi/actions/workflows/ci.yml)

An Ansible Role to install and configure GLPI. You must have a Web Server, php and MariaDB on your server. This role is tested only with Apache.

## Requirements

The prerequisites recommanded on the official GLPI docummentation are:

* A web server (Apache, Nginx, IIS, etc.)
* MariaDB >= 10.2 or MySQL >= 5.7
* PHP 7.4 or higher
* PHP extensions (mandatory):
    * ctype
    * curl
    * gd (picture generation)
    * iconv
    * intl
    * json
    * mbstring
    * mysqli
    * session
    * simplexml
    * zlib
* PHP extensions (optionnal)
    * exif (security enhancement on images validation)
    * imap (mail collector and users authentication)
    * ldap (users authentication)
    * openssl (encrypted communication)
    * sodium (performances enhancement on sensitive data encryption/decryption)
    * zip and bz2 (installation of zip and bz2 packages from marketplace)

You can use supertarto.apache, supertarto.mariadb and supertarto.php to install those beforehand with ansible

## Tested plateform
* Debian 11 (Bullseye)
* Debian 12 (Bookworm)


## Role variables
The GLPI version and the package name.
```yml
glpi_version: 10.0.16
glpi_version_package: glpi-10.0.16.tgz
```
Set to "true" if you want to perform the final installation automatically. It configure the database for the first time. Else, you'll have to end it with your web browser. Don't use it when you update and your database is already configured. The task will crash, cause it's not meant for update.
```yml
glpi_auto_install: false
```
Set glpi_update to "true" if you want to force an reinstallation. The URL use the glpi_version an the package name.
```yml
glpi_update: false
glpi_download_url: "https://github.com/glpi-project/glpi/releases/download/{{ glpi_version }}/{{ glpi_version_package }}"
```
GLPI web owner, group and installation path.
```yml
glpi_web_owner: "www-data"
glpi_web_group: "www-data"
glpi_install_path: /var/www
```

Information about the GLPI database.
```yml
glpi_db_host: "localhost"
glpi_db_port: "3306"
glpi_db_name: "glpi"
glpi_db_user: "glpi"
glpi_db_password: "changeit!"
```

## Examples
```yml
- hosts: all
  roles:
    - role: supertarto.apache
    - role: supertarto.mariadb
    - role: supertarto.php
    - role: supertarto.glpi
  vars:
    php_packages:
      - php8.2
      - php8.2-gd
      - php8.2-mysql
      - php8.2-curl
      - php8.2-imap
      - php8.2-json
      - php8.2-ldap
      - php8.2-mbstring
      - php8.2-xml
      - php8.2-cli
      - php8.2-xmlrpc
      - php8.2-intl
      - php-apcu
      - php-cas
      - php8.2-zip
      - php8.2-bz2
    glpi_db_host: "localhost"
    glpi_db_port: "3306"
    glpi_db_name: "glpi"
    glpi_db_user: "glpi"
    glpi_db_password: "changeit!"
    apache_create_vhosts: true
    apache_vhosts_filename: "glpi.conf"
    apache_vhost_config:
      - listen_ip: "*"
        listen_port: 80
        server_name: host1
        documentroot: "/var/www/glpi"
        serveradmin: admin@localhost
        custom_param: |
          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined
          LogLevel warn
        directory:
          - path: "/var/www/glpi"
            config: |
              AllowOverride All
              Order deny,allow
              allow from all

    mariadb_use_dump_script: false
    mariadb_databases:
      - name: "{{ glpi_db_name }}"

    mariadb_users:
      - name: "{{ glpi_db_user }}"
        host: "{{ glpi_db_host }}"
        password: "{{ glpi_db_password }}"
        priv: "{{ glpi_db_name }}.*:SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER,CREATE TEMPORARY TABLES,LOCK TABLES"
```
## Installation
```
ansible-galaxy role install supertarto.glpi
```
## License
GPL V3.0
