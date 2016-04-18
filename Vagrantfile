# -*- mode: ruby -*-
# vi: set ft=ruby :
$vm5_ip="192.168.56.14"

$chef1 = <<EOF
echo Installing Chef client
rpm -ivh chef-12.9.38-1.el6.x86_64.rpm
echo Installing Chef SDK
rpm -ivh chefdk-0.12.0-1.el6.x86_64.rpm
echo Create Directories for chef and ssh-keys
mkdir -p /root/{.chef,cookbooks,.ssh}
echo Create solo.rb file

cat > /root/.chef/solo.rb <<FL
log_level :debug
file_cache_path "/root/.chef/"
cookbook_path "/Share/chef-courses/cookbooks"
json_attribs "/root/.chef/runlist.json"
log_location "/Share/chef-courses/chef-solo.log"
FL
echo cd to nginx and run berks install
cd /Share/chef-courses/cookbooks/nginx
berks install
echo unpack archive 
berks package
tar -xf $(ls | grep *tar.gz) -C /Share/chef-courses/
echo Create runlist file
cat > /root/.chef/runlist.json <<FL
{ 
"run_list": ["recipe[nginx::default]", "recipe[iptables::default]"],
  "nginx": {"default_root":"/usr/share/nginx/html"} 
} 
FL
echo Run chef-solo
chef-solo -c /root/.chef/solo.rb
EOF


Vagrant.configure(2) do |config|

  config.vm.define "vm5" do |vm5_config|
    vm5_config.vm.box = "sbeliakou/centos-6.7-x86_64"
    vm5_config.vm.network "private_network", ip: $vm5_ip
    vm5_config.vm.hostname = "vm5.test.com"
    vm5_config.vm.provider "virtualbox" do |v|
		v.name = "vm5"
		v.customize ["modifyvm", :id, "--memory", 2048] 
		v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
	end
    vm5_config.vm.synced_folder "D:/Share", "/Share"
    vm5_config.vm.provision "shell", inline: $chef1
  end
end