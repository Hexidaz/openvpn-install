# OPENVPN Install Script

This project is based on / clone from [angristen](https://github.com/angristan/openvpn-install/)

This script has been tested working on:

- Ubuntu 24.04
- Ubuntu 22.04
- Ubuntu 18.04

As of now, there is no way to change the VPN local IP !

## Extras

Use VPN only for remote connection, do not force client 

- Comment `push "redirect gateway def1 bypass-dhcp"` in the file `/etc/openvpn/server/server.conf`

Set static IP per profile

- Create a new file with the profile name of the ovpn file in `/etc/openvpn/ccd`
- Add this line `ifconfig-push 10.8.0.<ip> 255.255.255.0`

Mikrotik support is not tested yet. But from the config file, it seems like it should support Mikrotik V7.8+. Although some settings must be disabled [Ref.](https://github.com/angristan/openvpn-install/issues/536):

Server

```conf
port 443
proto tcp
dev tun
user nobody
group nogroup
persist-key
persist-tun
keepalive 10 120
topology subnet
server 10.8.0.0 255.255.255.0
ifconfig-pool-persist ipp.txt
push "dhcp-option DNS 8.8.8.8"
push "dhcp-option DNS 8.8.4.4"
push "redirect-gateway def1 bypass-dhcp"
dh none
ecdh-curve prime256v1
#tls-crypt tls-crypt.key 0
crl-verify crl.pem
cert server_xxxxxxxxx.crt
key server_xxxxxxxxxx.key
#auth SHA256
cipher AES-128-CBC
ncp-ciphers AES-128-CBC
tls-server
tls-version-min 1.2
tls-cipher TLS-ECDHE-RSA-WITH-AES-128-GCM-SHA256
status /var/log/openvpn/status.log
verb 3
```

Client

```
client
proto tcp-client
remote xxxxxxxxx 443
dev tun
resolv-retry infinite
nobind
persist-key
persist-tun
remote-cert-tls server
verify-x509-name server_xxxxxxxxxxx name
#auth SHA256
auth-nocache
cipher AES-128-CBC
tls-client
tls-version-min 1.2
tls-cipher TLS-ECDHE-RSA-WITH-AES-128-GCM-SHA256
setenv opt block-outside-dns # Prevent Windows 10 DNS leak
verb 3
```
