# Environment Setup:

## Terminal
* ZSH
  1. Install: `sudo apt install zsh`
  2. Verify installation by running `zsh --version`
  3. Make it your default shell: `chsh -s $(which zsh)`
  4. Log out and login back again to use your new default shell.
  5. Test that it worked with `echo $SHELL`. Expected result: `/bin/zsh` or similar.
  6. Test with `$SHELL --version`.

* OhMyZsh
  ```shell
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
  ```
* Refs: [ZSH](https://gist.github.com/derhuerst/12a1558a4b408b3b2b6e), [ohmyzsh](https://github.com/robbyrussell/oh-my-zsh)

## Ruby on Rails

* RVM:
  1. Make sure `gpg2` is installed in your system:
  * if not sure run `gpg --version`, in case it is not installed run `sudo apt-get install gnupg2`
  2. Add public key: `gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB`
  3. Install rvm: `\curl -sSL https://get.rvm.io | bash -s stable`

* Ruby:
  1. Run `rvm install ruby 2.5.1`
  2. Use the follwing command to set your default ruby version `rvm --default use ruby-2.5.1`
  3. Run `rvm gemset use global`
  4. Run `echo "gem: --no-document" >> ~/.gemrc` if you don't want to install ruby docs (takes lots of time)
  5. Use `rvm list` to list all ruby versions installed useing `rvm`

* Rails: 
  1. Run `gem install rails --version=5.2.0`
  2. Run `rails -v` to make sure rails is installed correctly.

## Redis:
  1. Run `sudo apt-get update`
  2. Install Redis: `sudo apt-get install build-essential tcl`
  3. Go to `cd /tmp`
  4. Run `curl -O http://download.redis.io/redis-stable.tar.gz`
  5. Extract file `tar xzvf redis-stable.tar.gz`
  6. Go to `cd redis-stable`
  7. Run `make` - Will take some time be patient
  8. Run `make test` - Will take some time be patient
  9. Run `sudo make install`
  10. Create Dir `sudo mkdir /etc/redis`
  11. Run `sudo cp /tmp/redis-stable/redis.conf /etc/redis`
  12. Open config file to add some configs: `sudo nano /etc/redis/redis.conf`\
  In the file, find the `supervised` directive.Currently, this is set to `no`, change it to `systemd` as follows:
  ```shell
    . . .

    # If you run Redis from upstart or systemd, Redis can interact with your
    # supervision tree. Options:
    #   supervised no      - no supervision interaction
    #   supervised upstart - signal upstart by putting Redis into SIGSTOP mode
    #   supervised syste  md - signal systemd by writing READY=1 to $NOTIFY_SOCKET
    #   supervised auto    - detect upstart or systemd method based on
    #                        UPSTART_JOB or NOTIFY_SOCKET environment variables
    # Note: these supervision methods only signal "process is ready."
    #       They do not enable continuous liveness pings back to your supervisor.
    supervised systemd

    . . .
  ```
  13. Find the `dir` directory, and change it's value to `/var/lib/redis` as follows:
  ```shell
    . . .

    # The working directory.
    #
    # The DB will be written inside this directory, with the filename specified
    # above using the 'dbfilename' configuration directive.
    #
    # The Append Only File will also be created inside this directory.
    #
    # Note that you must specify a directory here, not a file name.
    dir /var/lib/redis

    . . .
  ```
  14. Save and close the file when you are finished.
  15. Create and open the `/etc/systemd/system/redis.service` file and paste the following:
  ```shell
    [Unit]
    Description=Redis In-Memory Data Store
    After=network.target

    [Service]
    User=redis
    Group=redis
    ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
    ExecStop=/usr/local/bin/redis-cli shutdown
    Restart=always

    [Install]
    WantedBy=multi-user.target
  ```
  16. Save and close the file when you are finished.
  17. Run `sudo adduser --system --group --no-create-home redis`
  18. Create Dir `sudo mkdir /var/lib/redis`
  19. Run `sudo chown redis:redis /var/lib/redis`
  20. Run `sudo chmod 770 /var/lib/redis`
  21. Start Redis Service `sudo systemctl start redis`
  22. Check if Redis running `sudo systemctl status redis`
  23. Enable Redis to start at Boot: `sudo systemctl enable redis`

* Ref: [Redis](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-redis-on-ubuntu-16-04)

## PostgreSQL
  1. Run `sudo apt-get update`
  2. Install Postgres `sudo apt-get install libpq-dev postgresql postgresql-contrib`
  3. Create a Postgres user with the **same name as your login username** using: `sudo -u postgres createuser --interactive`
  ```shell
    Enter name of role to add: *your machine name*
    Shall the new role be a superuser? (y/n) y
  ```
  4. Create a Databaase with the same username you used in the previous step: `sudo -u postgres createdb *same as login username*`
  5. Run `psql` to open postgres cli
  6. Run `\password` and make it `root`
  7. Run `\q` to exit postgres cli
