
# terraform-ansible-centos8-podman

Deploy CentOS-8 podman host using Terraform and Ansible. After deployment
you are able to use [Cockpit](https://cockpit-project.org/) to manage your
CentOS host. If you are using a reverse proxy, you can enable this in the
playbook so that a nginx configuration will be generated.

![CentOS-Cockpit](/files/centos8-cockpit.png)

Requirements
------------

Any pre-requisites that is needed for a successful deployment.

 - Terraform with [libvirt](https://github.com/dmacvicar/terraform-provider-libvirt) provider
 - Ansible
 - Libvirt with KVM
 - Base CentOS template with passwordless root access using public/private SSH key
 - Netcat for checking if SSH daemon is running
 - Nginx for reverse proxying (optional)

Creating a Template Image
--------------------------

Create a template VM CentOS 8 image with SSH deamon running.
Make sure the OS image contains the public key of your main host.
This way Ansible can start the playbook once the SSH port of the VM is up.
Edit the */resources.tf files and set the source.

Default source in resources.tf is:

    source = "/var/lib/libvirt/images/CentOS-8.x86_64-kvm-and-xen.qcow2"

Tip: Keep the size of the template image as small as possible:

    dd if=/dev/zero of=/root/zero status=progress bs=4M
    rm -f /root/zero
    shutdown -h now

Then convert the qcow2 image using the following command:

    kvm-img convert -O qcow2 original_image.qcow2 new_image.qcow2


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
root of the Git repo.

License
-------

See LICENSE

