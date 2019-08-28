# -*- mode: ruby -*-
# vim: set ft=ruby :

home = ENV['HOME']

MACHINES = {
  :otuslinux => {
    :box_name => "centos/7",
    :ip_addr => '192.168.11.101',
    :disks => {
      :sata1 => {
        :dfile => home + '/VirtualBox VMs/sata1.vdi',
        :size => 4 * 1024, # Gigabytes
        :port => 1
      },
      :sata2 => {
        :dfile => home + '/VirtualBox VMs/sata2.vdi',
        :size => 2 * 1024, # Gigabytes
        :port => 2
      },
      :sata3 => {
        :dfile => home + '/VirtualBox VMs/sata3.vdi',
        :size => 1 * 1024, # Gigabytes
        :port => 3
      },
      :sata4 => {
        :dfile => home + '/VirtualBox VMs/sata4.vdi',
        :size => 1 * 1024, # Gigabytes
        :port => 4
      }
    }
  },
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    # config.ssh.username = 'root'
    # config.ssh.password = 'vagrant'
    # config.ssh.insert_key = 'true'

    config.ssh.insert_key = false
    config.ssh.private_key_path = ['~/.vagrant.d/insecure_private_key', '~/.ssh/id_rsa']
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxname.to_s
      needsController = false

      #box.vm.network "forwarded_port", guest: 3260, host: 3260+offset

      box.vm.network "private_network", ip: boxconfig[:ip_addr]

      box.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--memory", "1024"]
        vb.customize ["modifyvm", :id, "--cpus", "2"]

        boxconfig[:disks].each do |dname, dconf|
          unless File.exist?(dconf[:dfile])
            vb.customize ['createhd', '--filename', dconf[:dfile], '--variant', 'Fixed', '--size', dconf[:size]]
            needsController =  true
          end
        end
          
        if needsController == true
          vb.customize ["storagectl", :id, "--name", "SATA", "--add", "sata", '--portcount', 4 ]
          boxconfig[:disks].each do |dname, dconf|
            vb.customize ['storageattach', :id,  '--storagectl', 'SATA', '--port', dconf[:port], '--device', 0, '--type', 'hdd', '--medium', dconf[:dfile]]
          end
        end
      end

      #box.vm.synced_folder "files/", "/storage/"
      box.vm.provision "file", source: "~gisty/.ssh/id_rsa.pub", destination: "~/.ssh/authorized_keys"

      box.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
        systemctl restart sshd.service
        # yum install -y mdadm smartmontools hdparm gdisk 
        yum install -y perl wget ncurses-devel make gcc bc bison flex elfutils-libelf-devel openssl-devel grub2
        mkdir -p ~root/Downloads
        cd ~root/Downloads
        wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.2.10.tar.xz
        tar -xvf linux-5.2.10.tar.xz
        cd linux-5.2.10
        cp -v /boot/config-3.10.0-957.12.2.el7.x86_64 .config
        echo Unpacked
        # make olddefconfig
        # sed -i "s/CONFIG_KVM_GUEST=.*/CONFIG_KVM_GUEST=y/" .config
        # sed -i "s/CONFIG_VIRTIO_PCI=.*/CONFIG_VIRTIO_PCI=y/" .config
        # sed -i "s/CONFIG_VIRTIO_PCI_LEGACY=.*/CONFIG_VIRTIO_PCI_LEGACY=y/" .config
        # sed -i "s/CONFIG_BLK_DEV_S.*/CONFIG_BLK_DEV_SD/" .config
        # sed -i "s/CONFIG_SCSI_VIRTIO=.*/CONFIG_SCSI_VIRTIO=y/" .config
        # sed -i "s/CONFIG_VIRTIO_NET=.*/CONFIG_VIRTIO_NET=y/" .config
        # sed -i "s/CONFIG_SERIAL_8250=.*/CONFIG_SERIAL_8250=y/" .config
        # sed -i "s/CONFIG_SERIAL_8250_CONSOLE=.*/CONFIG_SERIAL_8250_CONSOLE=y/" .config
        # sed -i "s/USB_SERIAL_XSENS_MT=.*/USB_SERIAL_XSENS_MT=n/" .config # с ним не собирается, лень дебажить почему
        # make -j4
        # make modules_install
        # make install
      SHELL
    end
  end
end