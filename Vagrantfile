# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Enable Berkshelf support
  config.berkshelf.enabled = true

  # Define VM box to use
  config.vm.box = "precise32"
  config.vm.box_url = "http://files.vagrantup.com/precise32.box"

  # Set share folder
  config.vm.synced_folder "./" , "/var/www/lucerna/"

  # Use hostonly network with a static IP Address
  # and enable hostmanager
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.vm.define 'lucerna' do |node|
    node.vm.hostname = 'lucerna.local'
    node.vm.network :private_network, ip: '10.16.19.71'
    node.hostmanager.aliases = %w(www.lucerna.local)
  end
  config.vm.provision :hostmanager

  # Enable and configure chef solo
  config.vm.provision :chef_solo do |chef|
    chef.add_recipe "app::packages"
    chef.add_recipe "app::web_server"
    chef.add_recipe "app::vhost"
    chef.add_recipe "app::db"
    chef.json = {
      :app => {
        # Project name
        :name           => "lucerna",

        # Name of MySQL database that should be created
        :db_name        => "lucerna",

        # Optional database dump to be imported when server is provisioned
        # If the file doesn't exist, it is just ignored
        :db_dump        => "/var/www/lucerna/dump.sql",

        # Server name and alias(es) for Apache vhost
        :server_name    => "lucerna.local",
        :server_aliases => "www.lucerna.local",

        # Document root for Apache vhost
        :docroot        => "/var/www/lucerna/public_html",

        # General packages
        :packages   => %w{ git screen curl },
        
        # PHP packages
        :php_packages   => %w{ php5-mysqlnd php5-curl }
      },
      :mysql => {
        :server_root_password   => 'lucerna',
        :server_repl_password   => 'lucerna',
        :server_debian_password => 'lucerna',
        :bind_address           => '10.16.19.71',
        :allow_remote_root      => true
      }
    }
  end
end