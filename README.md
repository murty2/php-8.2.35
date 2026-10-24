# Run php version 8.2.35 on any Linux operating system

## Download necessary binaries
Download php (Command Line Interface), php-cgi (FastCGI testing tool) or php-fpm from the Releases page
https://github.com/murty2/php-8.2.35/releases

## Run manually
```
./php-fpm -c /path/to/php.ini -y /path/to/php-fpm.conf
```

## If using systemd
Update your php-fpm.service file to use this php-fpm. Remember to include -c and -y arguments as stated above.

## How to create the binaries (php, php-fpm, php-cgi) from source
* First create and login to a docker that works for version 8.2.35
```
docker run --rm -it alpine:3.18 bash
```
* Run these commands at the container prompt
```
mkdir /build
cd /build

apk add --no-cache \
    bash curl git build-base autoconf automake libtool \
    bison re2c flex openssl-dev zlib-dev libxml2-dev \
    sqlite-dev libpng-dev libjpeg-turbo-dev freetype-dev \
    libwebp-dev libzip-dev bzip2-dev gmp-dev icu-dev \
    gettext-dev openssh-client rsync

curl -fsSL https://dl.static-php.dev/v3/spc-bin/nightly/spc-linux-x86_64 -o spc
chmod +x spc
./spc doctor --auto-fix

# auto-fix one more time
./spc doctor --auto-fix

# Pick your extensions here
EXTENSIONS="apcu,bcmath,bz2,calendar,ctype,curl,dom,exif,fileinfo,filter,ftp,gd,gettext,iconv,intl,mbregex,mbstring,mysqli,mysqlnd,opcache,openssl,pcntl,pdo,pdo_mysql,pdo_sqlite,phar,posix,readline,redis,session,shmop,simplexml,sockets,sodium,sqlite3,sysvmsg,sysvsem,sysvshm,tokenizer,xml,xmlreader,xmlwriter,xsl,zip,zlib"

# Download all sources. If it fails, re-run until it succeeds (Github rate limits you)
./spc download --with-php 8.2.35 --for-extensions "$EXTENSIONS"

# Compile everything. It will take an hour
./spc build "$EXTENSIONS" --build-fpm --build-cgi --build-cli

cd buildroot/bin

# Upload the binaries to your favorite target machine
scp php* hostname:/tmp
```
