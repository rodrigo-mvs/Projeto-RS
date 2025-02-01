$$
## Router A ##########################################################
conf t

ipv6 unicast-routing

int f0/0
ip address 210.10.10.253 255.255.255.252 
ipv6 address 2100:A:A::253/64
ipv6 enable
no shut
exit

int f0/1
ip address 194.4.4.1 255.255.254.0 
ipv6 enable
no shut
exit


router bgp 2
bgp router-id 2.2.2.2
neighbor 210.10.10.254 remote-as 1
network 194.4.4.0 mask 255.255.254.0


## Router 1 ##########################################################
conf t

ipv6 unicast-routing

int f0/0
ip address 210.10.10.254 255.255.255.252 
ipv6 address 2100:A:A::254/64
ipv6 enable
ip nat outside
no shut
exit

int f0/1
ip address 10.10.5.1 255.255.255.0
ip address 195.5.5.39 255.255.255.240 secondary
ipv6 address 2200:0:0:05::1/64
ipv6 enable
ipv6 ospf 1 area 0
ip nat inside
no shut
exit

int f1/0
ip address 10.10.7.7 255.255.255.0
ipv6 address 2200:0:0:07::7/64
ipv6 enable
ipv6 ospf 1 area 0
ip nat inside
no shut
exit

int f1/1
ip address 10.10.8.7 255.255.255.0
ipv6 address 2200:0:0:08::7/64
ipv6 enable
ipv6 ospf 1 area 0
ip nat inside
no shut
exit

NATPOOL 195.5.5.80 195.5.5.87 netmask 255.255.255.248
ip nat inside source list 2 pool NATPOOL overload
access-list 2 permit 10.10.0.0 0.0.255.255

router ospf 1
network 10.10.5.0 0.0.0.255 area 0
network 10.10.7.0 0.0.0.255 area 0
network 10.10.8.0 0.0.0.255 area 0
network 195.5.5.0 0.0.0.127 area 0
redistribute bgp 1 subnets

ipv6 router ospf 1
router-id 1.1.1.1

router bgp 1
bgp router-id 1.1.1.1
neighbor 210.10.10.253 remote-as 2

network 10.10.1.0 mask 255.255.255.0
network 10.10.2.0 mask 255.255.255.0
network 10.10.3.0 mask 255.255.255.0
network 10.10.4.0 mask 255.255.255.0
network 10.10.5.0 mask 255.255.255.0
network 10.10.6.0 mask 255.255.255.0
network 10.10.7.0 mask 255.255.255.0
network 10.10.8.0 mask 255.255.255.0
network 195.5.5.80 mask 255.255.255.248

network 10.10.5.0 0.0.0.255 area 0
network 10.10.7.0 0.0.0.255 area 0
network 10.10.8.0 0.0.0.255 area 0
network 195.5.5.0 0.0.0.127 area 0

no aggregate-address 195.5.5.0 255.255.255.128 summary-only

## Router OB #########################################################
conf t

ipv6 unicast-routing


int f0/0
ip address 195.5.5.36 255.255.255.240 secondary
ip address 10.10.1.1 255.255.255.0
ipv6 address 2200:0:0:00::1/64
ipv6 enable
ipv6 rip 1 enable
ipv6 router ospf 1
router-id 4.4.4.4
ipv6 ospf 1 area 0
no shut

int f0/1
ip address 195.5.5.34 255.255.255.240 secondary
ip address 10.10.0.2 255.255.255.0
ipv6 address 2200:0:0:01::2/64
ipv6 enable
ipv6 rip 1 enable
no shut



router ospf 1
log-adjency-changes
redistribute rip subnets
network 10.10.1.0 0.0.0.255 area 0



ipv6 router rip 1
redistribute ospf 1 metric 1

router rip
version 2
redistribute ospf 1 metric 1
network 10.10.0.0
network 195.5.5.0

## SWL3A #############################################################
vlan database
vlan 20
vlan 30
vlan 40

conf t

ip routing
ipv6 unicast-routing

int f0/0
ip address 10.10.7.8 255.255.255.0
ipv6 address 2200:0:0:07::8/64
ipv6 enable
ipv6 ospf 1 area 0
no shut

