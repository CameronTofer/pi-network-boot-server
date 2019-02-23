
Vagrant.configure("2") do |config|

	config.vm.box = "debian/jessie64"
    config.vm.hostname = "pxe-server"
    config.vm.network "public_network"

	config.vm.provision :shell, inline: <<-SHELL
	    apt-get install kpartx -y

cat <<-'EOF' > /vagrant/dnsmasq.conf
port=0
log-dhcp
pxe-service=0,"Raspberry Pi Boot"
enable-tftp
tftp-root=/var/lib/tftpboot
tftp-unique-root=mac
dhcp-range=192.168.1.1,proxy
user=root
EOF

	SHELL

	config.vm.provision :shell, run: 'always', inline: <<-SHELL
		sudo kpartx -a -s /vagrant/2018-11-13-raspbian-stretch-lite.img
		mkdir /vagrant/tftpboot
		sudo mount /dev/mapper/loop0p1 /vagrant/tftpboot
	SHELL
	
	config.vm.provision :docker do |d|
		d.run "jpillora/dnsmasq", args: '--name dnsmasq --net=host -v /vagrant/dnsmasq.conf:/etc/dnsmasq.conf -v /vagrant/tftpboot:/var/lib/tftpboot --log-opt "max-size=100m" -e "HTTP_USER=foo" -e "HTTP_PASS=bar" --restart always'
	end

end
