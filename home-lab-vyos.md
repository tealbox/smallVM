
                           +----------------------+
                           |      Upstream        |
                           |   BGP Peer (65002)   |
                           |  192.168.10.254/24   |
                           +----------+-----------+
                                      |
                                      | VLAN 10 (tagged)
                                      |
                           +----------+-----------+
                           |       router01       |
                           |        VyOS          |
                           |      AS 65001        |
                           +----------+-----------+
                                      |
                         +------------+------------+
                         |        br0 (bridge)     |
                         | 192.168.1.5/24 (VLAN 1) |
                         | enable-vlan, MTU 9216   |
                         +------------+------------+
                                      |
             +------------------------+------------------------+
             |                        |                        |
             |                        |                        |
     native-vlan 1            native-vlan 1             (optional)
   allowed-vlan 10,20       allowed-vlan 10,20        native-vlan 10
             |                        |                        |
          +--+--+                  +--+--+                  +--+--+
          |eth2|                  |eth3|                  |ethX |
          +--+-+                  +--+-+                  +--+-+
             |                        |                        |
   ----------+------------   ---------+-----------   ----------+---------  
   VLAN 1 untagged +        VLAN 1 untagged +       VLAN 10 untagged +  
   VLAN 10/20 tagged        VLAN 10/20 tagged       (if you choose so)  
   ----------+------------   ---------+-----------   ----------+---------  
             |                        |                        |  
   ----------+------------   ---------+-----------   ----------+---------  
         NUC ESXi              Tower ESXi  
   VLAN 10/20 tagged        VLAN 10/20 tagged       
  ----------+------------   ---------+-----------   ----------+---------  
            |                        |                        |  
  ----------+------------   ---------+-----------   ----------+---------  
    VM vYos()  
    VM-linux-v10                 VM-linux-v20   
    vlan 10                      vlan 20  
    192.168.10.10/24             192.168.20.20/24  
    gw 192.168.10.1              gw 192.168.20.1  
  
      
VLAN SVIs on bridge:  
  
  br0.10  -> 192.168.10.1/24  (BGP peering, user subnet)  
  br0.20  -> 192.168.20.1/24  (another user subnet)  
  
Loopback for BGP router-id:  
  lo      -> 10.255.255.1/32  
  

  




admin@router01:~$ sh configuration commands  
**set interfaces bridge br0 address '192.168.1.5/24'  
set interfaces bridge br0 enable-vlan  
set interfaces bridge br0 member interface eth2 allowed-vlan '10'  
set interfaces bridge br0 member interface eth2 allowed-vlan '20'  
set interfaces bridge br0 member interface eth2 native-vlan '1'  
set interfaces bridge br0 member interface eth3 allowed-vlan '20'  
set interfaces bridge br0 member interface eth3 allowed-vlan '10'  
set interfaces bridge br0 member interface eth3 native-vlan '1'  
set interfaces bridge br0 mtu '9216'  
set interfaces bridge br0 vif 10 address '192.168.10.1/24'  
set interfaces bridge br0 vif 20 address '192.168.20.1/24'**  
  
set interfaces ethernet eth0 hw-id '98:b7:85:24:b4:ff'  
set interfaces ethernet eth0 mtu '**9216**'  
set interfaces ethernet eth0 offload gro  
set interfaces ethernet eth0 offload gso  
set interfaces ethernet eth0 offload sg  
set interfaces ethernet eth0 offload tso  
set interfaces ethernet eth1 hw-id '98:b7:85:24:b5:00'  
set interfaces ethernet eth1 mtu '**9216**'  
set interfaces ethernet eth1 offload gro  
set interfaces ethernet eth1 offload gso  
set interfaces ethernet eth1 offload sg  
set interfaces ethernet eth1 offload tso  
set interfaces ethernet eth2 hw-id '98:b7:85:24:b5:01'  
set interfaces ethernet eth2 mtu '**9216**'  
set interfaces ethernet eth2 offload gro  
set interfaces ethernet eth2 offload gso  
set interfaces ethernet eth2 offload sg  
set interfaces ethernet eth2 offload tso  
set interfaces ethernet eth3 hw-id '98:b7:85:24:b5:02'  
set interfaces ethernet eth3 mtu '**9216**'  
set interfaces ethernet eth3 offload gro  
set interfaces ethernet eth3 offload gso  
set interfaces ethernet eth3 offload sg  
set interfaces ethernet eth3 offload tso  
set interfaces ethernet eth4 address '192.168.1.2/24'  
set interfaces ethernet eth4 hw-id '54:bf:64:9a:9a:bf'  
set interfaces ethernet eth4 mtu '**9000**'  
set interfaces ethernet eth4 offload gro  
set interfaces ethernet eth4 offload gso  
set interfaces ethernet eth4 offload sg  
set interfaces loopback lo  
set service ntp allow-client address '127.0.0.0/8'  
set service ntp allow-client address '169.254.0.0/16'  
set service ntp allow-client address '10.0.0.0/8'  
set service ntp allow-client address '172.16.0.0/12'  
set service ntp allow-client address '192.168.0.0/16'  
set service ntp allow-client address '::1/128'  
set service ntp allow-client address 'fe80::/10'  
set service ntp allow-client address 'fc00::/7'  
set service ntp server time1.vyos.net  
set service ntp server time2.vyos.net  
set service ntp server time3.vyos.net  
set service ssh  
set system host-name 'router01'  
set system login user admin authentication encrypted-password '$6$rounds=656000$/iYp198rn6KuaqhC$1nPrmmHZLjW3E0aZtkINCQTFntZuUaMX6SUHfTMroQqm.4OyzbNtPaxZvdsnIbgEIm0yJNV2isscpISEJmylO/'  
set system login user vyos authentication encrypted-password '$6$rounds=656000$iYgp0Ci7Oy4q7usK$Q9r5rzCO7QghJDfrg84du/KVV3Egh64esGZqiL3NUbV2ET58LpWS01oLim4Jt4I/uwcKqyKNJLaPvfmfp9ac10'  
  
set system syslog local facility local7 level 'debug'  
admin@router01:~$  
