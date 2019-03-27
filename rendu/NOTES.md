Iptables : 



ping : 

LAN TO DMZ:

iptables -A FORWARD -s 192.168.100.0/24 -d 192.168.200.0/24 -p icmp --icmp-type 8 -j ACCEPT
iptables -A FORWARD -s 192.168.100.0/24 -d 192.168.200.0/24 -p icmp --icmp-type 0 -j ACCEPT

DMZ TO LAN :

iptables -A FORWARD -s 192.168.200.0/24 -d 192.168.100.0/24 -p icmp --icmp-type 8 -j ACCEPT
iptables -A FORWARD -s 192.168.200.0/24 -d 192.168.100.0/24 -p icmp --icmp-type 0 -j ACCEPT

LAN TO WAN : 

iptables -A FORWARD -s 192.168.100.0/24 -p icmp --icmp-type 8 -o eth0 -j ACCEPT
iptables -A FORWARD -d 192.168.100.0/24 -p icmp --icmp-type 0 -i eth0 -j ACCEPT



```
iptables -A FORWARD -d 192.168.100.0/24 -i eth0 -p udp --sport 53 -m state --state ESTABLISHED -j ACCEPT
iptables -A FORWARD -s 192.168.100.0/24 -o eth0 -p udp --dport 53 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A FORWARD -d 192.168.100.0/24 -i eth0 -p tcp --sport 53 -m state --state ESTABLISHED -j ACCEPT
iptables -A FORWARD -s 192.168.100.0/24 -o eth0 -p tcp --dport 53 -m state --state NEW,ESTABLISHED -j ACCEPT
```

```
iptables -A FORWARD -s 192.168.100.0/24 -o eth0 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A FORWARD -d 192.168.100.0/24 -i eth0 -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT
iptables -A FORWARD -d 192.168.100.0/24 -o eth0 -p tcp --sport 8080 -m state --state ESTABLISHED -j ACCEPT
iptables -A FORWARD -s 192.168.100.0/24 -i eth0 -p tcp --dport 8080 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A FORWARD -s 192.168.100.0/24 -o eth0 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A FORWARD -d 192.168.100.0/24 -i eth0 -p tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT

# HTTP LAN TO DMZ
iptables -A FORWARD -s 192.168.100.0/24 -d 192.168.200.3 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT

iptables -A FORWARD -s 192.168.200.3 -d 192.168.100.0/24 -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT

iptables -A FORWARD -i eth0 -d 192.168.200.3 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT

iptables -A FORWARD -s 192.168.200.3 -o eth0 -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT

#SSH
```

```
iptables -A FORWARD -s 192.168.100.3 -d 192.168.200.3 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A FORWARD -s 192.168.200.3 -d 192.168.100.3 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT


iptables -A INPUT -s 192.168.100.3 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -d 192.168.100.3 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
```

| De Client\_in\_LAN à | OK/KO | Commentaires et explications                                 |
| :------------------- | :---: | :----------------------------------------------------------- |
| Interface DMZ du FW  |  KO   | Tous les input du firewall sont drops.                       |
| Interface LAN du FW  |  KO   | Tous les input du firewall sont drops.                       |
| Client LAN           |  OK   | Le client étant la source et la destination, le ping est permis. |
| Serveur WAN          |  OK   | Les régles que l'on a instaurées nous le permettent.         |

| De Server\_in\_DMZ à | OK/KO | Commentaires et explications                                 |
| :------------------- | :---: | :----------------------------------------------------------- |
| Interface DMZ du FW  |  KO   | Tous les input du firewall sont drops.                       |
| Interface LAN du FW  |  KO   | Tous les input du firewall sont drops.                       |
| Serveur DMZ          |  OK   | Le client étant la source et la destination, le ping est permis. |
| Serveur WAN          |  KO   | Aucune régle du FW n'autorise ce traffic.                    |

## 