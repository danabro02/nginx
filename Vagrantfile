Vagrant.configure("2") do |config|

  config.vm.box = "base"

  config.vm.define "nginx" do |nginx|
    nginx.vm.box = "debian/bookworm64"
    nginx.vm.network "private_network", ip: "192.168.57.102"
    nginx.vm.hostname = "nginx.sistema.test"
  end
end

