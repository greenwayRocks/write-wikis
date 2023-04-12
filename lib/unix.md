## Unix Library 101

**NOTE** - Just starting out - and - 101's are mostly copied!
Hugely inspired from **gotbletu's** codes.

### _ Need a `ssh-session` connection for some script work: _

```bash
    eval $(ssh-agent)
    ssh-add ~user/.ssh/id_rsa

    # do stuff e.g. git push using ssh

    eval $(ssh-agent -k)
```

### _ Tmux `zoom-in` & `zoom-out` scripts: _

```bash
    #!/bin/bash

    # Zoom in the current pane ONLY if it is not currently zoomed.
    tmux list-panes -F '#F' | grep -q Z || tmux resize-pane -Z

    # Zoom out the current pane ONLY if it is not currently zoomed.
    tmux list-panes -F '#F' | grep -q Z && tmux resize-pane -Z
```

### _ Tmux `workflow automation` script: _

```bash
  #!/bin/bash

  # Phase One : Project Creation -- if necessary --
  # Phase Two : TMUX automation

  session="dev"
  tmux has-session -t "$session" 2> /dev/null
   
  if [ $? != 0 ] ; then
    # Set up my session
   
    ## Session 'dev' creation 
    tmux new-session -d
    tmux rename "$session"

    # 1. "media-browser" window
    tmux renamew 'media'
    tmux send-keys -t 'media' 'cd /var/shared/ws && ranger' C-m

    # 2. "src" window
    tmux new-window -n 'src' -c /var/shared/ws/src -d
    tmux split-window -l 10 -t 2. -c /var/shared/ws/src -d
    tmux send-keys -t 'src.1' 'vi .' C-m
    # tmux list-panes -F '#F' | grep -q Z || tmux resize-pane -Z

    # 3. "wiki" window
    tmux new-window -n 'wiki' -c /var/shared/wiki -d
    tmux send-keys -t 'wiki' 'vi index.md' C-m

  fi

  tmux attach -t $session
```

----

### _ Capitalize 1st letter in a string ? _

```bash
  foo="bar"
  echo "$(tr '[:lower:]' '[:upper:]' <<< ${foo:0:1})${foo:1}"
  # returns 'Bar'
```

### _ FloatermSend _

```bash
" select and send command to terminal
:'<.'>FloatermSend
" Also `$ floaterm newFile` to edit a new one.
```

### _ aria2c to download torrent? _

```bash
# to see file indexes
aria2c --show-files your-torrent-file.torrent

# then use it like
aria2c --select-file=3,6 your-torrent-file.torrent

# to download magnet link
aria2c "magnet-link"
```

### _ auto detect default package manager _

```bash
find_pkm() { for i;do command -v "$i" > /dev/null 2>&1 && { printf "%s" "$i"; return 0;};done;return 1; }
PKMGR=$(find_pkm apt apt-get aptitude dnf emerge eopkg pacman zypper)
```


----

### _ Searching through history easily _

```bash
fh() {
eval $(history | fzf +s | sed 's/ *[0-9]* *//')
}

bind '"\C-F":"fh\n"'	# fzf history
```

### _ If file exists, source it! Global config idea pops up. _

```bash
# autocompletion
if [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
fi
```

### _ Requires root _

```bash
# requires root
[ "$(whoami)" != "root" ] && exec sudo -- "$0" "$@"

# usage example
printf "%b\n" "${Yellow}>>>Usage: ${Red}ssh -X username@$MY_IP:22${Color_Off}"
```

### _ Bash colors _

```bash
Color_Off='\e[0m'
Red='\e[0;31m'
Green='\e[0;32m'
Yellow='\e[0;33m'
```

### _ Description with colors _

```bash
__desc="${Red}========== Automate this or that ==========${Color_Off}
Automating some task with a handy script? Go.
https://greenwayrocks.github.io/mdwiki
"

printf "%b\n" "$__desc" | fold -s
```

### _ Some here-docs? _ 

```bash
PATH_CONFIG="/etc/samba/smb.conf"

cat <<EOF | tee -a "$PATH_CONFIG" > /dev/null
[MYUSERNAME]
  path = MYSAVEDIR/MYUSERNAME
  browseable = yes
  read only = no
  force create mode = 0660
  force directory mode = 2770
  valid users = MYUSERNAME @smbadmins
EOF
```

### _ Where is the server hosted? _

```bash
MY_IP="$(ip addr | awk '/global/ {print $1,$2}' | cut -d'/' -f1 | cut -d' ' -f2 | head -n 1)"
printf "%b\n" "${Yellow}>>>Server will be hosted at ${Red}smb://$MY_IP${Color_Off}"
```

### _ A yes/no question? _

```bash
printf "%b" "${Yellow}Enable X11 Forwarding? [y/n] ${Color_Off}"
read -r REPLY
if ! [[ $REPLY =~ ^[Yy]$ ]]; then
  exit 1
fi
```

### _ Declare variables also arrays & more _ 

```bash
# fill this

declare -A commands
```

### _ Search && Open a file or a URL with its default application? _

```bash
# use xdg-open

$ xdg-open "$(locate -ir .mp3$ | fzf)"
```

### _ A one-hit away binding? _ 

```bash
bind '"\C-F":"find /mnt/d/ws -type f | fzf | xargs nvim"'
```

