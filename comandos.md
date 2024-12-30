$$ 1


## Router A
conf t
int f0/0
ip address 210.10.10.253 255.255.255.252 
ipv6 address 2100:A:A::253/64
no shut
exit

int f0/1
ip address 194.4.4.1 255.255.254.0 
no shut
exit



## Router 1
conf t

int f0/0
ip address 210.10.10.254 255.255.255.252 
ipv6 address 2100:A:A::254/64
no shut
exit

int f0/1
ip address 10.10.5.1 255.255.255.0
ip address 195.5.5.31 255.255.255.128 secondary
ipv6 address 2200:0:0:05::1/64
no shut
exit

int f1/0
ip address 10.10.7.7 255.255.255.0
ipv6 address 2200:0:0:07::7/64
no shut
exit

int f1/1
ip address 10.10.8.7 255.255.255.0
ipv6 address 2200:0:0:08::7/64
no shut
exit


## PC7
ip 194.4.4.2 255.255.254.0 194.4.4.1



## PCDMZ_Private
ip 10.10.5.32 255.255.255.0 10.10.5.1/24
ip 2200:0:0:05::32/64



## PCDMZ_Public
ip 195.5.5.33 255.255.255.128 195.5.5.31/25
ip 2200:0:0:05::33/64






$$
$$ 2


## SWL3A
conf t

int f0/0
ip address 10.10.7.8 255.255.255.0
ipv6 address 2200:0:0:07::8/64

int VLAN 20
ip address 10.10.2.1 255.255.255.0
ipv6 address 2200:0:0:02::1/64

int VLAN 30
ip address 10.10.3.1 255.255.255.0
ip address 195.5.5.1 255.255.255.128 secondary
ipv6 address 2200:0:0:03::1/64

VLAN 40 
ip address 10.10.4.1 255.255.255.0
ipv6 address 2200:0:0:04::1/64



## SWL3B 
conf t

int f0/0
ip address 10.10.8.8 255.255.255.0
ipv6 address 2200:0:0:08::8/64

int VLAN 20
ip address 10.10.2.2 255.255.255.0
ipv6 address 2200:0:0:02::2/64

int VLAN 30
ip address 10.10.3.2 255.255.255.0
ip address 195.5.5.2 255.255.255.128 secondary
ipv6 address 2200:0:0:03::2/64

VLAN 40
ip address 10.10.4.2 255.255.255.0
ipv6 address 2200:0:0:04::2/64



## SWITCH_Private / SWITCH_Public
port | vlan | type
  0  |  1   | dot1q
  1  |  1   | dot1q
  2  |  20  | access
  3  |  30  | access
  4  |  40  | access



 ---> TODOS OS PCS TEEM GATEWAY NO SWL3A

## PCMANG_Private
ip 10.10.2.20 255.255.255.0 10.10.2.1/24
ip 2200:0:0:02::20/64

## PCMANG_Public
ip 2200:0:0:02::21/64

## PCENG_Private
ip 10.10.3.30 255.255.255.0 10.10.3.1/24
ip 2200:0:0:03::30/64

## PCENG_Public
ip 195.5.5.30 255.255.255.128 195.5.5.1/25
ip 2200:0:0:03::31/64

## PCVOIP_Private
ip 10.10.4.40 255.255.255.0 10.10.4.1/24
ip 2200:0:0:04::40/64

## PCVOIP_Public
ip 2200:0:0:04::41/64





$$ 
$$ CONFIGURE NAT

## Router 1

int f0/0
ip nat outside
int f0/1
ip nat inside
int f1/0
ip nat inside
int f1/1
ip nat inside

NATPOOL 195.5.5.80 195.5.5.87 netmask 255.255.255.248
ip nat inside source list 2 pool NATPOOL overload
access-list 2 permit 10.10.0.0 0.0.255.255













############## FALTA DATACENTER E OLD BUILDING








### Masks

/30  - 255.255.255.252   - 2 usable IPs
/29  - 255.255.255.248   - 6 usable IPs
/28  - 255.255.255.240   - 14 usable IPs
/27  - 255.255.255.224   - 30 usable IPs
/26  - 255.255.255.192   - 62 usable IPs
/25  - 255.255.255.128   - 126 usable IPs
/24  - 255.255.255.0     - 254 usable IPs
/23  - 255.255.254.0     - 510 usable IPs
/22  - 255.255.252.0     - 1022 usable IPs
/21  - 255.255.248.0     - 2046 usable IPs
