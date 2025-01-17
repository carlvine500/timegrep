# timegrep

Utility to grep log between two dates or tail last lines to time ago similar [dategrep](https://github.com/mdom/dategrep) or [dateutils](https://github.com/hroptatyr/dateutils).

## Examples

Grep last minute from current nginx access log (binary search):

```
$ timegrep --format=nginx --minutes=1 /var/log/nginx/access.log
```

Grep datetime interval from archive log (sequential read data from `stdin`):

```
$ zcat archive.log.gz | timegrep --start='2017:09:01 15:23:00' --stop='2017:09:01 16:32:00'
```

## Download / Install

* [Precompiled static binaries](https://github.com/abbat/timegrep/releases) built with system libc (glibc) and musl libc for x64 and i386
* From source code:

```
$ git clone https://github.com/abbat/timegrep.git
$ make && sudo make install
```

To compile with own flags use `USER_CFLAGS` and `USER_LDFLAGS` variables. For example for i386 static binary with [musl libc](https://www.musl-libc.org):

```
$ CC=musl-gcc32 USER_CFLAGS=-m32 USER_LDFLAGS='-L/usr/lib/i386-linux-gnu -m32 -static -Wl,-melf_i386' make
```

## Usage

```
timegrep [options] [files]
```

**Options**

* `--help`, `-?` - print help message and named datetime formats;
* `--version`, `-v` - print program version and exit;
* `--format`, `-e` - datetime format (default: 'default');
* `--start`, `-f` - datetime to start search (default: now);
* `--stop`, `-t` - datetime to stop search (default: now);
* `--seconds`, `-s` - seconds to substract from `--start` (default: 0);
* `--minutes`, `-m` - minutes to substract from `--start` (default: 0);
* `--hours`, `-h` - hours to substract from `--start` (default: 0).

See [strptime(3)](https://linux.die.net/man/3/strptime) for format details. See `--help` for list of format aliases.

## Exit code

* `0` - successful completion;
* `1` - nothing found;
* `2` - general application error.

## How to help

* Translate this document or [man page](https://github.com/abbat/timegrep/blob/master/timegrep.1) to your native language;
* Proofreading README.md or man page with your native language;
* Share, Like, RT to your friends;
* Send PRs if you are developer.

## compile in alpine
```
docker run -it -v$(pwd):/tmp --rm alpine:3.15 sh   
echo "https://mirrors.aliyun.com/alpine/v3.15/main/" > /etc/apk/repositories \
    && echo "https://mirrors.aliyun.com/alpine/v3.15/community/" >> /etc/apk/repositories \
    && apk update ;

apk add gettext-dev pcre-dev g++ gcc make glib-static
wget https://codeload.github.com/abbat/timegrep/zip/refs/tags/v0.7 -O timegrep.zip
unzip timegrep.zip 
cd timegrep-0.7

CC=x86_64-alpine-linux-musl-gcc  USER_CFLAGS=-m64 USER_LDFLAGS='-static -lintl -Wl,-melf_x86_64' make

cp timgrep /tmp
```

### example (commit by Fe)
```
echo '{"instant":{"epochSecond":1681732800,"nanoOfSecond":"20:00:00"}}
{"instant":{"epochSecond":1681732806,"nanoOfSecond":"20:00:06"}}
{"instant":{"epochSecond":1681732806,"nanoOfSecond":"20:00:06"}}
{"instant":{"epochSecond":1681732807,"nanoOfSecond":"20:00:07"}}
{"instant":{"epochSecond":1681733862,"nanoOfSecond":"20:17:42"}}' | tee x.log
 ./timegrep timegrep --start="2023-03-01 00:00:00" --format=json_timestamp x.log                    
```
