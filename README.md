
# terraform-ansible-centos8-podman

Deploy CentOS-8 podman host using Packer Terraform and Ansible. After deployment
you are able to use [Cockpit](https://cockpit-project.org/) to manage your
CentOS host. If you are using a reverse proxy, you can enable this in the
playbook so that a nginx configuration will be generated.

![CentOS-Cockpit](/files/centos8-cockpit.png)

Requirements
------------

Any pre-requisites that is needed for a successful deployment.

 - Hashicorp [Packer](https://packer.io/downloads.html) tool
 - Terraform with [libvirt](https://github.com/dmacvicar/terraform-provider-libvirt) provider
 - [Ansible](https://ansible.com)
 - [Libvirt](https://libvirt.org) with KVM
 - Netcat for checking if SSH daemon is running
 - Optional: Nginx for reverse proxying

Example
-------

Execute build.sh script to start building the host.

    # ./build.sh 

To destory the VM:

    # ./build destroy
    
Reverse Proxy with Nginx
------------------------
Cockpit uses web sockets to deliver active content back and forth between 
the client and the server in real time. 

    +-------+    +-------+    +-------+
    |Browser|    |Nginx  |    |Cockpit|
    |       +--->+:443   +--->+:9090  |
    |       |    |:80    |    |       |
    +-------+    +-------+    +-------+

To enable Nginx proxy, enable the following variables in the Ansible Playbook
Open the ./ansible/playbook.yml file and modify the following settings to your
needs:

    vars:

      nginx_reverse_proxy: "true"
      nginx_allow_unencrypt: "true"
      nginx_origin_domain: "http://cockpit.home.lab"

When the Ansible playbook has finished a nginx.conf file is generated in the
root of the Git repo. You can copy the configuration file to the location
of the Nginx configs.

    cp ./nginx.conf /etc/nginx/conf.d/cockpit.conf
    systemctl restart nginx

License
-------

See LICENSE

