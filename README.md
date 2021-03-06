[![Docker Stars](https://img.shields.io/docker/stars/kaniabi/hubot.svg?style=flat-square)](https://hub.docker.com/r/kaniabi/hubot/)
[![Docker Pulls](https://img.shields.io/docker/pulls/kaniabi/hubot.svg?style=flat-square)](https://hub.docker.com/r/kaniabi/hubot/)
[![ImageLayers Size](https://badge.imagelayers.io/kaniabi/hubot:latest.svg)](https://imagelayers.io/?images=kaniabi/hubot:latest)

# Hubot on Docker

This is ready for production docker-image for [hubot](https://hubot.github.com/)
instances.

Execute it anywhere with your custom options. Note the dependency to a
[redis](http://redis.io/) server:

```
$ docker run --rm --name redis \
  -p 6379:6379 \
  -v /opt/redis:/var/redis \
  smebberson/alpine-redis

$ docker run --rm --name hubot \
  --link redis.service:redis \
  -e "REDIS_URL=redis://redis:6379=" \
  -e "HUBOT_NAME=<your bot name>" \
  -e "HUBOT_SLACK_TEAM=<your team name>" \
  -e "HUBOT_SLACK_TOKEN=<your slack token>" \
  kaniabi/hubot:latest
```

You can also use Unit files that will look like this:

```
[Unit]
Description=Hubot bot
After=docker.service
Requires=docker.service

[Service]
# Make sure we have the image latest version
ExecStartPre=-/usr/bin/docker pull kaniabi/hubot:latest
ExecStop=/usr/bin/docker stop -t 3 %n
ExecStart=/usr/bin/docker run --rm --name %n \
  --link redis.service:redis \
  -e "REDIS_URL=redis://redis:6379=" \
  -e "HUBOT_NAME=<your bot name>" \
  -e "HUBOT_SLACK_TEAM=<your team name>" \
  -e "HUBOT_SLACK_TOKEN=<your slack token>" \
  kaniabi/hubot:latest

[X-Fleet]
X-ConditionMachineOf=redis.service
```