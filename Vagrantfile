VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/trusty64"
  # Fix for - stdin: is not a tty 
  config.vm.provision :shell, privileged: true, :inline => "sudo sed -i '/tty/!s/mesg n/tty -s \\&\\& mesg n/' /root/.profile" 
  # Fix for - dpkg-preconfigure: unable to re-open stdin: No such file or directory 
  config.vm.provision :shell, privileged: true, :inline => "sudo ex +\"%s@DPkg@//DPkg\" -cwq /etc/apt/apt.conf.d/70debconf"
  config.vm.provision :shell, privileged: true, :inline => "sudo dpkg-reconfigure debconf -f noninteractive -p critical" 
  # Install some required packages 
  config.vm.provision :shell, privileged: true, :inline => "export DEBIAN_FRONTEND=noninteractive;sudo apt-get install -y git sshpass python-dev python-virtualenv libffi-dev libssl-dev"
  # Create a key for the vagrant user
  config.vm.provision :shell, privileged: false, :inline => "ssh-keygen -P '' -t rsa -b 4096 -f ~/.ssh/id_rsa"
  # Mount code into VM 
  config.vm.synced_folder ".", "/home/vagrant/ansible-101"
end
