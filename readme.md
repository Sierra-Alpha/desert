# Draft version
---
# Kainga

A `kainga` (home/\~) away from `kaianga` (home/\~)

## TL;DR (Too Long; Didn't Read)

A containerised Xserver with VNC over SSH tunnels. Launching a Spacemacs editor
with a preferred setup.

## Description

Kainga is the Te Reo Moari word for home, we use the following.

Kainga is a way of setting up containerised Xserver and virtual network
computing (VNC) connections over a Secure Shell (SSH) tunnel. This allows
applications that require Graphical User Interfaces (GUI) to run on a container
platform, in this case Docker, and be accessible over a VNC viewer through a SSH
tunnel.

This implementation uses Docker for the containerisation, this part is named
`matapihi`, the Te Reo Moari word for window, as it is the window into the
house, home or `~`. The container uses Xvnc to start the X server and provide
the VNC server.

Next once SSH'd into the container and port forwarding to the containers 5900
(VNC) port, you then access the container through a VNC viewer. At this point
the `matapihi` init will be launched where you can add urls to scripts to run at
the initial VNC client connection, also available is an exit script for tearing
doan an environment when the VNC exits.

The script provided in the `matapihi` init needs to call some application to run
in the foreground else the script will finish and cause the Xserver to prompt
for exiting.

In this `kaianga` use of `matapihi` we run a script hosted in my `kainga-conf`
repo that installs `wakahiki` (The Te Reo Moari word for crane, as it picks up
the scripts and builds the container with them) which reads form the
`kainga-conf` file in the `kainga-conf` repo and then sets up the environment
and launches spacemacs when ready.

### why?

Because I needed to do something for a Uni project...

But really this:

