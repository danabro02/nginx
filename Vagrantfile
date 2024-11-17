Vagrant.configure("2") do |config|

  config.vm.box = "debian/bookworm64"

  config.vm.define "nginx" do |nginx|
    nginx.vm.network "private_network", ip: "192.168.57.102"
    nginx.vm.hostname = "nginx.sistema.test"

    nginx.vm.synced_folder ".", "/vagrant", type: "rsync"

    nginx.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt install -y nginx vsftpd git openssl

      sudo mkdir -p /var/www/web/html
      if [ ! -d "/var/www/web/html/static-website-example" ]; then
        sudo git clone https://github.com/cloudacademy/static-website-example /var/www/web/html
      fi
      sudo chown -R www-data:www-data /var/www/web/html
      sudo chmod -R 755 /var/www/web
      sudo cp /vagrant/nginx /etc/nginx/sites-available/web
      sudo ln -sf /etc/nginx/sites-available/web /etc/nginx/sites-enabled/
      sudo systemctl restart nginx

      if [ -f "/vagrant/vsftpd.conf" ]; then
        sudo cp /vagrant/vsftpd.conf /etc/vsftpd.conf
      fi
      sudo mkdir -p /home/vagrant/ftp
      sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt \
        -subj "/CN=nginx.sistema.test"
      sudo systemctl restart vsftpd
    SHELL
  end
end