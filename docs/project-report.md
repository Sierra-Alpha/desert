# Project

<details>
  <summary> Table of Contents</summary>
<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [Project](#project)
    - [Overview](#overview)
    - [Editor](#editor)
        - [Outcomes](#outcomes)
        - [The Process](#the-process)
            - [Issues and Solutions](#issues-and-solutions)

<!-- markdown-toc end -->
</details>

## Overview 

The goal of this project is to assist in making budget forecasts to help with personal finances.
But the technical aspect of this project is to have a look at the entire toolchain from the text editor 
used through to the deployed cloud app.

To build any software you need the right tools, and you need flexibility, the idea that you can sit at
any terminal and start coding in a way that is familiar to you gives way to this first topic the 
[Editor](#editor).

## Inspiration

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

## Editor

The issue I find is that I use different devices and these are different platforms, and the developer
experience is different on every machine, across Linux, Mac OSx and Windows I end up installing three 
different sets of tools to try and have some kind of workflow consistency across my devices. My editor 
of choice has become GNU Emacs, I'm a big fan of the opensource community and love how configurable it is
but it has one downside, it's almost too configurable for someone starting out, and as such a 
community driven customisation called Spacemacs exists. Spacemacs is combining Vim key bindings and a 
more standard setup with a set of key bindings and packages to get a user going with Emacs very quickly.
Spacemacs is the configuration of Emacs that I use.

### Outcomes

I was doing research into containerisation for a paper I was taking, 158355 Cloud and IoT paper. This
led me to discover Docker and I imediatley saw it was the answer for what I was trying to achive, VM's 
are great but heavy, and you can't always install them on every machine. Docker is much more lightweight 
and portable and mostly installable everywhere so I persued this avenue. But this approach wasn't totally
easy to setup.

### The Process

Involved lots of trial and error, Docker allows you to clone images but from a security point of view I
wanted to make sure that I knew everything that I had chosen to install and why it was configured as such. 
Below is a list of issues and solutions and as such they have all been implemented in my Dockerfile, the 
build instructions to Docker to be able to build the same container every time.

#### Issues and Solutions

 - **Spacemacs is better in a GUI**  
    Docker containers are mainly set up to host shell applications and as such the base images don't contain
    an X server for rendering GUI applications and the console connections are all text based. While emacs 
    and Spacemacs both can run in a shell the user experience these days is much nicer in a GUI.
    
    **Solution**  
    The solution is to install and Xserver onto the Docker image and a virtual network computing (VNC) server 
    to be able to remote desktop 
    in and interact with the chosen application running in a Docker container. My first avenue was to install 
    a full Desktop environment onto the linux core and a VNC server to be able to control it remotely. This
    was expensive in terms of size, around 2.5gb and consumed a lot of memory during operation, at that size
    I didn't think it would be very scaleable and needed a different approach. Then I discovered x11vnc a 
    Linux package that doesn't require a desktop environment but can just forward the Xserver output and allow
    interactions, so instead of remoting into the full desktop environment, we remote into just the chosen 
    applications window. But x11vnc isn't an Xserver so we need an Xserver for Spacemacs to render in and then
    to forward it to the VNC client, xvfb is such a package, it can make a virtual frame buffer which removes 
    the need for the desktop environment and Spacemacs can render into this virtual frame and x11vnc can serve
    this to any vnc client connecting to the VNCserver.
    
 - **Screen resolution**  
 
    **Solution**  
    
 - **Dotfiles**  
   In the unix-like world dot files are stored in a users home directory and used to store local customisations
   for user preferences for all sorts of programs, the problem is that they are not stored in a standard way 
   and in order to be able to have repeatable user experience we want to be able to store them in a git 
   repository which also allows for verioning if a new configuration doesn't result in desired affect for 
   example.
   
   **Solution**  
   Fortunatley the GNU team are to our rescue again, with GNU Stow. Stow is a symlink farm manager. What this
   means is that you can set up a standard folder with a specific stow structure and it will move your dotfiles 
   into this folder then create and symlink back to the original location for the programs to reference. The 
   advantage here is that the dotfiles folder can be managed by git for versioning and pushed to a public repo
   on GitHub for replicating to any machine that has internet access.

 - **Build Automation**  
    Docker is wonderful but you can end up needing to pass in a lot of commands to build the images securely.
    
    **Solution**  


 - **Security**  
    Write some stuff here
    
   **Solution**  
   And some more stuff here 


___

matapihi todos here:


   [*] add git keys to GH on run
   [ ] Change https pull files to ssh
   [ ] git add . git commit -m "some message on save of .files" git push file 
       watcher program (periodically poll for repo changes)
   [ ] Make additions to bash RC in another file and touch to end rather than ovewiritng it
   [ ] inception pull in DevSetup repo to be able to tweak and push locally
   [ ] add user to dockerfile
   [ ] Do security of vnc and perhaps no vnc
   [ ] setup read in 
   [ ] Do everything else I want with it automated to push to the repo so I make my 
       environment how I want and it keeps it updated online
   [ ] Spawning new windows per GH branch


Kainga Readme

TLDR

desription - continued

quick start

intstall

configure

use cases

features

more info

#################

Project report

Development Process

Seperation of concerns

Security considerations

limitations, further improvements



#################

Matapihi Readme

TLDR

desription

quick start

intstall

configure

use cases

more info

#################

Wakahiki Readme

TLDR

desription

quick start

intstall

configure

use cases

more info

#################

Kainga Readme

TLDR

desription

quick start

intstall

configure

use cases

more info

#################

Dotfiles Readme

TLDR

desription

quick start

intstall

configure

use cases

more info