The inspiration came because I have multiple OS's that I develop on, Windows,
Linux and MacOS, both for work and home, I used to use VS Code but my remote
connection for work was over a Citrix Session and some how VS Code caused the
Citrx session to crash so I had to get used to different tools. I tried Eclipse
because most of the rest of my team used it, but I had dabbled with it at uni and
hated it, and using it for work didn't change my feelings for it. I tried Vim
because some of my uni friends had always fanboyed about it but I struggled to
get the packages downloaded through the protected work network (I now realise
that was probebly more related to proxy settings than Vim, but the ship has
sailed). One of the old beards at work (disclaimer; they don't really have a
beard, I'm more referring to their unix wizardry level) uses emacs and kept
suggesting I use that, I gave it a go and was scared away, as most people are,
then in time I got used to it, then I fell to the _evil_ side combining Vim
keybindings with emacs power, and then it was time to delve into the beautiful
preconfigured emacs that is spacemacs. I knew within hours of trying it that I
wanted to use it as my development tool, I set up configuring it in my Linux
environment and loved it, I went to use it on my Windows environment and at
first glance everything seemed to be the same out of the box which was very
promising.

However there was a snag, not everything worked as well in windows,
Spacemacs/Emacs functions that relied on unix commands like grep didn't work in
the windows ecosystem, they required different configuration, but I wanted one
.emacs to rule them all I didn't want to have a linux configuration, a windows
configuration and then a MacOS configuration.

It was around now that I had discovered containerisation for another Uni paper I
was taking on cloud and IoT. It seemed a Docker container that could run on all
machines that housed the emacs/spacemeacs configuration that suited me was the
answer, there were some initial hurdles, such as getting a GUI on a container
you can read about the whole process at the [project
process docs](./docs/project-process.md) if you are interested.

## current submodule versions

matapihi 0.1.1  
wakahiki 0.1.4  

## Quick Start/Install

You: "Ahh you mentioned SSH tunelling, Containerisation, VNC and GUI all in one
sentence up there, I just want what you have without the hassle!"

Me: "Sure just do this:"

You can get up and runnning using my config and you'll be able to use the system
to get a feel for how you could customise it and use it as your own.

First you'll need to install [Docker](https://docs.docker.com/get-docker/),
once you've done that you can run the container by using the command:

```shell
docker run -p 22000 --name matapihi sierraalpha/matapihi:0.1.1
```

Then we should do some security things, open up a terminal of your choice, ssh
into the container with the following:

```shell
ssh -p 22000 matapihi@localhost
```
Follow the prompts using `initial` as the first password to enter the system, it
will ask you to enter the current password `initial` again and then select a new
password, it will then log out of the ssh connection, connect again with the
same command and then do the following:

```shell
vncpasswd
```

Note it's not `password` this is the password for the vnc connection, it is also
set to `initial` by default but you don't need the initial one for resetting it
(because you're already logged in and an authorised user with the SSH
Connection).

Then you should do the following to regenerate the SSH keys

** Note if you get disconnected during these commands it's likely to be
   impossible to reconnect through SSH but you can still use docker to get a
   root shell and then run these commands there.

```shell
sudo su
/bin/rm -v /etc/ssh/ssh_host_*
dpkg-reconfigure openssh-server
service ssh restar
exit
exit
```
Now you should exit the SSH connection one more time (if you copy pasted the
above then it should already be done) so that we actually start using the new
keys, when you try and reconnect using the above ssh command, it will give you
the following error.

```log
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:Somereallylongandweirdlookingsha256keykdkshsije.
Please contact your system administrator.
Add correct host key in /home/<user>/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/<user>/.ssh/known_hosts:X
  remove with:
  ssh-keygen -f "/home/<user>/.ssh/known_hosts" -R "[localhost]:22000"
ECDSA host key for [localhost]:22000 has changed and you have requested strict checking.
Host key verification failed.
```

Don't worry you're the one doing something nasty, just follow it's instructions
on your local (the non container) machine about removing the old keys from the
known host and follow on below and all should be fine.

This time when we reconnect we want to use the port
forwarding aspect of SSH, this is what gives us an encrypted tunnel through to
the VNC server running on the computer.

```shell
ssh -p 22000 matapihi@localhost -L 59000:localhost:5900
```
Now go to your vnc viewer of choice, I'm currently using Real VNC but will
likely switch to tigervncviewer soon because that is the client version of the
vnc server which is installed in the container and there is a chance that it
handles dynamic screen resizing to the client size, but I digress...

In your VNC viewer set up a connection to the port we're forwarding to the
containers vnc port of 5900, in this example it's 59000 so:

`localhost:59000`

is the host/ip you want to connect to.

Now you are connected to `matapihi` the window into the Xserver so we want to
set up the programs that I use, `matapihi` will prompt for some urls that point
to scripts you want to run on start up, heres what I use, for the startup
script, I paste into the prompts the following:

```shell
https://raw.githubusercontent.com/sierra-alpha/kainga-conf/master/kainga-bootstrap
```

then for the exit script

```shell
https://raw.githubusercontent.com/sierra-alpha/kainga-conf/master/kaianga-exit
```

Now `matapihi` will run through these scripts and install python, pip and git,
then clone my `kainga-conf` repo and install `wakahiki` the multithreaded script
loader also part of this collection here. Then it will load all the config as
setout in the kainaga-conf. In the kainga-conf there will be one bit about 
loading a key to GitHub under my profile which you will want to use `q` to skip. 
After all that is 
loaded eventually it will start the emacs server and then launch emacs and
you're in.

Like everything that's good it's best to start it with a restart, (in spacemacs
the mode line won't render properly till we restart) so `ctrl` + `x`, `ctrl` + `c` to
exit emacs which will start terminating matapihi (you might need to exit or alt
tab out of the spare terminal to get to the exit prompt), follow the prompts to exit
and then reconnect the VNC client, this will reload and this time when emacs starts
then it should display correctly.

You can navigate to a terminal using `alt` + `tab` or pull one up in emacs by
using `spc` + `'` then check the output 
of the `xrandr` command and chose a resolution of your display that fits best
and change to that size by typing `wnrz <#>` where <#> is the number (starting
from 0) that matches the item in the display list you want to use. (hint if the
screen choice was incorrect and you can no longer see all of the screen to
change it back your vnc viewer likely has a scaling option you can use to see
the whole screen at an incorrect dpi while you play around with and get the
correct size, you can also add resolutions to the xrandr output, see the xrandr
docs for that here)

Happy Hacking!

## Configure

Above we walked you through how to get up and running with my config, but the
idea is for `kainga` to be your home, not mine so lets walk through how to
configure it. In the interest of seperation of concerns the various aspects that
make up this project have been seperated into their respective repos that you
can read specific guidance on at their respective repos listed after each
heading if applicable.

### Matapihi

For an in depth guide to `matapihi` see it's repo [here](https://github.com/sierra-alpha/matapihi)

Is basically an SSH server that spins up a default VNC connection on Xdisplay :0
so connection is on it's local host port of 5900 (long term goal is to spin up
different Xservers as new clients connect, raise an issue if you want this
sooner). Customisations here include which port you wish to forward, and which
of your host ports you want to use to connect to the SSH on. You may also like
to mount some of your host drives into the container to be able to work on them
from within the container, this is what I do

```shell
docker run -d -p <host port to use>:22 [--mount type=bind,source=<path/on/host>,target=<path/on/container> --name <container-name>] sierraalpha/matapihi-private:<current-version>
```

   Everything inside the `[]` are optional  
   `<host port to use>` is any unused ephemeral port, I normally use 22000 as
SSH default is 22 so it's easy to remember  
   `<path/on/host>` is an existing directory on your host that you want the
contaier to be able to access  
   `<path/on/container>` is the path inside the container where you want to access
the host directory above  
   `<container-name>` A name to easily identify this particular container instance
of the `matapihi` image  
   `<current-version>` The current version (0.1.0) of matapihi image to use  

Now that is done the SSH Tunnel has some configurable options too

