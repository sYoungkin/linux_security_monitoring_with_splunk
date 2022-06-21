# Add vmware plugin in folder before vagrant up
vagrant plugin install vagrant-vmware-desktop

# unfortunately creating private networks with vmare is not as trival as with virtual box
# (in inprince the whole thing could be rewritten with virtualbox)
# That is, there is not a static IP address. After vagrant up, just "ip a" to get the address 
# and save it somewhere. PASSWORD is "vagrant". Example to connect per ssh:
#   auditd ip 192.168.142.148:
#       ssh vagrant@192.168.142.148
#   saul ip 192.168.142.158:
#       ssh vagrant@192.168.142.158
#   combo ip 192.168.142.159:
#       ssh vagrant@192.168.142.159
# Note: you can also connect with "vagrant ssh <auditd|saul|combo>"

# Make sure the apps/TAs are download and put somewhere, 
# then just update the provision lines. E.g.:
# subconfig.vm.provision "file", source: "~/vagrant_projects/splunk_apps/linux-auditd-technology-add-on_312.tgz", destination: "/home/vagrant/linux-auditd-technology-add-on_312.tgz"
# Becomes:
# subconfig.vm.provision "file", source: "<your path to app>/linux-auditd-technology-add-on_<your version>.tgz", destination: "/home/vagrant/linux-auditd-technology-add-on_<your version>.tgz"
# Make sure to also update the tar commands with the correct app version/name:
# sudo tar xvzf /home/vagrant/linux-auditd-technology-add-on_<your version>.tgz -C /opt/splunk/etc/apps

# Audit rules are found in /etc/audit/rules.d
# NOTE: do not write the rules in /etc/audit
# Restart after changes: systemctl restart auditd.service

