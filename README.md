# Vector tiles hosting
## What do we do in this hands-on?

1.
2.
3.

# Software
## Recommended baseline software

### Linux
Although this process works well with macOS and probably works with Cygwin or the Windows Subsystem for Linux, this hands-on assumes that we use Linux, such as ubuntu or RedHat.

### Google Chrome
Although vector tiles works well with all modern web browsers. You may want to add Chrome Secure Shell extension for ssh access to the server.

## Required software

### Node.js

You can install from [nodejs.org](https://nodejs.org/ja/download/package-manager/#debian-and-ubuntu-based-linux-distributions-debian-ubuntu-linux)

Or you may want to use [n](https://github.com/tj/n):
```
npm install -y nodejs
npm cache clean
npm install -g n
npm cache clean
n stable
ln -sf /usr/local/bin/node /usr/bin/node
apt-get -y purge npm nodejs
```

### git

[git](https://git-scm.com/) is installed in Linux in many cases.

# Hands-on steps

## Software check
### check node

```console
$ node -v
v8.11.2
```

## Host vector tiles
### clone tile-block using git

```console
$ cd # if you want to go back to home directory.
$ git clone git@github.com:hfu/tile-block.git
Initialized empty Git repository in /home/fhidenori/hands-on/tile-block/.git/
remote: Counting objects: 99, done.
remote: Compressing objects: 100% (72/72), done.
remote: Total 99 (delta 51), reused 70 (delta 25), pack-reused 0
Receiving objects: 100% (99/99), 26.94 KiB, done.
Resolving deltas: 100% (51/51), done.
```

### install npm modules

```console
$ cd tile-block
$ npm install
added 166 packages from 81 contributors in 5.574s
```

### change port number

```
console
$ vi config/default.hjson
```

If you want to change the port number to 8888, default.hjson file should look like the following.

```hjson
{
  port: 8888
}
```

### copy mbtiles file to mbtiles/

### make a copy of htdocs/washington/ and edit the copy for a test site

### run the tile-block server

### check the web site

## Next steps
### 
