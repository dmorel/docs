# Quick and dirty Caddyserver / Gitlab setup

Static site + easy SSL on a reverse proxy in a handful of minutes

- needs Debian Stretch (testing as of today) to get Go >= 1.4

- install Go

        apt-get install golang
        mkdir $HOME/go
        echo export GOPATH=$HOME/go >> .zshrc
        echo export PATH=$PATH:$HOME/go/bin >> .zshrc
        
- gitlab was already installed using these sources

        deb https://packages.gitlab.com/gitlab/raspberry-pi2/debian/ jessie main
        deb-src https://packages.gitlab.com/gitlab/raspberry-pi2/debian/ jessie main

- reload the shell, then install caddy (installs in `$HOME/go/bin`)

        go get github.com/mholt/caddy
        
- allow binding to port 80 and 443 for unprivileged users

        sudo setcap cap_net_bind_service=+ep $HOME/go/bin/caddy
                
- add a Caddyfile (in the server's root dir, simplest), redirect to https is
  disabled for 1st site for now, enabled for gitlab

        http://amakuru.net, http://ns50.amakuru.net, http://www.amakuru.net {
          root /var/www/sites/static.amakuru.net
          gzip
          log /home/dmorel/.caddy/log/static.amakuru.net.access.log
          errors /home/dmorel/.caddy/log/static.amakuru.net.error.log
        }
        
        gitlab.amakuru.net {
          proxy / localhost:81 {
            proxy_header Host {host}
            proxy_header X-Forwarded-For {remote}
          }
          log /home/dmorel/.caddy/log/gitlab.amakuru.net.access.log
          errors /home/dmorel/.caddy/log/gitlab.amakuru.net.error.log
        }

- housekeeping

        mkdir -m 700 -p $HOME/.caddy/log

- adjust gitlab config to listen on localhost only (`/etc/gitlab/gitlab.rb`)

        external_url 'http://gitlab.amakuru.net:81'
        nginx['listen_addresses'] = ['127.0.0.1']
         
- run `sudo gitlab-ctl reconfigure && sudo gitlab-ctl restart`
                
- start caddy in tmux (under current uid) from the root dir, where the Caddyfile is
        
        cd /var/www/sites/static.amakuru.net
        caddy
        
- done
