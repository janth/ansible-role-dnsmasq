=======================
 Ansible Role: dnsmasq
=======================

This ansible role handles installing and configuring a Dnsmasq server to provide a DHCP server, internal DNS, and PXE boot server.

----------------
 Role Variables
----------------

Key role variables are documented with their default values below. See ``defaults/main.yml`` for a full list.

::

    dnsmasq_domain_name: ""

The domain name of the internal network you intend to run this Dnsmasq instance on.

::

    dnsmasq_dhcp_rangs:
        - network: "10.0.0.0"
          netmask: "255.255.255.0"

A list of dictionarys defining the networks which will be served by DHCP. For each dictionary, ``network`` should be the IPv4 network prefix, and ``netmask`` should be the network's netmask.

::

    dnsmasq_firewalld_zone: "internal"

Which firewall zone the various Dnsmasq services should be added to.

::
    
    dnsmasq_host_list: []

All hosts which should be served a static IP address from DHCP should appear as a dictionary in this list. Required keys in the dictionary are: ``name`` the hostname, ``mac`` the MAC address of the host, and ``address`` the IP to be assigned to the host. Optionally, setting the key ``tags:nodefroute`` will suppress the host recieving a default gateway from the DHCP server.

::
    
    dnsmasq_listen_interfaces:
        - "eth0"

List of interfaces on which DNSMasq should listen for DHCP requests.

::

    dnsmasq_provide_pxe: False

Set to true to enable the PXE server.

::

    dnsmasq_get_tftp_file: []

A list of dictionaries defining files which should be downloaded to the TFTP directory so that the PXE server can serve them. Each dictionary should define the keys ``url`` and ``dest`` for the URL to get the file from, and the path within the TFTP directory to save the file at, respectively.

::

    dnsmasq_pxe_menu: []

A list of dictionaries defining lines to appear in the PXE menu. By default, the menu will only contain a "Boot local OS" option. Required keys in each dictionary are: ``name`` the name of the PXE Option, ``label`` the label for the Option in the menu, ``kernel`` the kernel or other file to download from the PXE server and execute. The additional keys may optionally be included: ``initrd`` path to an initrd for the kernel to download from the PXE server, and ``append`` any parameters to pass to the kernel.

------------------
 Example Playbook
------------------

::

    ---
    - hosts: localhost
      vars:
        dnsmasq_domain_name: "example.com"
        dnsmasq_host_list:
            - { address: 10.0.0.11, mac: "00:11:22:33:44:55", name: node1 }
            - { address: 10.0.0.12, mac: "00:11:22:33:44:66", name: node2 }
            - { address: 10.0.0.13, mac: "00:11:22:33:44:77", name: node3, tag: nodefroute }
        dnsmasq_provide_pxe: True
        dnsmasq_get_tftp_files:
            - url: "http://example.com/centos/initrd.img"
              dest: "centos/initrd.img"
            - url: "http://example.com/centos/vmlinuz"
              dest: "centos/vmlinuz"
        dnsmasq_pxe_menu:
            - name: centos
              label: Install CentOS
              kernel: centos/vmlinuz
              initrd: centos/initrd.img
      roles:
        - dnsmasq
