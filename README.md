# Scaleway CLI

[![Build Status (Travis)](https://img.shields.io/travis/scaleway/scaleway-cli.svg)](https://travis-ci.org/scaleway/scaleway-cli)
[![Dependency Status](https://img.shields.io/david/scaleway/scaleway-cli.svg)](https://david-dm.org/scaleway/scaleway-cli)
[![](https://img.shields.io/npm/dm/scaleway-cli.svg)](https://npmjs.org/package/scaleway-cli)
[![](https://img.shields.io/npm/v/scaleway-cli.svg)](https://npmjs.org/package/scaleway-cli)
[![](https://img.shields.io/npm/l/scaleway-cli.svg)](https://npmjs.org/package/scaleway-cli)

Interact with Scaleway API from the command line.

Uses [moul/node-scaleway](https://github.com/moul/node-scaleway) SDK.


## Usage

Usage inspired by [Docker CLI](https://docs.docker.com/reference/commandline/cli/)

```console
$ scw

  Usage: scw [options] [command]


  Commands:

    attach <server>                 attach (serial console) to a running server
    build <path>                    build an image from a file
    commit <server>                 create a new image from a server's changes
    cp <server:path> <path>         copy files/folders from a server's filesystem to the host path
    create <image>                  create a new server but do not start it
    events                          get real time events from the API
    exec <server> <command>         run a command in a running server
    export <server>                 stream the contents of a server as a tar archive
    history <image>                 show the history of an image
    images                          list images
    import                          create a new filesystem image from the contents of a tarball
    info                            display system-wide information
    inspect <item> [otherItems...]  return low-level information on a server or image
    kill <server>                   kill a running server
    load                            load an image from a tar archive
    login                           login to the API
    logout                          log out from the API
    logs <server>                   fetch the logs of a server
    port                            list port security for the server
    pause                           pause all processes within a server
    ps                              list servers
    pull <image>                    pull an image or a repository
    push <image>                    push an image or a repository
    rename <server>                 rename an existing server
    restart <server>                restart a running server
    rm <server>                     remove one or more servers
    rmi <image>                     remove one or more images
    run <image>                     run a command in a new server
    save <image>                    save an image to a tar archive
    search <keyword>                search for an image on the Hub
    start <server>                  start a stopped server
    stop <server>                   stop a running server
    tag <image> <tag>               tag an image into a repository
    top <server>                    lookup the running processes of a server
    unpause <server>                unpause a paused server
    version                         show the version information
    wait <server>                   block until a server stops

  Options:

    -h, --help            output usage information
    -V, --version         output the version number
    --api-endpoint <url>  set the API endpoint
    -D, --debug           enable debug mode
```


## Examples

Create a server with Ubuntu Trusty image and 3.2.34 bootscript

```console
$ scw create trusty --bootscript=3.2.34
df271f73-60ce-47fd-bd7b-37b5f698d8b2
```


Create a server with Fedora 21 image

```console
$ scw create 1f164079
7313af22-62bf-4df1-9dc2-c4ffb4cb2d83
```


Create a server with an empty disc of 20G and rescue bootscript

```console
$ scw create 20G --bootscript=rescue
5cf8058e-a0df-4fc3-a772-8d44e6daf582
```


Run a stopped server

```console
$ scw start 7313af22
7313af22-62bf-4df1-9dc2-c4ffb4cb2d83
```


Run a stopped server and wait for SSH to be ready

```console
$ scw start --sync myserver
myserver
$ scw exec myserver /bin/bash
[root@noname ~]#
```


Run a stopped server and wait for SSH to be ready (inline version)

```console
$ scw exec $(scw start --sync myserver) /bin/bash
[root@noname ~]#
```


Create, start and ssh to a new server (inline version)

```console
$ scw exec $(scw start --sync $(scw create ubuntu-trusty)) /bin/bash
[root@noname ~]#
```

or

```console
$ scw exec --wait $(scw start $(scw create ubuntu-trusty)) /bin/bash
[root@noname ~]#
```


Wait for a server to be available, then execute a command

```console
$ scw exec --wait myserver /bin/bash
[root@noname ~]#
```


Run a stopped server and wait for SSH to be ready with:

- a timeout of 120 seconds for kernel to start
- a timeout of 60 seconds for SSH to be ready
- a global timeout of 150 seconds

```console
$ scw start --sync --boot-timeout=120 --ssh-timeout=60 --timeout=150 myserver
global execution... failed: Operation timed out.
```


Wait for a server to be in 'stopped' state

```console
$ scw wait 7313af22
[...] some seconds later
0
```


Attach to server serial port

```console
$ scw attach 7313af22
[RET]
Ubuntu Vivid Vervet (development branch) nfs-server ttyS0
my-server login:
^C
$
```


Create a server with Fedora 21 image and start it

```console
$ scw start `scw create 1f164079`
5cf8058e-a0df-4fc3-a772-8d44e6daf582
```


Execute a 'ls -la' on a server (via SSH)

```console
$ scw exec myserver -- ls -la
total 40
drwx------.  4 root root 4096 Mar 26 05:56 .
drwxr-xr-x. 18 root root 4096 Mar 26 05:56 ..
-rw-r--r--.  1 root root   18 Jun  8  2014 .bash_logout
-rw-r--r--.  1 root root  176 Jun  8  2014 .bash_profile
-rw-r--r--.  1 root root  176 Jun  8  2014 .bashrc
-rw-r--r--.  1 root root  100 Jun  8  2014 .cshrc
drwxr-----.  3 root root 4096 Mar 16 06:31 .pki
-rw-rw-r--.  1 root root 1240 Mar 12 08:16 .s3cfg.sample
drwx------.  2 root root 4096 Mar 26 05:56 .ssh
-rw-r--r--.  1 root root  129 Jun  8  2014 .tcshrc
```


Run a shell on a server (via SSH)

```console
$ scw exec 5cf8058e /bin/bash
[root@noname ~]#
```


List public images and my images

```console
$ scw images
REPOSITORY                                 TAG      IMAGE ID   CREATED        VIRTUAL SIZE
user/Alpine_Linux_3_1                      latest   854eef72   10 days ago    50 GB
Debian_Wheezy_7_8                          latest   cd66fa55   2 months ago   20 GB
Ubuntu_Utopic_14_10                        latest   1a702a4e   4 months ago   20 GB
...
```


List public images, my images and my snapshots

```console
$ scw images -a
REPOSITORY                                 TAG      IMAGE ID   CREATED        VIRTUAL SIZE
noname-snapshot                            <none>   54df92d1   a minute ago   50 GB
cool-snapshot                              <none>   0dbbc64c   11 hours ago   20 GB
user/Alpine_Linux_3_1                      latest   854eef72   10 days ago    50 GB
Debian_Wheezy_7_8                          latest   cd66fa55   2 months ago   20 GB
Ubuntu_Utopic_14_10                        latest   1a702a4e   4 months ago   20 GB
```


List running servers

```console
$ scw ps
SERVER ID   IMAGE                       COMMAND   CREATED          STATUS    PORTS   NAME
7313af22    user/Alpine_Linux_3_1                 13 minutes ago   running           noname
32070fa4    Ubuntu_Utopic_14_10                   36 minutes ago   running           labs-8fe556
```


List all servers

```console
$ scw ps -a
SERVER ID   IMAGE                       COMMAND   CREATED          STATUS    PORTS   NAME
7313af22    user/Alpine_Linux_3_1                 13 minutes ago   running           noname
32070fa4    Ubuntu_Utopic_14_10                   36 minutes ago   running           labs-8fe556
7fc76a15    Ubuntu_Utopic_14_10                   11 hours ago     stopped           backup
```


Stop a running server

```console
$ scw stop 5cf8058e
5cf8058e
```


Stop multiple running servers

```console
$ scw stop myserver myotherserver
901d082d-9155-4046-a49d-94355344246b
a0320ec6-141f-4e99-bf33-9e1a9de34171
```


Terminate a running server

```console
$ scw stop -t myserver
901d082d-9155-4046-a49d-94355344246b
```


Stop all running servers matching 'mysql'

```console
$ scw stop $(scw ps | grep mysql | awk '{print $1}')
901d082d-9155-4046-a49d-94355344246b
a0320ec6-141f-4e99-bf33-9e1a9de34171
36756e6e-3146-4b89-8248-abb060fc5b61
```


Create a snapshot of the root volume of a server

```console
$ scw commit 5cf8058e
54df92d1
```


Delete a stopped server

```console
$ scw rm 5cf8
5cf8082d-9155-4046-a49d-94355344246b
```


Delete multiple stopped servers

```console
$ scw rm myserver myotherserver
901d082d-9155-4046-a49d-94355344246b
a0320ec6-141f-4e99-bf33-9e1a9de34171
```


Delete all stopped servers matching 'mysql'

```console
$ scw rm $(scw ps -a | grep mysql | awk '{print $1}')
901d082d-9155-4046-a49d-94355344246b
a0320ec6-141f-4e99-bf33-9e1a9de34171
36756e6e-3146-4b89-8248-abb060fc5b61
```


Create a snapshot of nbd1

```console
$ scw commit 5cf8058e -v 1
f1851f99
```


Create an image based on a snapshot

```console
$ scw tag 87f4526b my_image
46689419
```


Delete an image

```console
$ scw rmi 46689419
```


Send a 'halt' command via SSH

```console
$ scw kill 5cf8058e
5cf8058e
```


Inspect a server

```console
$ scw inspect 90074de6
[
  {
    "server": {
    "dynamic_ip_required": true,
    "name": "My server",
    "modification_date": "2015-03-26T09:01:07.691774+00:00",
    "tags": [
      "web",
      "production"
    ],
    "state_detail": "booted",
    "public_ip": {
      "dynamic": true,
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "address": "212.47.xxx.yyy"
    },
    "state": "running",
  }
]
```


Show public ip address of a server

```console
$ scw inspect 90074de6 -f '.server.public_ip.address'
212.47.xxx.yyy
```


## Advanced commands

We added some non-docker inspired commands (hidden in the usage)

#### _patch

Usage:

```console
$ scw _patch item field1=value1 field2=value2
```

Example:

```console
$ scw _patch myserver state_detail=booted
- state_detail: booting kernel => booted
myserver
```


## Workflows


For more examples, see [./examples/](https://github.com/scaleway/scaleway-cli/tree/master/examples) directory

```console
# create a server with a nbd1 volume of 50G and rescue bootscript
$ SERVER=$(scw create trusty --bootscript=rescue --volume=50000000000 --sync)
# print the ip address of the server
$ echo "Your server is ready and is available at: $(scw inspect ${SERVER} -f .server.public_ip.address)"
```


## Debug

`scaleway-cli` uses the [debug](https://www.npmjs.com/package/debug) package.

To enable debug you can use the environment variable `DEBUG=` as :

- `DEBUG='*' scw ...` to see debug for `scaleway-cli` and all dependencies
- `DEBUG='scaleway-cli:*' scw ...` to see debug for `scaleway-cli`
- `DEBUG='node-scaleway:*' scw ...` to see debug for `node-scaleway`

```console
$ DEBUG='*' scw images
  node-scaleway:lib GET https://api.cloud.online.net/images? +0ms { method: 'GET',
  url: 'https://api.cloud.online.net/images?',
  headers:
   { Accept: 'application/json',
     'User-Agent': 'node-scaleway',
     'X-Auth-Token': 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' },
  resolveWithFullResponse: true,
  json: true }
REPOSITORY                                 TAG      IMAGE ID   CREATED        VIRTUAL SIZE
Fedora_21_Twenty-one                       latest   1f164079   10 days ago    50 GB
user/Archlinux_latest                      latest   1197ca91   10 days ago    50 GB
...
scaleway-cli:utils saveEntities: removed 15 items +0ms
scaleway-cli:utils saveEntities: inserted 15 items +4ms
```


## Install

1. Install `Node.js` and `npm` (https://nodejs.org/download/)
2. Install `scaleway-cli`: `$ npm install -g scaleway-cli`
3. Setup token and organization: `$ scw login --token=XXXXX --organization=YYYYY`
4. Profit... `$ scw ps -a`


## License

[MIT](https://github.com/scaleway/scaleway-cli/blob/master/LICENSE.md)
