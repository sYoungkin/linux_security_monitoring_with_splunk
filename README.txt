# Add vmware plugin in folder before vagrant up
vagrant plugin install vagrant-vmware-desktop

# Make sure the apps/TAs are download and put somewhere, 
# then just update the provision lines. E.g.:
# subconfig.vm.provision "file", source: "~/vagrant_projects/splunk_apps/linux-auditd-technology-add-on_312.tgz", destination: "/home/vagrant/linux-auditd-technology-add-on_312.tgz"
# Becomes:
# subconfig.vm.provision "file", source: "~/<your path to app>/linux-auditd-technology-add-on_<your version>.tgz", destination: "/home/vagrant/linux-auditd-technology-add-on_<your version>.tgz"
# Make sure to also update the tar commands with the app version:
# sudo tar xvzf /home/vagrant/linux-auditd-technology-add-on_<your version>.tgz -C /opt/splunk/etc/apps

