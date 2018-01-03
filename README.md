[![Build Status](https://travis-ci.org/woahbase/alpine-redis.svg?branch=master)](https://travis-ci.org/woahbase/alpine-redis) [![](https://images.microbadger.com/badges/image/woahbase/alpine-redis.svg)](https://microbadger.com/images/woahbase/alpine-redis) [![](https://images.microbadger.com/badges/commit/woahbase/alpine-redis.svg)](https://microbadger.com/images/woahsbase/alpine-redis) [![](https://images.microbadger.com/badges/version/woahbase/alpine-redis.svg)](https://microbadger.com/images/woahbase/alpine-redis)

## Alpine-Redis
#### Container for Alpine Linux + Redis

---

This [image][8] containerizes the [Redis][12] Cache/Message broker
to setup a key-value store / pub-sub service.

Built from my [alpine-s6][9] image with the [s6][10] init system
[overlayed][11] in it.

The image is tagged respectively for the following architectures,
* **armhf**
* **x86_64**

**armhf** builds have embedded binfmt_misc support and contain the
[qemu-user-static][5] binary that allows for running it also inside
an x64 environment that has it.

---
#### Get the Image
---

Pull the image for your architecture it's already available from
Docker Hub.

```
# make pull
docker pull woahbase/alpine-redis:x86_64

```

---
#### Run
---

If you want to run images for other architectures, you will need
to have binfmt support configured for your machine. [**multiarch**][4],
has made it easy for us containing that into a docker container.

```
# make regbinfmt
docker run --rm --privileged multiarch/qemu-user-static:register --reset

```
Without the above, you can still run the image that is made for your
architecture, e.g for an x86_64 machine..

```
# make
docker run --rm -it \
  --name docker_redis --hostname redis \
  -c 256 -m 256m \
  -e PGID=100 -e PUID=1000 \
  -p 6379:6379 \
  -v /etc/hosts:/etc/hosts:ro \
  -v /etc/localtime:/etc/localtime:ro \
  -v data:/var/lib/redis \
  woahbase/alpine-redis:x86_64

# make stop
docker stop -t 2 docker_redis

# make rm
# stop first
docker rm -f docker_redis

# make restart
docker restart docker_redis

```

---
#### Shell access
---

```
# make rshell
docker exec -u root -it docker_redis /bin/bash

# make shell
docker exec -it docker_redis /bin/bash

# make logs
docker logs -f docker_redis

```

---
## Development
---

If you have the repository access, you can clone and
build the image yourself for your own system, and can push after.

---
#### Setup
---

Before you clone the [repo][7], you must have [Git][1], [GNU make][2],
and [Docker][3] setup on the machine.

```
git clone https://github.com/woahbase/alpine-redis
cd alpine-redis

```
You can always skip installing **make** but you will have to
type the whole docker commands then instead of using the sweet
make targets.

---
#### Build
---

You need to have binfmt_misc configured in your system to be able
to build images for other architectures.

Otherwise to locally build the image for your system.

```
# make ARCH=x86_64 build
# sets up binfmt if not x86_64
docker build --rm --compress --force-rm \
  --no-cache=true --pull \
  -f ./Dockerfile_x86_64 \
  -t woahbase/alpine-redis:x86_64 \
  --build-arg ARCH=x86_64 \
  --build-arg DOCKERSRC=alpine-s6 \
  --build-arg USERNAME=woahbase \
  --build-arg PUID=1000 \
  --build-arg PGID=1000

# make ARCH=x86_64 test
docker run --rm -it \
  --name docker_redis --hostname redis \
  woahbase/alpine-redis:x86_64 \
  sh -ec 'redis-server --version; redis-cli --version'

# make ARCH=x86_64 push
docker push woahbase/alpine-redis:x86_64

```

---
## Maintenance
---

Built daily at Travis.CI (armhf / x64 builds). Docker hub builds maintained by [woahbase][6].

[1]: https://git-scm.com
[2]: https://www.gnu.org/software/make/
[3]: https://www.docker.com
[4]: https://hub.docker.com/r/multiarch/qemu-user-static/
[5]: https://github.com/multiarch/qemu-user-static/releases/
[6]: https://hub.docker.com/u/woahbase

[7]: https://github.com/woahbase/alpine-redis
[8]: https://hub.docker.com/r/woahbase/alpine-redis
[9]: https://hub.docker.com/r/woahbase/alpine-s6

[10]: https://skarnet.org/software/s6/
[11]: https://github.com/just-containers/s6-overlay
[12]: https://redis.io