int f0/1
ip address 10.10.6.1 255.255.255.0
ip address 195.5.5.49 255.255.255.240 secondary
ipv6 address 2200:0:0:06::01/64
ipv6 enable
ipv6 ospf 1 area 0
no shut

int VLAN 20
ip address 10.10.2.1 255.255.255.0
ipv6 address 2200:0:0:02::1/64
ipv6 enable
ipv6 ospf 1 area 0
no shut

int VLAN 30
ip address 10.10.3.1 255.255.255.0
ip address 195.5.5.20 255.255.255.224 secondary
ipv6 address 2200:0:0:03::1/64
ipv6 enable
ipv6 ospf 1 area 0
no shut

int VLAN 40 
ip address 10.10.4.1 255.255.255.0
ipv6 address 2200:0:0:04::1/64
ipv6 enable
ipv6 ospf 1 area 0
no shut

int range f1/0 - 1
switchport mode trunk
switchport trunk encapsulation dot1q
no shut

router ospf 1
network 10.10.2.0 0.0.0.255 area 0
network 10.10.3.0 0.0.0.255 area 0
network 10.10.4.0 0.0.0.255 area 0
network 10.10.6.0 0.0.0.255 area 0
network 10.10.7.0 0.0.0.255 area 0
network 195.5.5.0 0.0.0.127 area 0

ipv6 router ospf 1
router-id 2.2.2.2







conf t
int f1/1
switchport access vlan 40
mls qos trust dscp

int f1/1
switchport access vlan 40
mls qos trust dscp
end 
wr

conf t
access-list 101 permit ip 10.10.4.40 0.0.0.255 any
class-map match-all VOIP
  match access-group 101
exit
policy-map MARK-VOIP
  class VOIP
    set ip dscp 46
exit
exit
interface FastEthernet0/1
  service-policy output MARK-VOIP
exit
interface FastEthernet0/0
  service-policy output MARK-VOIP
end
wr

conf t
class-map match-all PREMIUMVOIP
 match ip dscp 46
exit
class-map match-all BEST-EFFORT
 match ip dscp 0
exit


policy-map PRIORITIZE
  class PREMIUMVOIP
    priority percent 40
  class BEST-EFFORT
    police 16000 2000 2000 conform-action set-dscp-transmit 0




## SWL3B #############################################################
vlan database
vlan 20
vlan 30
vlan 40

conf t

ip routing
ipv6 unicast-routing

int f0/0
ip address 10.10.8.8 255.255.255.0
ipv6 address 2200:0:0:08::8/64
ipv6 enable
ipv6 ospf 1 area 0
no shut

int f0/1
ip address 10.10.1.2 255.255.255.0
ip address 195.5.5.33 255.255.255.240
ipv6 enable
ipv6 rip 1 enable
no shut

int VLAN 20
ip address 10.10.2.2 255.255.255.0
ipv6 address 2200:0:0:02::2/64
ipv6 enable
ipv6 ospf 1 area 0
no shut

int VLAN 30
ip address 10.10.3.2 255.255.255.0
ip address 195.5.5.2 255.255.255.224 secondary
ipv6 address 2200:0:0:03::2/64
ipv6 enable
ipv6 ospf 1 area 0
no shut

router ospf 1
router-id 2.2.2.2
redistribute rip metric 1 subnets
network 10.10.2.0 0.0.0.255 area 0
network 10.10.3.0 0.0.0.255 area 0
network 10.10.4.0 0.0.0.255 area 0
network 10.10.8.0 0.0.0.255 area 0

router rip
redistribute rip metric 1 subnets
network 10.0.0.0

ipv6 router ospf 1
router-id 3.3.3.3
log-adjacency-changes
redistribute rip 1

ipv6 router rip 1
redistribute ospf 1








conf t
int f1/1
switchport access vlan 40
mls qos trust dscp
int f1/1
switchport access vlan 40
mls qos trust dscp
end 
wr

conf t
access-list 101 permit ip 10.10.4.40 0.0.0.255 any
class-map match-all VOIP
  match access-group 101
exit
policy-map MARK-VOIP
  class VOIP
    set ip dscp 46
