Vagrant.configure("2") do |config|
  config.vm.define "puppetserver" do |puppetserver|
    $serverscript = <-SCRIPT
	echo "Enable passeger repo"
    curl --fail -sSLo /etc/yum.repos.d/passenger.repo https://oss-binaries.phusionpassenger.com/yum/definitions/el-passenger.repo
    echo "Install NTP"
    yum -y install ntpdate
    echo "Done with NTP installation"
    echo "Syncing time.."
    ntpdate 0.centos.pool.ntp.org
    echo "Done syncing.."
    echo "Adding puppet repository"
    rpm -Uvh https://yum.puppetlabs.com/puppet5/puppet5-release-el-7.noarch.rpm
    echo "Done adding puppet repository"
    echo "Installing Puppet server"
    yum install -y puppetserver
    echo "Done install Puppet server"
    echo "Update the Memory setting"
    sed -i 's/2g/1g/g' /etc/sysconfig/puppetserver
    echo "Done.."
    echo "Start Pupppet server"
    systemctl start puppetserver
    systemctl enable puppetserver
    echo "Enabled puppet to start on bootup"
    echo "Install Dependencies"
    yum install -y ruby ruby-devel rubygems
    sudo gem install r10k
    yum -y  install git
    echo "Done installing deps"
	SCRIPT
    puppetserver.vm.box = "centos/7"
    puppetserver.vm.network "public_network", :bridge => "Ethernet adapter VirtualBox Host-Only Network #3"
    puppetserver.vm.network "forwarded_port", guest:8140, host:8140
    puppetserver.vm.provider "virtualbox" do |v|
      v.name = "puppet-server"
      v.memory = 2048
      v.cpus   = 2
    end
	puppetserver.vm.provision "shell", inline: $serverscript	 
  end
  config.vm.define "puppetagent" do |puppetagent|
    $agentscript = <-SCRIPT
	echo "Enable repo on agent"
    rpm -Uvh https://yum.puppetlabs.com/puppet5/puppet5-release-el-7.noarch.rpm
    echo "Install NTP"
    yum -y install ntpdate
    echo "Done with NTP installation"
    echo "Syncing time.."
    ntpdate 0.centos.pool.ntp.org
    echo "Done syncing.."
    echo "Explicitly setting the UTC time stamp to match with the server"
    sudo unlink /etc/localtime 
    sudo ln -s /usr/share/zoneinfo/Etc/GMT+6 /etc/localtime
    echo "Installing puppet agent"
    yum install -y puppet-agent
    echo "done installing Puppet agent"
    echo "Enable puppet to start on boot up"
    /opt/puppetlabs/bin/puppet resource service puppet ensure=running enable=true
    echo "done..."
	SCRIPT
    puppetagent.vm.box = "minimal/centos7"
    puppetagent.vm.network "public_network", :bridge => "Ethernet adapter VirtualBox Host-Only Network #3"
    puppetagent.vm.provider "virtualbox" do |v|
      v.name = "puppet-agent"
      v.memory = 1024
      v.cpus   = 1
    end
  end
end
