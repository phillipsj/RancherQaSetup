Vagrant.configure(2) do |config|
    config.vm.network "private_network", bridge: "Default Switch"
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
      winworker.vm.provision :shell, privileged: true, inline: "Set-MpPreference -DisableRealtimeMonitoring $true -DisableScriptScanning $true -DisableArchiveScanning $true -ExclusionPath c:\var\lib\rancher\rke2\bin,c:\usr\local\bin"
      winworker.vm.provision :shell, privileged: true, inline: "Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False"
    end

  end