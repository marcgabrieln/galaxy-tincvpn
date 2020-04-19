Tinc VPN
========

Simple role to keep one or more clusters of TincVPN connected nodes.

Network mode selected is "router", switch mode can be used with almost no effort

Generated RSA public keys for each node are being downloaded to the role. 

Requirements
------------
``No previous requirements.`` 

Role fully tested on:
- Debian 9
- Debian 10
- Ubuntu 18.04 
- Ubuntu 20.04

Role Variables
--------------
Networks can be separated into the inventories folder:

    inventories/{{ network_name }}/{{ network_name }}.ini
    inventories/{{ network_name }}/group_vars/members.yml
    
Simple example for .ini file:

    [members]
    vpn1 network_nodename=vpn1 ansible_host=192.168.156.11
    vpn2 network_nodename=vpn2 ansible_host=192.168.156.12
    
    [removed]
    vpn3 network_nodename=vpn3 ansible_host=192.168.156.13
    # To remove a node from a cluster
    # hostname ansible_host=ip_address_access_first_time
      
    [all:vars]
    ansible_user=root # if you have sudo users just use "become: yes"
   
A vars file all.yml for the inventory may be needed to override default values

    network_name="tinc_network"
    network_port=655
    network_compression=10 # observed best performance: LZ0-fast
    network_mask=255.255.255.0
    network_base=192.168.156
    network_interface="/dev/net/tun"

Simple .yml structure for "inventories/{{ network_name }}/group_vars/members.yml":

    ---
    node_list:
      vpn1:
        public_address: vpn1
        network_address: "{{ network_base }}.11"
        connect_to:
          - name: vpn2
          - name: vpn3
        is_public: False
    
      vpn2:
        public_address: vpn2
        network_address: "{{ network_base }}.12"
        connect_to:
          - name: vpn1
          - name: vpn3
        is_public: False
    
      vpn3:
        public_address: vpn3
        network_address: "{{ network_base }}.13"
        connect_to:
          - name: vpn1
          - name: vpn2
        is_public: False


Dependencies
------------

Role is installing "ifconfig" due to and observed bad routing behaviour using "ip" command


License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
