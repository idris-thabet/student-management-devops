Vagrant.configure("2") do |config|

  # OS
  config.vm.box = "ubuntu/jammy64"

  # VM name
  config.vm.hostname = "devops-vm"

  # Shared folder (project folder ↔ VM)
  config.vm.synced_folder ".", "/vagrant"

  # Network (access Jenkins from browser)
  config.vm.network "private_network", ip: "192.168.56.10"

  # Resources
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096"
    vb.cpus = 2
  end

  # Provisioning
  config.vm.provision "shell", inline: <<-SHELL
    sudo apt update -y

    # Java
    sudo apt install -y openjdk-17-jdk

    # Maven
    sudo apt install -y maven

    # Docker
    sudo apt install -y docker.io
    sudo systemctl start docker
    sudo systemctl enable docker
    sudo usermod -aG docker vagrant
    sudo usermod -aG docker jenkins || true

    # Jenkins
    curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
      /usr/share/keyrings/jenkins-keyring.asc > /dev/null

    echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
      https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
      /etc/apt/sources.list.d/jenkins.list > /dev/null

    sudo apt update -y
    sudo apt install -y jenkins
    sudo systemctl start jenkins
    sudo systemctl enable jenkins

    # vm.max_map_count (for SonarQube later)
    sudo sysctl -w vm.max_map_count=262144
    echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
  SHELL

end
