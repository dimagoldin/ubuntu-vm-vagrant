Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.ssh.forward_agent = true

  config.vm.provision :file, source: "~/.ssh/id_rsa.pub", destination: "/home/vagrant/.ssh/id_rsa.pub"
  config.vm.provision :file, source: "~/.ssh/id_rsa", destination: "/home/vagrant/.ssh/id_rsa"
  config.vm.provision :shell, inline: "cat /home/vagrant/.ssh/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"

  # Add Google Chrome repository
  config.vm.provision :shell, inline: "wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub|sudo apt-key add -"
  config.vm.provision :shell, inline: "sudo sh -c 'echo \"deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main\" > /etc/apt/sources.list.d/google.list'"

  # Update repositories
  config.vm.provision :shell, inline: "sudo apt update -y"

  # Upgrade installed packages
  config.vm.provision :shell, inline: "sudo apt upgrade -y"

  # Add Git
  config.vm.provision :shell, inline: "sudo apt install -y git"

  config.vm.provision "shell", privileged: false, inline: <<-SHELL
  eval "$(ssh-agent -s)"
  ssh-add /home/vagrant/.ssh/id_rsa
  ssh-keyscan github.com >> ~/.ssh/known_hosts
  git clone git@github.com:dimagoldin/docker-tutorial.git
SHELL

  # require plugin https://github.com/leighmcculloch/vagrant-docker-compose
  config.vagrant.plugins = "vagrant-docker-compose"
  
  # install docker and docker-compose
  config.vm.provision :docker
  config.vm.provision :docker_compose
  
  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--ioapic", "on"]
    vb.customize ["modifyvm", :id, "--memory", "4096"]
    vb.customize ["modifyvm", :id, "--cpus", "4"]
  end

  # Currently "ubuntu/bionic64" on VirtualBox requires `type: "virtualbox"`
  # to make synced folder works.
  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"

  


  # Add desktop environment
  config.vm.provision :shell, inline: "sudo apt install -y --no-install-recommends ubuntu-desktop"
  config.vm.provision :shell, inline: "sudo apt install -y --no-install-recommends virtualbox-guest-dkms virtualbox-guest-utils virtualbox-guest-x11"
  # Add `vagrant` to Administrator
  config.vm.provision :shell, inline: "sudo usermod -a -G sudo vagrant"

  # Add Google Chrome
  config.vm.provision :shell, inline: "sudo apt install -y google-chrome-stable"

  

  # Add Vscode
  config.vm.provision :shell, inline: "sudo apt install software-properties-common apt-transport-https wget"
  config.vm.provision :shell, inline: "wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -"
  config.vm.provision :shell, inline: "sudo add-apt-repository \"deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main\""
  config.vm.provision :shell, inline: "sudo apt install -y code"
  config.vm.provision "shell", privileged: false, inline: <<-SHELL
  code --install-extension vscode-icons-team.vscode-icons --force
  code --install-extension ms-azuretools.vscode-docker --force
SHELL

  # Add Java11
  config.vm.provision :shell, inline: "sudo apt install -y default-jdk"

  # Add Maven
  config.vm.provision :shell, inline: "sudo apt install -y maven"

  # Add Nodejs
  config.vm.provision "shell", privileged: false, inline: <<-SHELL
  # Install NVM
  git clone https://github.com/creationix/nvm.git ~/.nvm && cd ~/.nvm && git checkout `git describe --abbrev=0 --tags`
  source ~/.nvm/nvm.sh
  echo "source ~/.nvm/nvm.sh" >> ~/.bashrc

  # Install Node
  echo "Installing Node.js (please be patient)"
  nvm install stable &> /dev/null
  nvm alias default stable
SHELL


  
  config.vm.provision "shell", privileged: false, inline: <<-SHELL
  cd docker-tutorial
  make build
SHELL

  # Restart
  config.vm.provision :shell, inline: "sudo shutdown -r now"
end
