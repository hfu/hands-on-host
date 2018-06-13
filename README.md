# Vector tiles hosting
## What do we do in this hands-on?
In this hands-on, we host vector tiles in mbtiles via a simple server. We also host a simple site to use these vector tiles.

# Software
## Recommended baseline software

### Linux
Although this process works well with macOS and probably works with Cygwin or the Windows Subsystem for Linux, this hands-on assumes that we use Linux, such as ubuntu or RedHat.

### Google Chrome
Although vector tiles works well with all modern web browsers. You may want to add Chrome Secure Shell extension to access to the server.

## Required software

### Node.js
You can skip this step if your hands-on tutor has Node.js ready for you.

Otherwise, you can install Node.js from [nodejs.org](https://nodejs.org/ja/download/package-manager/#debian-and-ubuntu-based-linux-distributions-debian-ubuntu-linux)

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
First, we get a copy of [a simplest-possible mbtiles-based http/2 vector tile server](https://github.com/hfu/tile-block/) from GitHub.

```console
$ git clone git@github.com:hfu/tile-block.git
Initialized empty Git repository in /home/fhidenori/tile-block/hands-on/tile-block/.git/
remote: Counting objects: 110, done.
remote: Compressing objects: 100% (79/79), done.
remote: Total 110 (delta 56), reused 78 (delta 27), pack-reused 0
Receiving objects: 100% (110/110), 27.97 KiB, done.
Resolving deltas: 100% (56/56), done.
```

### install npm modules
Second, we install npm modules as below.

```console
$ cd tile-block
$ npm install

> sqlite3@3.1.13 install /home/fhidenori/tile-block/hands-on/tile-block/node_modules/sqlite3
> node-pre-gyp install --fallback-to-build

[sqlite3] Success: "/home/fhidenori/tile-block/hands-on/tile-block/node_modules/sqlite3/lib/binding/node-v57-linux-x64/node_sqlite3.node" is installed via remote
added 192 packages in 10.222s
```

### change port number
In case we use a shared server or you simply want to assing a port number you like, please edit config/default.hjson.

```console
$ vi config/default.hjson
```

If you want to change the port number to 8888, default.hjson file should look like the following. The default port number is 3776.

```hjson
{
  port: 8888
}
```

### copy vector tiles
Next, create a directory named mbtiles, and copy your mbtiles into it as named default.mbtiles (for the sake of the simplicity of the hands-on).

```console
$ mkdir mbtiles
$ cp ~/pnd/8-150-124.mbtiles mbtiles/default.mbtiles
```

All vector tiles copied in this directory will be served. For example, if you copy {filename}.mbtiles in mbtiles, vector tiles will be served from https://{server}:{port}/zxy/{filename}/{z}/{x}/{y} without filename extension like pbf.

In addition, if the {filename} is in the form of 5-{X}-{Y} and contains vector tiles of a z=5 module, vector tiles will also be served from https://{server}:{port}/module/{z}/{x}/{y} using multiple mbtiles files for respective z=5 modules.

You can change these behaviour by modifying [tile-block.js](https://github.com/hfu/tile-block/blob/master/tile-block.js).

### update a JSON file
In the tile-block repository, all the files in the htdocs directory will be served. 

We use htdocs/default/index.html and htdocs/default/style.json already in the repository.

Please edit htdocs/default/style.json line 7 to have the correct server address and port for your hands-on setting. Below is the style.json which is intended to serve tiles from the port 9999 of tiles.example.com:

```json
{
  "version": 8,
  "sources": {
    "unbase": {
      "type": "vector",
      "tiles": [
        "https://tiles.example.com:9999/default/{z}/{x}/{y}"
      ],
      "attribution": "please update the attribution.",
      "minzoom": 5,
      "maxzoom": 16
    },
  },
  "glyphs": "https://hfu.github.io/openmaptiles-fonts/{fontstack}/{range}.pbf",
  "sprite": "https://openmaptiles.github.io/osm-bright-gl-style/sprite",
  "layers": [
    {
      "id": "background",
      "type": "background",
      "layout": {},
      "paint": {
        "background-color": "#000"
      }
    },
    {
      "id": "polygon",
      "type": "fill",
      "source": "unbase",
      "source-layer": "polygon",
      "layout": {},
      "paint": {
        "fill-color": "#fff",
        "fill-outline-color": "#fff",
        "fill-opacity": 0.1
      }
    },
    {
      "id": "linestring",
      "type": "line",
      "source": "unbase",
      "source-layer": "linestring",
      "layout": {},
      "paint": {
        "line-color": "#9e9e9e",
        "line-width": {
          "base": 0.5,
          "stops": [ [ 12, 1 ], [ 22, 4 ] ]
        }
      }
    },
    {
      "id": "point",
      "type": "symbol",
      "source": "unbase",
      "source-layer": "point",
      "layout": {
        "text-field": "{text}",
        "text-font": [
          "Metropolis Light"
        ]
      },
      "paint": {
        "text-color": "#e0e0e0"
      }
    }
  ]
}
```

### copy server certificates
We need to have private.key and server.crt at the top level of the tile-block repository (the same directory as tile-block.js) to serve the site via HTTP/2. Your hand-on tutor may provide them to help you. Otherwise, you can create them by following [README](https://github.com/hfu/tile-block#how-to-use).

```console
$ cd {somewhere}/tile-block
$ copy {elsewhere}/private.key .
$ copy {elsewhere}/server.crt .
```

or 

```console
$ cd {somewhere}/tile-block
$ openssl genrsa -des3 -passout pass:tile-block -out private.key 2048
$ openssl rsa -in private.key -pubout -out public.key -passin pass:tile-block
$ openssl req -new -key private.key -out server.csr
$ openssl x509 -req -days 365 -signkey private.key < server.csr > server.crt
```

In case you don't want HTTP/2, you have a choice to serve from plain old HTTP.

### run the tile-block server and check the web site.
Now you are good to start the server. 

```console
$ node tile-block.js
```
Your vector tiles will be displayed on https://{server}:{port}/default/ .

Press Ctrl-C on console to stop the server.

You can also change the port number by giving it as a command parameter, like:
```console
$ node tile-block.js 6666
```

This will start the server at the port 6666.

For informatin, if you want to run the server even after you log-off, you can server.sh, and unix commands such as ps and kill. Below is a typical example. 

```console
$ ps aux | grep tile-block
506       8024  0.0  0.3 1217700 64188 ?       Sl   Jun08   0:03 node tile-block.js
506      17306  0.0  0.0 103328   868 pts/0    S+   19:16   0:00 grep tile-block
$ kill 8024; ./serve.sh
```

### OpenLayers example
If https://{server}:{port}/default/ works, https://{server}:{port}/default/openlayers.html will work with OpenLayers.

## Next steps
### Add tiles for larger extent
You can add vector tiles for larger extent by further [producing vector tiles](https://hfu.github.io/hands-on-prod/).

### Change style
You can edit style.json to change the style according to [Mapbox Style Specification](https://www.mapbox.com/mapbox-gl-js/style-spec/).
