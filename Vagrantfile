Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.ssh.forward_agent = true

# Update repositories
config.vm.provision :shell, inline: "sudo apt update -y"
# Upgrade installed packages
config.vm.provision :shell, inline: "sudo apt upgrade -y"
  
# Add Git
config.vm.provision :shell, inline: "sudo apt install -y git"
config.vm.provision :file, source: "~/.ssh/id_rsa.pub", destination: "/home/vagrant/.ssh/id_rsa.pub"
config.vm.provision :file, source: "~/.ssh/id_rsa", destination: "/home/vagrant/.ssh/id_rsa"
config.vm.provision :shell, inline: "cat /home/vagrant/.ssh/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
# Add Git Creds from host (ssh key)
config.vm.provision "shell", privileged: false, inline: <<-SHELL
  eval "$(ssh-agent -s)"
  ssh-add /home/vagrant/.ssh/id_rsa
  ssh-keyscan github.com >> ~/.ssh/known_hosts
  # git clone git@github.com:dimagoldin/docker-tutorial.git
SHELL

# Add Google Chrome repository
config.vm.provision :shell, inline: "wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub|sudo apt-key add -"
config.vm.provision :shell, inline: "sudo sh -c 'echo \"deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main\" > /etc/apt/sources.list.d/google.list'"

# Add Bash Completion
config.vm.provision :shell, inline: "sudo apt install bash-completion -y"



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
code --install-extension dbaeumer.vscode-eslint --force
code --install-extension donjayamanne.git-extension-pack --force
code --install-extension vscjava.vscode-java-pack --force
code --install-extension redhat.vscode-yaml --force
code --install-extension xabikos.JavaScriptSnippets --force
code --install-extension CoenraadS.bracket-pair-colorizer --force
code --install-extension christian-kohler.path-intellisense --force
code --install-extension zhuangtongfa.Material-theme --force
code --install-extension christian-kohler.npm-intellisense --force
code --install-extension ms-vscode-remote.remote-containers --force
code --install-extension dracula-theme.theme-dracula --force
code --install-extension tomaszbartoszewski.avro-tools --force
code --install-extension tgriesser.avro-schemas --force
code --install-extension ms-python.python --force
code --install-extension oderwat.indent-rainbow --force
code --install-extension VisualStudioExptTeam.vscodeintellicode --force
code --install-extension msjsdiag.debugger-for-chrome --force
code --install-extension mongodb.mongodb-vscode --force
code --install-extension ckolkman.vscode-postgres --force
code --install-extension Dunn.redis --force
code --install-extension ms-kubernetes-tools.vscode-kubernetes-tools --force
code --install-extension jeppeandersen.vscode-kafka --force
code --install-extension rmoff.ksql --force
code --install-extension rmoff.ksql --force
SHELL

# Add Java11
config.vm.provision :shell, inline: "sudo apt install -y default-jdk"

# Add Maven
config.vm.provision :shell, inline: "sudo apt install -y maven"

# Add Nodejs
config.vm.provision :shell, privileged: false, inline: <<-SHELL
# Install NVM
git clone https://github.com/creationix/nvm.git ~/.nvm && cd ~/.nvm && git checkout `git describe --abbrev=0 --tags`
source ~/.nvm/nvm.sh
echo "source ~/.nvm/nvm.sh" >> ~/.bashrc

# Install Node
echo "Installing Node.js (please be patient)"
nvm install stable &> /dev/null
nvm alias default stable
SHELL

# docker compose bash completion
config.vm.provision :shell, inline: "sudo curl -L https://raw.githubusercontent.com/docker/compose/1.28.5/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose"
# Install kubectl
config.vm.provision :shell, privileged: false, inline: <<-SHELL
  sudo apt-get install -y apt-transport-https gnupg2 curl
  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
  sudo apt-get update
  sudo apt-get install -y kubectl
  echo 'source <(kubectl completion bash)' >>~/.bashrc
SHELL

# Install Helm
config.vm.provision :shell, privileged: false, inline: <<-SHELL
  curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
  sudo apt-get install apt-transport-https --yes
  echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
  sudo apt-get update
  sudo apt-get install helm -y
  echo 'source <(helm completion bash)' >>~/.bashrc
SHELL

# Install K3s
config.vm.provision :shell, inline: "curl -sfL https://get.k3s.io | sh -"

config.vm.provision :shell, inline: "sudo snap install kontena-lens --classic"

# config.vm.provision "shell", privileged: false, inline: <<-SHELL
#   cd docker-tutorial
#   make build
# SHELL

  # Restart
  config.vm.provision :shell, inline: "sudo shutdown -r now"
end
