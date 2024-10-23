# Allow SSH from a specific IP (192.168.0.20)
iptables -A INPUT -p tcp -s 192.168.0.20 --dport 22 -j ACCEPT
iptables -A INPUT -p tcp --dport 22 -j DROP

# Limit new connections to port 80
iptables -A INPUT -p tcp --dport 80 -m state --state NEW -m limit --limit 10/s --limit-burst 20 -j ACCEPT
iptables -A INPUT -p tcp --dport 80 -m state --state NEW -j DROP

# Enable SYN cookies
echo 1 > /proc/sys/net/ipv4/tcp_syncookies

# Limit half-open connections (SYN flood protection)
iptables -A INPUT -p tcp --syn -m limit --limit 1/s --limit-burst 5 -j ACCEPT
iptables -A INPUT -p tcp --syn -j DROP

# Drop packets from addresses that exceed connection requests
iptables -A INPUT -p tcp --dport 22 -m recent --set
iptables -A INPUT -p tcp --dport 22 -m recent --update --seconds 60 --hitcount 10 -j 
