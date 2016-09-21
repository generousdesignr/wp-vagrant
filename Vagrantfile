# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

    config.vm.box = "scotch/box"
    config.vm.network "private_network", ip: "192.168.33.10"
    config.vm.hostname = "scotchbox"
    config.vm.synced_folder ".", "/var/www", :nfs => { :mount_options => ["dmode=777","fmode=666"] }

    config.vm.provision "shell", inline: <<-SHELL

        ## Install LFTP for Wordmove
        sudo apt-get install lftp

        ## Add locale settings for Wordmove
        echo "export LANGUAGE=en_US.UTF-8" >> ~/.bashrc
        echo "export LC_ALL=en_US.UTF-8" >> ~/.bashrc
        echo "export LANG=en_US.UTF-8" >> ~/.bashrc

        ## Install Wordmove
        gem install wordmove

        ## Update WP CLI
        sudo wp cli update --allow-root

        ## List of domains on same Vagrant box
        DOMAINS=("domain1" "domain2")

        ## Loop through all sites
        for ((i=0; i < ${#DOMAINS[@]}; i++)); do

            ## Current Domain
            DOMAIN=${DOMAINS[$i]}

            echo "Creating vhost config for $DOMAIN..."
            sudo cp /etc/apache2/sites-available/scotchbox.local.conf /etc/apache2/sites-available/$DOMAIN.local.conf

            echo "Updating vhost config for $DOMAIN..."
            sudo sed -i s,scotchbox.local,$DOMAIN.local,g /etc/apache2/sites-available/$DOMAIN.local.conf
            sudo sed -i s,/var/www/public,/var/www/$DOMAIN/public,g /etc/apache2/sites-available/$DOMAIN.local.conf

            echo "Enabling $DOMAIN. Will probably tell you to restart Apache..."
            sudo a2ensite $DOMAIN.local.conf

            echo "So let's restart apache..."
            sudo service apache2 restart

        done

    SHELL

end