# Dev Machine Setup on Ubuntu 20.04

This should probably be an Ansible script or something but I thought I'd document the setup of my fresh dev machine

## Snaps for great justice (and convenient auto-updating)

- intellij-idea-community
- spotify
- code (Visual Studio Code)
- helm
- kubectl --channel 1.16/stable
- gimp
- slack
- node --channel 12/stable (also serverless `sudo npm install -g serverless --scripts-prepend-node-path`)
- [devoperator](https://snapcraft.io/devoperator) (includes LOTS of DevOps tools including Terraform and aws-iam-authenticator)
  - Then run:
    ```
    sudo snap alias devoperator.terraform terraform
    sudo snap alias devoperator.tf tf
    sudo snap alias devoperator.yq yq
    sudo snap alias devoperator.aws aws
    sudo snap alias devoperator.aws-iam-authenticator aws-iam-authenticator
    sudo snap alias devoperator.assume-role assume-role
    sudo snap alias devoperator.git-flow git-flow
    sudo snap alias devoperator.istioctl istioctl
    sudo snap alias devoperator.mfa mfa
    sudo snap alias devoperator.eksctl eksctl
    ```
  - I opted not to use every tool from the snap so I could control my own kubectl version for example.

## Apt packages

- vlc
- mpv
- vim
- openjdk-8-jdk
- maven
- docker.io (and `sudo usermod -aG docker myusername`)
- tilix (and remap Ctrl-Alt-T to tilix)
- jq

## Other setup

- kubectx/kubens
    ```
    sudo ln -s ~/.kubectx/kubectx /usr/local/bin/
    sudo ln -s ~/.kubectx/kubens /usr/local/bin/
    git clone https://github.com/ahmetb/kubectx.git ~/.kubectx
    COMPDIR=$(pkg-config --variable=completionsdir bash-completion)
    sudo ln -sf ~/.kubectx/completion/kubens.bash $COMPDIR/kubens
    sudo ln -sf ~/.kubectx/completion/kubectx.bash $COMPDIR/kubectx
    ```

- vte symlink to make tilix happy `sudo ln -s /etc/profile.d/vte-2.91.sh /etc/profile.d/vte.sh`

- Copy .vimrc
    ```
    " Switch off all auto-indenting
    set nocindent
    set nosmartindent
    set noautoindent
    set indentexpr=
    set viminfo='100,<1000,s100,h
    filetype indent off
    filetype plugin indent off
    ```

- Copy .bashrc
    ```
    # ~/.bashrc: executed by bash(1) for non-login shells.
    # see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
    # for examples

    # If not running interactively, don't do anything
    case $- in
        *i*) ;;
        *) return;;
    esac

    # don't put duplicate lines or lines starting with space in the history.
    # See bash(1) for more options
    HISTCONTROL=ignoreboth

    # append to the history file, don't overwrite it
    shopt -s histappend

    # for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
    HISTSIZE=
    HISTFILESIZE=

    # check the window size after each command and, if necessary,
    # update the values of LINES and COLUMNS.
    shopt -s checkwinsize

    # If set, the pattern "**" used in a pathname expansion context will
    # match all files and zero or more directories and subdirectories.
    #shopt -s globstar

    # make less more friendly for non-text input files, see lesspipe(1)
    [ -x /usr/bin/lesspipe ] && eval "$(SHELL=/bin/sh lesspipe)"

    # set variable identifying the chroot you work in (used in the prompt below)
    if [ -z "${debian_chroot:-}" ] && [ -r /etc/debian_chroot ]; then
        debian_chroot=$(cat /etc/debian_chroot)
    fi

    # set a fancy prompt (non-color, unless we know we "want" color)
    case "$TERM" in
        xterm-color|*-256color) color_prompt=yes;;
    esac

    # uncomment for a colored prompt, if the terminal has the capability; turned
    # off by default to not distract the user: the focus in a terminal window
    # should be on the output of commands, not on the prompt
    #force_color_prompt=yes

    if [ -n "$force_color_prompt" ]; then
        if [ -x /usr/bin/tput ] && tput setaf 1 >&/dev/null; then
        # We have color support; assume it's compliant with Ecma-48
        # (ISO/IEC-6429). (Lack of such support is extremely rare, and such
        # a case would tend to support setf rather than setaf.)
        color_prompt=yes
        else
        color_prompt=
        fi
    fi

    if [ "$color_prompt" = yes ]; then
        PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
    else
        PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
    fi
    unset color_prompt force_color_prompt

    # If this is an xterm set the title to user@host:dir
    case "$TERM" in
    xterm*|rxvt*)
        PS1="\[\e]0;${debian_chroot:+($debian_chroot)}\u@\h: \w\a\]$PS1"
        ;;
    *)
        ;;
    esac

    # enable color support of ls and also add handy aliases
    if [ -x /usr/bin/dircolors ]; then
        test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
        alias ls='ls --color=auto'
        #alias dir='dir --color=auto'
        #alias vdir='vdir --color=auto'

        alias grep='grep --color=auto'
        alias fgrep='fgrep --color=auto'
        alias egrep='egrep --color=auto'
    fi

    # colored GCC warnings and errors
    #export GCC_COLORS='error=01;31:warning=01;35:note=01;36:caret=01;32:locus=01:quote=01'

    # some more ls aliases
    alias ll='ls -alF'
    alias la='ls -A'
    alias l='ls -CF'

    # Add an "alert" alias for long running commands.  Use like so:
    #   sleep 10; alert
    alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'

    # Alias definitions.
    # You may want to put all your additions into a separate file like
    # ~/.bash_aliases, instead of adding them here directly.
    # See /usr/share/doc/bash-doc/examples in the bash-doc package.

    if [ -f ~/.bash_aliases ]; then
        . ~/.bash_aliases
    fi

    # enable programmable completion features (you don't need to enable
    # this, if it's already enabled in /etc/bash.bashrc and /etc/profile
    # sources /etc/bash.bashrc).
    if [ -f /etc/bash_completion ] && ! shopt -oq posix; then
        . /etc/bash_completion
    fi

    source <(kubectl completion bash)

    export EDITOR=vim

    #kubectx and kubens
    export PATH=~/.kubectx:$PATH

    source /snap/devoperator/current/eksctl.bash
    source /snap/devoperator/current/istioctl.bash

    if [ $TILIX_ID ] || [ $VTE_VERSION ]; then
            source /etc/profile.d/vte.sh
    fi
    ```

- I also copied these from my previous machine but I'll omit these for obvious reasons
  - .aws
  - .m2
  - .mozilla
  - .ssh
  - .IdeaIC2019.3

## Helm Setup

We already install helm via the snap but still need to:

  - Download the latest Helmfile release to /usr/local/bin/helmfile
    ```
    sudo wget https://github.com/roboll/helmfile/releases/download/v0.115.0/helmfile_linux_amd64 -O /usr/local/bin/helmfile
    sudo chmod +x /usr/local/bin/helmfile
    ```
  - Install helm plugins
    ```
    helm plugin install https://github.com/databus23/helm-diff
    helm plugin install https://github.com/futuresimple/helm-secrets
    helm plugin install https://github.com/hypnoglow/helm-s3
    ```

## Thrift 0.9.1 Compilation

I've since found out that it may be possible to download the old .deb package from Ubuntu Xenial and copy the binary file /usr/local/bin/thrift. However this is what I did...

```
sudo apt install automake bison flex g++ git libboost-all-dev libevent-dev libssl-dev libtool make pkg-config libcrypto++-dev curl
wget https://archive.apache.org/dist/thrift/0.9.1/thrift-0.9.1.tar.gz
tar xvf thrift-0.9.1.tar.gz
cd thrift-0.9.1
wget http://archive.ubuntu.com/ubuntu/pool/universe/t/thrift-compiler/thrift-compiler_0.9.1-2.debian.tar.xz
tar xvf thrift-compiler_0.9.1-2.debian.tar.xz
cd compiler
patch -p1 < ../debian/patches/thrift-2247-binary-as-string-keys.patch
patch -p1 < ../debian/patches/gcc6.patch
cd ../
./configure --prefix /opt/thrift-0.9.1 --without-python --without-cpp --without-nodejs -without-py3
make
sudo make install
sudo ln -s /opt/thrift-0.9.1/bin/thrift /usr/local/bin/
thrift --version
```
