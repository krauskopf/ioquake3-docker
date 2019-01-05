# ioquake3-docker
Dockerfiles for [ioquake3](https://ioquake3.org/) dedicated server

## Setup

### Compose file
You may want to use the following docker compose file to run the server:

```
version: '3.2'
services:
 quake3:
  image: krauskopf/ioquake3-server:latest
  entrypoint: /home/ioq3srv/ioquake3/ioq3ded.x86_64
   +set dedicated 1
   +exec server.cfg
  volumes:
   - quake3-data:/home/ioq3srv/.q3a/:rw
  deploy:
   replicas: 1
   restart_policy:
    condition: any
    delay: 10s
    max_attempts: 3
  ports:
    - target: 27960
      published: 27960
      protocol: udp
      mode: host
volumes:
 quake3-data:
networks:
 default:
  external:
   name: web
```


### Game files
For the server to run, you have to copy all `.pk3` files from the `baseq3` folder in your game directory into the folder `baseq3` in the volume `quake3-data`. Note, that the `pak0.pk3` is not distributed with ioquake, but has to be taken from the original disk.

```
docker cp *.pk3 quake3-data:/baseq3/
```

### Server Configuration
You have to also provide a server configuration file in the `baseq3` folder of the volume. To match the compose file from above, name it `server.cfg`. There are many tutorials for this around in the web. Here is only a short template:


```
// --- basic stuff ---
seta rconPassword "change me"                       // remote console admin password
seta r_smp "0"                                      // 0 = single cpu, 1 = multi processing

// --- banner stuff ---
seta sv_hostname "change me"                        // how the server shows up in q3a game browser
seta g_motd "change me"                             // message of the day, shown on client connect

// --- clients and slots ---
g_password "change me"                              // server password for clients who try to connect
g_needpass "0"                                      // whether the password is enabled / needed to connect
seta sv_maxClients "16"                             // max players allowed on server, includes spectators
seta sv_privateClients "2"                          // reserved slots for players who know the private password
seta sv_privatePassword "nuklear"                   // private slot password
seta g_syncronousClients "0"                        // whether clients are allowed to record demos
seta g_allowvote "1"                                // map - map restart - kick - g_gametype

// --- weapons ---
seta g_quadfactor "3"                               // quad damage multiplier, default = 3
seta g_weaponrespawn "5"                            // weapon respawn time in secs, default = 5
seta g_friendlyfire "1"                             // whether you can do damage to your team members
seta g_teamAutoJoin "0"                             // whether players are automatically added to a team
seta g_teamForceBalance "0"                         // whether teams are auto-balanced by the server
seta g_forcerespawn "0"                             // time after which players are forced to respawn, 0 = never

// --- bots ---
seta bot_enable "1"                                 // whether bots are allowed on the server
seta bot_minplayers "0"                             // minimum players number, filled up with bots if fewer
seta bot_nochat "1"                                 // whether bots are allowed to chat

// --- game ---
seta g_gametype "0"                                 // gametype (0 = ffa, 1 = tourney,  2 = ffa, 3 = tdm, 4 = ctf)
seta timelimit 15
seta fraglimit 25
seta g_inactivity 3000
set g_warmup "10"

map q3dm16                                          // start map
```

Copy to the volume:
```
docker cp server.cfg quake3-data:/baseq3/
```


## Credits
Thanks to [jberrenberg/quake3](https://hub.docker.com/r/jberrenberg/quake3/) for the initial build script.
