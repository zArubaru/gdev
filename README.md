# gdev

## What is this for?

Helper script for all your container related development needs. This is for locally developing containerized applications in Geniem.

## Quick installation

Install gdev dependencies and start development environment by running:

    $ curl -fsSL https://raw.githubusercontent.com/devgeniem/gdev/master/bin/bootstrap | bash

## Start project containers

Start a new shell and cd to a project that uses [Docker Compose](https://docs.docker.com/compose/)

```
$ gdev up
```

## Short list of usual commands

```
# This is similiar to vagrant up
# It reads docker-compose.yml from current directory and starts up containers
$ gdev up

# Open shell into web container
$ gdev shell

# Restart all project containers
$ gdev reload

# List all containers from project
$ gdev ps

# List all containers from docker
$ docker ps -a

# Open bash into any container
$ docker exec -it $CONTAINER_ID bash
```

## Workflow

- The source code running inside a project container is loaded from the directory on your hard drive. You can use text editors and Git clients on the host machines, and shouldn't need to work in the guest machine or the container.
- You should not need to run any application code directly from your host machine. Try to force yourself to find a containerized way of accomplishing things.
- Run `gdev` without any arguments for lots of help

### Troubleshooting

#### No space left on device
Docker for Mac has only limited amount of disk space and this means that older images or stopped containers are taking all of the 60gb/120gb share.

To resolve this delete stopped containers, dangling images and dangling volumes. This can be done by running cleanup helper:

```
$ gdev cleanup
```

#### When in doubt, update and restart everything

To update all containers and settings run following global commands:
```
$ gdev pull
$ gdev update
$ gdev service pull
$ gdev service build
$ gdev service reload
```

Then restart docker for mac and run these commands:

```
# Reload project containers
$ cd /to/your/project
$ gdev reload
```


## Contributors

* [Nicholas Silva](https://github.com/silvamerica), creator.
* [Onni Hakala](https://github.com/onnimonni), forked the gdev version.

## Contributing

1. Fork it ( https://github.com/devgeniem/gdev/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request

## License

`gdev` is available under the MIT license. See the LICENSE file for more info.

Copyright 2016 Geniem Oy.
