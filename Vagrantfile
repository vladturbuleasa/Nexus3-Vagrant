# -*- mode: ruby -*-
# vi: set ft=ruby :


$script_setupHosts = <<SCRIPT
echo "Setting up hostname..."
echo "nameserver 8.8.8.8" >> /etc/resolv.conf
#Disable IPV6
echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf
yum -y install zip unzip mc wget
SCRIPT

$script_java = <<SCRIPT
#Install Oracle JDK
wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u91-b14/jdk-8u91-linux-x64.rpm
yum -y install jdk-8u91-linux-x64.rpm
SCRIPT

$script_nexus = <<SCRIPT
adduser nexus
mkdir -p /opt/nexus
cd /opt/nexus
wget -nv -O --no-check-certificate https://sonatype-download.global.ssl.fastly.net/nexus/3/nexus-3.0.0-03-unix.tar.gz
tar -zxvf nexus-3.0.0-03-unix.tar.gz
ln -s nexus-3.0.0-03 latest
cd latest/bin
chown -R nexus:nexus /opt/nexus
./nexus start
SCRIPT

Vagrant.configure("2") do |config|
  
  config.vm.define "nexus3" do |nexus3|
     nexus3.vm.box = "bento/centos-7.2"
     nexus3.vm.hostname = "nexus3"
     nexus3.vm.synced_folder ".", "/vagrant", type: "virtualbox", disabled: true
     nexus3.vm.provision :shell, :inline => $script_setupHosts
	 nexus3.vm.provision :shell, :inline => $script_java
	 nexus3.vm.provision :shell, :inline => $script_nexus
     nexus3.vm.network "private_network", ip: "192.168.56.31", virtualbox__hostonly: true
     nexus3.vm.network "forwarded_port", guest: 22, host: 9022, id: "ssh", auto_correct: true
     nexus3.vm.provider "virtualbox" do |vm|
      vm.name = "nexus3"
			vm.customize [
							'modifyvm', :id,
							'--memory', '1024'
							
						]
		end
	end
end