```shell
ssh [-fNT] -p <host port to use> matapihi@<container-address> -L <host-port-to-fwd>:<container-address>:5900
```

   `[]` optional values to allow SSH tunnel to go to the background  
   `<host port to use>` the same value as used above  
   `<host-port-to-fwd>` The port on the host that will be forwarded to 5900 (the VNC port) 
   on matapihi, ususally I use 59000
   `<container-address>` The address of the container, most likely `localhost`, you
can probably use a different address for a machine that isn't the host if the
host is configured to accept incoming connections on the `<host port to use>`
specified earlier although this hasn't been tested yet. 

Next `matapihi` will prompt you for some urls that point to scripts that you want
to download and run, `matapihi` uses `xterm` for these terminals so pasting is
with the middle mouse button or `shift` + `insert` (also to support my macbook
air with no insert or middle mouse button you can `ctrl` + `shift` + `V` or
right click to paste), which brings us too:

### Kainga-Conf

For an in depth guide to `kainga-conf` see it's repo [here](https://github.com/sierra-alpha/kainga-conf)

The `kaianga-conf` repo contains all the configuration and scripts I want to run
when `matapihi` loads, I use `wakahiki` to load all the config, this is
specified in the `kaianga-bootstrap` file. But at this point you could load
whatever startup and exit scripts you want to run by similarly hosting them on
an url that `wget` can access from within the container. Similarily you could keep the
bootstrap and exit scripts but just change the `kaianga-conf` file to suit your
needs, the next section on `wakahiki` details more about the kind of config 

### Wakahiki

For an in depth guide to `wakahiki` see it's repo [here](https://github.com/sierra-alpha/wakahiki)

`wakahiki` is a multithreaded script loader, it uses the following format to
allow a subprocess shell to be run, it handles a priority arrangement and will
execute in multithreaded fashion where possible.

```toml
[command-group]
    pre-reqs = ["previous-command-group.scripts"]
    [[command-group.scripts]]
        no_wait = true
        prompt = true
        priority = 0
        root = true
        script = ["first", "group", "command"]
    [[command-group.scripts]]
        script = ["second", "group", "command"]
```

   `[command-group]` (required) is the given name to the group of scripts to run, it is
   also used to to determine pre requisites, you can have multiple sections
   like this  
   `pre-reqs` (optional, default=None) the name of any script groups that should be run before this one,
   optional, if excluded will not depend on any othr script groups  
   `[[command-group.scripts]]` (required at least one) this is the sub group,
   allows you to specify order of script running for related subtasks, these are
   not run on multiple threads relative to other commands in the same subgroup  
   `no_wait` (optional, default=false) wakahiki will start the command in the
   background, usefull for launching daemon processes (will ignore this if set
   to run with prompt=true)  
   `prompt` (optional default=false) will run connected to the stdout/stderr and
   stdin so users can interact with the process as need be, processes that prompt
   users may cause the program to stop if this is not set to true.  
   `priority` (optional, default=0)will run the subgroups in this order,
   duplicate values are run in an undefined order amongst themselves but still
   in order relative to other values.   
   `root` (optional default=false) will run a sudo echo command before running
   the supplied command, the supplied commands still need to use sudo as
   required but this is to try and prompt once for many scripts that may require
   it.  
   `script` (required) the scripts you want to run in a format that a python
   `subprocess.run()` would expect to recieve, for example `["bash", "-c",
   "echo", "Hello World"]`  

The second group is an example of the minimum required for wakahiki to run the
supplied scripts 

### Security

If you're super security conscious you'll wan't to inspect all the code, checkout
each of the repositories for that, and you may even like to build the Docker
container from from the Docker file, checkout `matapihi` and the
`docker_compose_with_secrets.py` script which will read a config file and pass
in the relavent passwords using files as arguments rather than paswords as args.


## Use Cases

Okay that's all great and good you say, but what would I use it for? Well as
already mentioned to have a similar environment across machines of differeing
OS's, perhaps you could set up a dev server that you can access from anywhere in
the world or even have a cloud server running that you remote into to do all
your dev work on, right there next to you're deployed web service, imagine that
a cloud dev environment that you could use wherever you have internet.

## Features

As `kainga` stands there are a few features, using Xvnc as the Xserver and the
VNC server as provided by the package tigervnc-standalone-server in the debian
repositories the emacs/spacemacs setup as
mentioned above, an xterm terminal, but you could add a browser such as firefox
to complete the environment, there is no window manager, but it uses `alttab`
for switching between windows and `GNU Stow` to help keep track of dotfiles, I
use `gitwatch` to keep track of changes and automatically push them to github
for the kainga-conf repos and dotfiles repo, and supervisord to launch and keep
gitwatch running. 

## More Info

If this has interested you and you want to know more you can see the [project
report](./docs/project-report.md) which details the journey and why decisions were made for various aspects
of the project.

## Contributing

Checkout the sponsor section of my github profile or raise an issue or submit a
PR, thanks for reading and I hope oyu enjoy!
