Vagrant.require_version '>= 1.8.0'
BOX_IMAGE = "generic/ubuntu1804"

Vagrant.configure("2") do |config|

    ########### General settings ###########

    config.vm.provider "vmware" do |v|
        v.cpus = 1
        v.linked_clone = true
        v.memory = 1048
        v.customize [ "modifyvm", :id, "--uartmode1", "disconnected" ]
    end

    ######################### Splunk instance with Auditd installed ########################

    config.vm.define "auditd" do |subconfig|
        subconfig.vm.box = BOX_IMAGE
        subconfig.vm.hostname = "auditd"
        subconfig.vm.synced_folder ".", "/vagrant", disabled: true
        subconfig.vm.provision "file", source: "~/vagrant_projects/splunk_apps/linux-auditd_310.tgz", destination: "/home/vagrant/linux-auditd_310.tgz"
        subconfig.vm.provision "file", source: "~/vagrant_projects/splunk_apps/linux-auditd-technology-add-on_312.tgz", destination: "/home/vagrant/linux-auditd-technology-add-on_312.tgz"
        subconfig.vm.provision "file", source: "~/vagrant_projects/splunk_apps/splunk-common-information-model-cim_501.tgz", destination: "/home/vagrant/splunk-common-information-model-cim_501.tgz"
        subconfig.vm.provision "shell", inline: <<-SHELL

        #### BASE SPLUNK INSTALLATION ####
        sudo apt update -y && sudo apt install wget -y
        sudo wget -P /opt splunk-8.2.4-87e2dda940d1-Linux-x86_64.tgz "https://download.splunk.com/products/splunk/releases/8.2.4/linux/splunk-8.2.4-87e2dda940d1-Linux-x86_64.tgz" 
        sudo tar xvzf /opt/splunk-8.2.4-87e2dda940d1-Linux-x86_64.tgz -C /opt
        sudo adduser splunk
        sudo chown -R splunk. /opt/splunk
        sudo /opt/splunk/bin/splunk enable boot-start -user splunk -systemd-managed 1 --accept-license --no-prompt
        echo -e "[user_info] \nUSERNAME = admin \nPASSWORD = adminuser" >> /opt/splunk/etc/system/local/user-seed.conf
        # Create alias for splunk for vagrant and root user
        echo -e "alias splunk='sudo /opt/splunk/bin/splunk'" >> /home/vagrant/.bashrc
        source /home/vagrant/.bashrc
        echo -e "alias splunk='/opt/splunk/bin/splunk'" >> /root/.bashrc
        source /root/.bashrc
        # Set TZ
        sudo timedatectl set-timezone Europe/Berlin
        # Enable TLS/SSL for Splunk Web
        echo -e "[settings] \nenableSplunkWebSSL = true" >> /opt/splunk/etc/system/local/web.conf
        sudo chown -R splunk. /opt/splunk/etc/system/local/web.conf

        #### AUDITD INSTALLATION ####
        sudo apt-get -y install auditd acl
        sudo service auditd start
        # Set read permissions on /var/log/audit/; ACL not set for rotating log
        # https://docs.datadoghq.com/logs/guide/setting-file-permissions-for-rotating-logs/
        sudo setfacl -R -m u:splunk:r-x /var/log/audit/
        # Creat linux_auditd index
        echo -e "[linux_auditd] \nhomePath=/opt/splunk/var/lib/linux_auditd/db \nthawedPath=/opt/splunk/var/lib/linux_auditd/thaweddb \ncoldPath=/opt/splunk/var/lib/linux_auditd/colddb" >> /opt/splunk/etc/system/local/indexes.conf
        sudo chown -R splunk. /opt/splunk/etc/system/local/indexes.conf
        # Configure input and install TAs
        echo -e "[monitor:///var/log/audit/audit.log] \ndisabled=0 \nindex=linux_auditd" >> /opt/splunk/etc/system/local/inputs.conf
        sudo chown -R splunk. /opt/splunk/etc/system/local/inputs.conf

        sudo tar xvzf /home/vagrant/linux-auditd_310.tgz -C /opt/splunk/etc/apps
        sudo tar xvzf /home/vagrant/linux-auditd-technology-add-on_312.tgz -C /opt/splunk/etc/apps
        sudo tar xvzf /home/vagrant/splunk-common-information-model-cim_501.tgz.tgz -C /opt/splunk/etc/apps
        sudo chown -R splunk. /opt/splunk/etc/apps

        # Start Splunk
        sudo /opt/splunk/bin/splunk start
    SHELL
    end

    #################### Splunk instance with Splunk Add-on for Unix and Linux ######################
    config.vm.define "saul" do |subconfig|
        subconfig.vm.box = BOX_IMAGE
        subconfig.vm.hostname = "saul"
        subconfig.vm.synced_folder ".", "/vagrant", disabled: true
        subconfig.vm.provision "file", source: "~/vagrant_projects/splunk_apps/splunk-common-information-model-cim_501.tgz", destination: "/home/vagrant/splunk-common-information-model-cim_501.tgz"
        subconfig.vm.provision "file", source: "~/vagrant_projects/splunk_apps/splunk-add-on-for-unix-and-linux_850.tgz", destination: "/home/vagrant/splunk-add-on-for-unix-and-linux_850.tgz"
        subconfig.vm.provision "file", source: "~/vagrant_projects/splunk_apps/splunk-app-for-unix-and-linux_602.tgz", destination: "/home/vagrant/splunk-app-for-unix-and-linux_602.tgz"
        subconfig.vm.provision "shell", inline: <<-SHELL

        #### BASE SPLUNK INSTALLATION ####
        sudo apt update -y && sudo apt install wget -y
        sudo wget -P /opt splunk-8.2.4-87e2dda940d1-Linux-x86_64.tgz "https://download.splunk.com/products/splunk/releases/8.2.4/linux/splunk-8.2.4-87e2dda940d1-Linux-x86_64.tgz" 
        sudo tar xvzf /opt/splunk-8.2.4-87e2dda940d1-Linux-x86_64.tgz -C /opt
        sudo adduser splunk
        sudo chown -R splunk. /opt/splunk
        sudo /opt/splunk/bin/splunk enable boot-start -user splunk -systemd-managed 1 --accept-license --no-prompt
        echo -e "[user_info] \nUSERNAME = admin \nPASSWORD = adminuser" >> /opt/splunk/etc/system/local/user-seed.conf
        # Create alias for splunk for vagrant and root user
        echo -e "alias splunk='sudo /opt/splunk/bin/splunk'" >> /home/vagrant/.bashrc
        source /home/vagrant/.bashrc
        echo -e "alias splunk='/opt/splunk/bin/splunk'" >> /root/.bashrc
        source /root/.bashrc
        # Set TZ
        sudo timedatectl set-timezone Europe/Berlin
        # Enable TLS/SSL for Splunk Web
        echo -e "[settings] \nenableSplunkWebSSL = true" >> /opt/splunk/etc/system/local/web.conf
        sudo chown -R splunk. /opt/splunk/etc/system/local/web.conf

        #### SAUL INSTALLATION ####
        
        sudo tar xvzf /home/vagrant/splunk-add-on-for-unix-and-linux_850.tgz -C /opt/splunk/etc/apps
        sudo tar xvzf /home/vagrant/splunk-app-for-unix-and-linux_602.tgz -C /opt/splunk/etc/apps
        sudo tar xvzf /home/vagrant/splunk-common-information-model-cim_501.tgz.tgz -C /opt/splunk/etc/apps
        sudo chown -R splunk. /opt/splunk/etc/apps
       
        # Create metrics index for metrics data
        echo -e "[linux_metrics] \ndatatype=metric \nhomePath=/opt/splunk/var/lib/linux_metrics/db \nthawedPath=/opt/splunk/var/lib/linux_metrics/thaweddb \ncoldPath=/opt/splunk/var/lib/linux_metrics/colddb" >> /opt/splunk/etc/system/local/indexes.conf
        sudo chown -R splunk. /opt/splunk/etc/system/local/indexes.conf
    
        # The deployment is more time consuming with this app and I haven't found an easy way to recreate it automatically without some
        # manual input. An approach to set it up in a distributed environment:
        #   1. Install TA and App in a test environment
        #   2. Assign the linux_metrics index to the metrics inputs
        #   3. Enable all the inputs needed - this creates a local folder with the inputs.conf
        #   3. Manually add the line index=linux_os (or whatever index the data should be saved in) in EVERY monitoring stanza in the TA (local folder) 
        #      and not under [default] as this will overwrite the metrics index needed for the metrics inputs
        #       --> the linux_os and linux_metrics should be on the indexers and search heads
        #   4. With this "active" TA, deploy to UFs
        #   5. When the App is configured, deploy from deployer to SHC
        #   6. sudo setfacl -R -m u:splunk:r-x /var/log
        #   7. sudo setfacl -R -m u:splunk:r-x /var/adm
        #
        #   For this single instance, assign metrics index like above, but just let the data run into the default index (main) as
        #   it is initially (unfortunately) designed to do. Only in a produciton environment should time be taken to properly configure the index (as stated above).

        # Start Splunk
        sudo /opt/splunk/bin/splunk start
    SHELL
    end

        #################### Splunk instance with Splunk Add-on for Unix and Linux AND Auditd ######################
        config.vm.define "combo" do |subconfig|
            subconfig.vm.box = BOX_IMAGE
            subconfig.vm.hostname = "combo"
            subconfig.vm.synced_folder ".", "/vagrant", disabled: true
            subconfig.vm.provision "file", source: "~/vagrant_projects/splunk_apps/splunk-common-information-model-cim_501.tgz", destination: "/home/vagrant/splunk-common-information-model-cim_501.tgz"
            subconfig.vm.provision "file", source: "~/vagrant_projects/splunk_apps/splunk-add-on-for-unix-and-linux_850.tgz", destination: "/home/vagrant/splunk-add-on-for-unix-and-linux_850.tgz"
            subconfig.vm.provision "file", source: "~/vagrant_projects/splunk_apps/splunk-app-for-unix-and-linux_602.tgz", destination: "/home/vagrant/splunk-app-for-unix-and-linux_602.tgz"
            subconfig.vm.provision "file", source: "~/vagrant_projects/splunk_apps/linux-auditd_310.tgz", destination: "/home/vagrant/linux-auditd_310.tgz"
            subconfig.vm.provision "file", source: "~/vagrant_projects/splunk_apps/linux-auditd-technology-add-on_312.tgz", destination: "/home/vagrant/linux-auditd-technology-add-on_312.tgz"
            subconfig.vm.provision "shell", inline: <<-SHELL
    
            #### BASE SPLUNK INSTALLATION ####
            sudo apt update -y && sudo apt install wget -y
            sudo wget -P /opt splunk-8.2.4-87e2dda940d1-Linux-x86_64.tgz "https://download.splunk.com/products/splunk/releases/8.2.4/linux/splunk-8.2.4-87e2dda940d1-Linux-x86_64.tgz" 
            sudo tar xvzf /opt/splunk-8.2.4-87e2dda940d1-Linux-x86_64.tgz -C /opt
            sudo adduser splunk
            sudo chown -R splunk. /opt/splunk
            sudo /opt/splunk/bin/splunk enable boot-start -user splunk -systemd-managed 1 --accept-license --no-prompt
            echo -e "[user_info] \nUSERNAME = admin \nPASSWORD = adminuser" >> /opt/splunk/etc/system/local/user-seed.conf
            # Create alias for splunk for vagrant and root user
            echo -e "alias splunk='sudo /opt/splunk/bin/splunk'" >> /home/vagrant/.bashrc
            source /home/vagrant/.bashrc
            echo -e "alias splunk='/opt/splunk/bin/splunk'" >> /root/.bashrc
            source /root/.bashrc
            # Set TZ
            sudo timedatectl set-timezone Europe/Berlin
            # Enable TLS/SSL for Splunk Web
            echo -e "[settings] \nenableSplunkWebSSL = true" >> /opt/splunk/etc/system/local/web.conf
            sudo chown -R splunk. /opt/splunk/etc/system/local/web.conf

            #### AUDITD INSTALLATION ####
            sudo apt-get -y install auditd acl
            sudo service auditd start
            # Set read permissions on /var/log
            sudo setfacl -R -m u:splunk:r-x /var/log
            
            # The monitoring input is now given by SAUL, so the auditd events will run into the default (main) index as well

            # NOTE: I did not install the Auditd TA and app
            #sudo tar xvzf /home/vagrant/linux-auditd_310.tgz -C /opt/splunk/etc/apps
            #sudo tar xvzf /home/vagrant/linux-auditd-technology-add-on_312.tgz -C /opt/splunk/etc/apps
    
            #### SAUL INSTALLATION ####
            sudo tar xvzf /home/vagrant/splunk-add-on-for-unix-and-linux_850.tgz -C /opt/splunk/etc/apps
            sudo tar xvzf /home/vagrant/splunk-app-for-unix-and-linux_602.tgz -C /opt/splunk/etc/apps
            sudo tar xvzf /home/vagrant/splunk-common-information-model-cim_501.tgz.tgz -C /opt/splunk/etc/apps
            sudo chown -R splunk. /opt/splunk/etc/apps
           
            # Create metrics index for metrics data
            echo -e "[linux_metrics] \ndatatype=metric \nhomePath=/opt/splunk/var/lib/linux_metrics/db \nthawedPath=/opt/splunk/var/lib/linux_metrics/thaweddb \ncoldPath=/opt/splunk/var/lib/linux_metrics/colddb" >> /opt/splunk/etc/system/local/indexes.conf
            sudo chown -R splunk. /opt/splunk/etc/system/local/indexes.conf
        
            # Start Splunk
            sudo /opt/splunk/bin/splunk start
        SHELL
        end
end