Сначала подготовка:
```
~# yum install -y perl wget ncurses-devel make gcc bc bison flex elfutils-libelf-devel openssl-devel grub2
~# mkdir -p ~root/Downloads
~# cd ~root/Downloads
~# wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.2.10.tar.xz
~# tar -xvf linux-5.2.10.tar.xz
~# cd linux-5.2.10
~# cp -v /boot/config-3.10.0-957.12.2.el7.x86_64 .config
```

Поправить параметры в конфиге:
```
~# sed -i "s/CONFIG_KVM_GUEST=.*/CONFIG_KVM_GUEST=y/" .config
~# sed -i "s/CONFIG_VIRTIO_PCI=.*/CONFIG_VIRTIO_PCI=y/" .config
~# sed -i "s/CONFIG_VIRTIO_PCI_LEGACY=.*/CONFIG_VIRTIO_PCI_LEGACY=y/" .config
~# sed -i "s/CONFIG_BLK_DEV_S.*/CONFIG_BLK_DEV_SD/" .config
~# sed -i "s/CONFIG_SCSI_VIRTIO=.*/CONFIG_SCSI_VIRTIO=y/" .config
~# sed -i "s/CONFIG_VIRTIO_NET=.*/CONFIG_VIRTIO_NET=y/" .config
~# sed -i "s/CONFIG_SERIAL_8250=.*/CONFIG_SERIAL_8250=y/" .config
~# sed -i "s/CONFIG_SERIAL_8250_CONSOLE=.*/CONFIG_SERIAL_8250_CONSOLE=y/" .config
~# sed -i "s/USB_SERIAL_XSENS_MT=.*/USB_SERIAL_XSENS_MT=n/" .config # с ним не собирается
```

Собираем и устанавливаем:
```
~# make olddefconfig
~# make -j3
~# make modules_install
~# make install
```

Меняем дефолтную запись в граб:
```
~# grub2-set-default 0
```
