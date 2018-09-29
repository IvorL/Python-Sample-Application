required_plugins = ["vagrant-hostsupdater", "vagrant-berkshelf"]
required_plugins.each do |plugin|
  unless Vagrant.has_plugin?(plugin)
    puts "Installing Vagrant plugin #{plugin}"
    Vagrant::Plugin::Manager.instance.install_plugin(plugin)
    puts "Installed Vagrant plugin #{plugin}"
  end
end

variables = {"DB_HOST" => "192.168.10.101"}

def set_variables(variables)
  command = ""
  variables.each do |key, value|
    command += <<~HEREDOC
      sudo sed -i "1iexport #{key}=#{value}" ~/.bashrc
    HEREDOC
  end
  command += <<~HEREDOC
    source ~/.bashrc
  HEREDOC
end


Vagrant.configure("2") do |config|
  # Define uber_app VM
  config.vm.define("uber_app") do |uber_app|
    # Choose OS
    uber_app.vm.box = "ubuntu/xenial64"
    # Set network type and ip for VM
    uber_app.vm.network("private_network", ip: "192.168.10.100")
    # Set an alias (using hosts updater plugin)
    uber_app.hostsupdater.aliases = ["development.local"]
    # Sync Python-Sample-App folders to the VM
    uber_app.vm.synced_folder("./", "/home/ubuntu/python-app")
    # Provision the VM with python and pip (python package manager) using Chef
    uber_app.vm.provision("chef_solo") do |chef|
      chef.add_recipe("PythonCookBook::default")
    end
    # Provision the VM with nginx
    uber_app.vm.provision("chef_solo") do |chef|
      chef.add_recipe("NginxCookBook::default")
    end
  end
end
