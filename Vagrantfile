Vagrant.configure("2") do |config|
	config.vm.define "web" do |web|
		
		web.vm.box = "ubuntu/xenial64"
		
		web.vm.hostname = "web"
		
		web.vm.network "private_network", ip: "10.0.2.3"
		
		web.vm.provider "virtualbox" do |vb|
			
			vb.memory = "512"
			
			vb.name = "web"
		
		end
		config.vm.provision "shell", inline: <<-SHELL
		sudo apt-get update
		sudo apt-get -y install apache2 
	SHELL
	end			

end
