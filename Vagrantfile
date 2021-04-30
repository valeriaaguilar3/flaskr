# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

Vagrant.configure("2") do |config|
    config.vm.box = "bento/ubuntu-18.04"

    config.vm.network "forwarded_port", guest: 80, host: 5000

    config.vm.synced_folder ".", "/vagrant_data"

    config.vm.provision "shell", inline: <<-SHELL
      apt-get update 
      apt-get upgrade 
      apt-get install -y python3-venv nginx
      mkdir /app 
      cd /app
      git clone https://github.com/valeriaaguilar3/flaskr.git
      cd flaskr
      chown vagrant:vagrant -R /app
    SHELL

    config.vm.provision "shell", privileged: false, inline: <<-SHELL
      cd /app/flaskr
      export FLASK_APP=flaskr 
      export FLASK_ENV=development
      python3 -m venv .venv 
      .venv/bin/pip install -e . 
      .venv/bin/flask init-db
    SHELL

    config.vm.provision "shell", inline: <<-SHELL
      cd /app/flaskr
      cp /app/flaskr/app /app
      cp /app/flaskr/app.service /etc/systemd/system/ 
      cp /app/flaskr/app /etc/nginx/sites-available/ 
      ln -s /etc/nginx/sites-available/app /etc/nginx/sites-enabled 
      rm /etc/nginx/sites-enabled/default 
      systemctl start app 
      systemctl enable app 
      nginx -s reload
    SHELL
  end
  
