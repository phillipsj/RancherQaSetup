Vagrant.configure(2) do |config|
    config.vm.network "private_network", bridge: "Default Switch"
    config.vm.synced_folder ".", "/vagrant", :disabled => true

     config.vm.define :rancher do |rancher|
          rancher.vm.host_name = "rancher"
          rancher.vm.box = "hashicorp/bionic64"

          rancher.vm.provider :virtualbox do |vb|
            vb.memory = 4096
            vb.cpus = 2
          end

          rancher.vm.provider :hyperv do |hv|
            hv.memory = 4096
            hv.cpus = 2
          end

          rancher.vm.provision :shell, privileged: true, inline: "apt-get update && apt-get install build-essential -y"
          rancher.vm.provision :shell, privileged: true, inline: "snap install docker"
          rancher.vm.provision :shell, privileged: true, inline: "snap install go --classic"
          rancher.vm.provision :shell, privileged: true, inline: "curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash"
          rancher.vm.provision :shell, privileged: true, inline: "git clone https://github.com/phillipsj/rancher.git"

          rancher.vm.provision :shell, privileged: true, inline: "k3d cluster delete rancher"
          rancher.vm.provision :shell, privileged: true, inline: "k3d cluster create rancher --k3s-server-arg \"--kube-proxy-arg=conntrack-max-per-core=0\" --k3s-agent-arg \"--kube-proxy-arg=conntrack-max-per-core=0\" --image=docker.io/rancher/k3s:v1.21.3-k3s1 --api-port=$(hostname -I | awk '{print $1}'):6443"

          rancher.vm.provision :shell, privileged: true, inline: "git clone https://github.com/rancher/machine.git"
          rancher.vm.provision :shell, privileged: true, inline: "cd machine && go build -o rancher-machine cmd/rancher-machine/machine.go"
          rancher.vm.provision :shell, privileged: true, inline: "cp /home/vagrant/machine/rancher-machine /usr/local/bin/rancher-machine"

          rancher.vm.provision :shell, privileged: true, inline: "k3d kubeconfig write rancher"

          rancher.vm.provision :shell, privileged: true, inline: "cd /home/vagrant/rancher && git checkout bugfix/rke2-windows-no-system-agent"
          rancher.vm.provision :shell, privileged: true, inline: "cd /home/vagrant/rancher && go get"
          rancher.vm.provision :shell, privileged: true, inline: "cd /home/vagrant/rancher && go build"

          rancher.vm.provision :shell, privileged: true, inline: "mkdir bin"
          rancher.vm.provision :shell, privileged: true, inline: "cp /home/vagrant/rancher/rancher /home/vagrant/bin/rancher"
          rancher.vm.provision :shell, privileged: true, inline: "CATTLE_BOOTSTRAP_PASSWORD=admin CATTLE_DEV_MODE=30 PATH=$PATH:/home/vagrant/bin KUBECONFIG=$HOME/.k3d/kubeconfig-rancher.yaml /home/vagrant/bin/rancher --debug --no-cacerts"
        end

    config.vm.define :etcd do |etcd|
      etcd.vm.host_name = "etcd"
      etcd.vm.box = "hashicorp/bionic64"

      etcd.vm.provider :virtualbox do |vb|
        vb.memory = 4096
        vb.cpus = 2
      end

      etcd.vm.provider :hyperv do |hv|
        hv.memory = 4096
        hv.cpus = 2
      end
    end

    config.vm.define :controlplane do |controlplane|
      controlplane.vm.host_name = "controlplane"
      controlplane.vm.box = "hashicorp/bionic64"

      controlplane.vm.provider :virtualbox do |vb|
        vb.memory = 4096
        vb.cpus = 2
      end

      controlplane.vm.provider :hyperv do |hv|
        hv.linked_clone = true
        hv.memory = 4096
        hv.cpus = 2
      end
    end

    config.vm.define :worker do |worker|
      worker.vm.host_name = "worker"
      worker.vm.box = "hashicorp/bionic64"

      worker.vm.provider :virtualbox do |vb|
        vb.memory = 4096
        vb.cpus = 2
      end

      worker.vm.provider :hyperv do |hv|
        hv.linked_clone = true
        hv.memory = 4096
        hv.cpus = 2
      end
    end

    config.vm.define :winworker do |winworker|
      winworker.vm.host_name = "winworker"
      winworker.vm.box = "StefanScherer/windows_2019_docker"

      winworker.vm.provider :virtualbox do |vb|
        vb.memory = 4096
        vb.cpus = 2
        vb.gui = true
      end

      winworker.vm.provider :hyperv do |hv, override|
        hv.linked_clone = true
        hv.memory = 4096
        hv.cpus = 2
        override.vm.boot_timeout = 600
      end

      winworker.vm.provision :shell, privileged: true, inline: "Stop-Service docker"
      winworker.vm.provision :shell, privileged: true, inline: "Set-MpPreference -DisableRealtimeMonitoring $true -DisableScriptScanning $true -DisableArchiveScanning $true -ExclusionPath c:\\var\\lib\\rancher\\rke2\\bin,c:\\usr\\local\\bin"
      winworker.vm.provision :shell, privileged: true, inline: "Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False"
    end

  end