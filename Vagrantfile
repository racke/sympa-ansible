# Vagrant configuration file for a development VM
# Works with vmware fusion and virtual box providers

# Define variables used for Ansible provisioning
ansible_inventory_path = "environments/local/inventory"
ansible_groups = {
  "sympa" => ["local-sympa"],
  "apache" => ["local-sympa"],
  "postfix" => ["local-sympa"],
}

Vagrant.configure("2") do |config|

    # The VM Image to use. Find others at: http://vagrantcloud.com
    # Note: The "nocm" version of this box does not have any puppet software installed
# TODO : tester avec "ubuntu/xenial64"
    config.vm.box = "puppetlabs/ubuntu-16.04-64-nocm"

    # VM: "local-sympa"
    config.vm.define "local-sympa" do |conf|
        conf.vm.hostname = "local-sympa"
        # Give it a fixed IP
        conf.vm.network "private_network", ip: "192.168.66.67", :netmask => "255.255.255.0"
        conf.vm.provider "vmware_fusion" do |v|
            v.vmx["memsize"] = "1024"
        end
        conf.vm.provider "virtualbox" do |v|
            v.memory = 1024
        end
    end

#    # VM: "dev-vm2"
#    config.vm.define "dev-vm2" do |conf|
#        conf.vm.hostname = "dev-vm2"
#        # Give it a fixed IP
#        conf.vm.network "private_network", ip: "192.168.66.67", :netmask => "255.255.255.0"
#        conf.vm.provider "vmware_fusion" do |v|
#            v.vmx["memsize"] = "1024"
#        end
#        conf.vm.provider "virtualbox" do |v|
#            v.memory = 1024
#        end
#    end

    config.vm.provision "ansible" do |ansible|
        # Groups to add to the Ansible inventory file generated by Vagrant
        # For use by ansible-playbook later.
        # These are not used by provision.yml

        ansible.groups = ansible_groups
        ansible.playbook = "create_inventory.yml"
        ansible.limit="all"
        ansible.inventory_path="environments/setup.ini"
        #ansible.verbose = "vvvv" # For troubleshooting ansible connection problems

        # create inventory file
        if File.dirname(ansible_inventory_path) != "."
          Dir.mkdir(File.dirname(ansible_inventory_path)) unless Dir.exist?(File.dirname(ansible_inventory_path))
        end
        File.open(ansible_inventory_path, 'w') do |f|
          # Add host configuration
          f.write "local-sympa ansible_host=192.168.66.67 ansible_user='vagrant' ansible_ssh_private_key_file='.vagrant/machines/local-sympa/virtualbox/private_key'\n"
          f.write "\n"

          # Add groups
          ansible_groups.each do |name, members|
            f.write "[#{name}]\n"
            members.each do |hostname|
              f.write "#{hostname}\n"
            end
            f.write "\n"
          end
        end
    end

end