exit
exit
interface FastEthernet0/1
  service-policy output MARK-VOIP
exit
interface FastEthernet0/0
  service-policy output MARK-VOIP
end
wr

conf t
class-map match-all PREMIUMVOIP
 match ip dscp 46
exit
class-map match-all BEST-EFFORT
 match ip dscp 0
exit


policy-map PRIORITIZE
  class PREMIUMVOIP
    priority percent 40
  class BEST-EFFORT
    police 16000 2000 2000 conform-action set-dscp-transmit 0




## SWITCH_Private / SWITCH_Public ####################################
port | vlan | type
  0  |  1   | dot1q
  1  |  1   | dot1q
  2  |  20  | access
  3  |  30  | access
  4  |  40  | access



## PC7 ###############################################################
ip 194.4.4.2 255.255.254.0 194.4.4.1

## PCDMZ_Private #####################################################
ip 10.10.5.32 255.255.255.0 10.10.5.1/24
ip 2200:0:0:05::32/64

## PCDMZ_Public ######################################################
ip 195.5.5.40 255.255.255.240 195.5.5.39/28
ip 2200:0:0:05::33/64

## PCOB_Private ######################################################
ip 10.10.0.20 255.255.255.0 10.10.0.2/24
ip 2200:0:0:01::20/64

## PCOB_Public #######################################################
ip 195.5.5.35 255.255.255.240 195.5.5.34/28
ip 2200:0:0:01::21/64


 ---> TODOS ESTES PCS TEEM GATEWAY NO SWL3A :
## PCMANG_Private ####################################################
ip 10.10.2.20 255.255.255.0 10.10.2.1/24
ip 2200:0:0:02::20/64

## PCMANG_Public #####################################################
ip 2200:0:0:02::21/64

## PCENG_Private #####################################################
ip 10.10.3.30 255.255.255.0 10.10.3.1/24
ip 2200:0:0:03::30/64

## PCENG_Public ######################################################
ip 195.5.5.30 255.255.255.224 195.5.5.20/27
ip 2200:0:0:03::31/64

## PCVOIP_Private ####################################################
ip 10.10.4.40 255.255.255.0 10.10.4.1/24
ip 2200:0:0:04::40/64

## PCVOIP_Public #####################################################
ip 2200:0:0:04::41/64

## PCDC_Private ######################################################
ip 10.10.6.60 255.255.255.0 10.10.6.1/24
ip 2200:0:0:06::60/64

## PCDC_Public #######################################################
ip 195.5.5.50 255.255.255.240 195.5.5.49/28
ip 2200:0:0:06::61/64





195.5.5.0  /27 - ENG

195.5.5.32 /27 ---------|-- 195.5.5.32 /28 - OB
                        |
                        |-- 195.5.5.48 /28 - DATA

195.5.5.64 /27 ---------|-- 195.5.5.64 /28 - DMZ
                        |
                        |--      ....      -- 195.5.5.80 /29 NAT


Rede   |   IPv4 Privado  |       IPv6
OB     |   10.10.1.0/24  |   2200:::01::/64
MAN    |   10.10.2.0/24  |   2200:::02::/64
ENG    |   10.10.3.0/24  |   2200:::03::/64
VOIP   |   10.10.4.0/24  |   2200:::04::/64
DMZ    |   10.10.5.0/24  |   2200:::05::/64
DC     |   10.10.6.0/24  |   2200:::06::/64



### Masks

/32  -   2      -   255.255.255.255 	 -   0.0.0.0
/31  -   6      -   255.255.255.254 	 -   0.0.0.1
/30  -   14     -   255.255.255.252 	 -   0.0.0.3
/29  -   30     -   255.255.255.248 	 -   0.0.0.7
/28  -   62     -   255.255.255.240 	 -   0.0.0.15
/27  -   126    -   255.255.255.224 	 -   0.0.0.31
/26  -   254    -   255.255.255.192 	 -   0.0.0.63
/25  -   510    -   255.255.255.128 	 -   0.0.0.127
/24  -   1022   -   255.255.255.0 	   -   0.0.0.255
/23  -   2046   -   255.255.254.0 	   -   0.0.1.255







