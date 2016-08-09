# gdev

## What is this for?

Helper script for all your container related development needs. This is for locally developing containerized applications in Geniem.

## Quick installation

Install gdev dependencies and start development environment by running:

    $ curl -fsSL https://raw.githubusercontent.com/devgeniem/gdev/master/bin/bootstrap | bash

## Start project containers

Start a new shell and cd to a project that uses [Docker Compose](https://docs.docker.com/compose/)

    $ gdev up

## Short list of usual commands
```
# This is similiar to vagrant up
# It reads docker-compose.yml from current directory and starts up containers
$ gdev up

# Open shell into web container
$ gdev shell

# List all containers from project
$ gdev ps

# List all containers from dlite machine
$ docker ps -a

# Open bash into any container
$ docker exec -it $CONTAINER_ID bash
```

## Step-By-Step Setup

### Terminology

**Host** or **host machine** refers to your physical computer.

**Docker Machine**, **virtual machine**, **guest**, **guest machine**, **guest VM**, etc., all refer to the virtual machine that runs the Docker server and Docker containers.

**Containers** or **Docker containers** refer to the individual services running inside of the virtual machine. Mac OS cannot natively run Docker containers (yet), so they have to be run inside of a VM.

### 1. Run the bootstrap script

This script will install the following:

- Homebrew with XCode Command Line Tools
- Ansible
- Docker
- Dlite
- Docker Compose

and will create:

- A DLite machine VM for running docker
- An include in your shell profile (bash, zsh or fish)
- A DNS resolver configuration pointing .test domains to your VM

It should run idempotently, meaning you should be able to run it as many times as you want and it won't hurt anything. If it fails due to a temporary condition (like network issues), running it again should pick up where it left off. If new items are added to the script, running it against a functioning environment should only add the new things.

    bash <(curl -fsSL https://raw.githubusercontent.com/devgeniem/gdev/master/bin/bootstrap)

After a successful installation, you can run the bootstrap again or you can instead run:

    gdev update

### 2. Start working on a project with a Docker Compose file.

The DevTool script mostly aliases docker-compose for convenience, as well as dlite. It enforces some arguments on commands that we found to be more like defaults, as well as just being less characters to type.

    # Start a new shell (The Docker client needs some ENV)
    # cd to a project with a docker-compose.yml file
    gdev up

## Alternate Setup

It is also fairly easy to configure all of this manually. The configuration files are pretty simple and easy to replicate. Most of the `gdev` tool are just shortcuts or wrappers to existing Docker commands.

## Workflow

- The source code running inside a project container is loaded from the directory on your hard drive. You can use text editors and Git clients on the host machines, and shouldn't need to work in the guest machine or the container.
- You should not need to run any application code directly from your host machine. Try to force yourself to find a containerized way of accomplishing things.
- Your SSH identify from the host machine will be forwarded into the guest machine.
- Run `gdev` without any arguments for lots of help

### Troubleshooting

####

#### When in doubt, restart the VM

Many performance-related issues can be solved by restarting the VM and your containers with `gdev machine restart` then `gdev up`. We have also seen issues with clock drift between the host and guest. Restarting the VM will re-sync the clock.

#### NFS Mount Issues

Dlite can't share paths in `/etc/exports` before doing the installation. Remove anything which is using /Users/$USER/ before installation. This is little limiting at the moment but you can restore contents of `/etc/exports` after installing dlite.

#### Network related problems

If you can ssh to docker-machine but can't run `docker-compose`, `docker` or `gdev up` it's usually sign of network interface problem.

Dash setup doesn't play well with some VPN providers. For example Cisco anyconnect can cause you plenty of problems. You can also mess networks just by changing to different wifi.

Just leave VPN (if any) and you should be okay.

#### Users of Vagrant VMs with NFS shares

Both Docker Machine and Vagrant can use NFS to mount host volumes, however the directory path model for Docker Machine conflicts with Vagrant's. The easiest solution is to reprovision your Vagrant VMs to use the Docker model. Instead of sharing a specific project folder, share the entire /Users path. Your /etc/exports may have looked like:

    # VAGRANT-BEGIN: 501 dcef4988-c1b1-4f46-926f-9c25d7c3d6fc
    "/Users/username/path/to/project" 192.168.50.4 -alldirs -mapall=0:80
    # VAGRANT-END: 501 dcef4988-c1b1-4f46-926f-9c25d7c3d6fc

Instead it should look like:

    # VAGRANT-BEGIN: 501 dcef4988-c1b1-4f46-926f-9c25d7c3d6fc
    "/Users" 192.168.50.4 -alldirs -mapall=0:80
    # VAGRANT-END: 501 dcef4988-c1b1-4f46-926f-9c25d7c3d6fc

as that is what Docker Machine expects.

Now the entire /Users directory gets shared into the Vagrant VM. You may then need to create a shell provisioner in your Vagrantfile that creates a symlink to keep Vagrant-style paths the same as they were before. If you don't care about the paths being exactly the same, you can just adapt to the new paths.

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
