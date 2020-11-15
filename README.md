# wireguard_lab

This role setups wireguard on host and deploys its public key to peers listed in host_vars.

 * Prepare private and pub keys for each host:
```
priv=$(wg genkey)
pub=$(wg pubkey <<< $priv)
echo $priv $pub
```

 * Define host_vars for each host:

```
wgip: "10.5.0.3"
wgpub: "cQvVqtQQxrrBwNFmWF/fH3Jm2q/s10XL2xNnvzUN6wk="
wgpriv: '{{ vault_wgpriv }}'
wgallow: "{{ wgip }}, 192.168.10.0/24, 192.168.20.0/24"
gateways:
  - vpn-gw-site-10
  - vpn-gw-site-20
wgpeers:
  - vpn-gw-site-10
  - vpn-gw-site-20
  - client
```


 * Put private key to host_vars/vault_wg:
```
ansible-vault create ...
```

 * Run playbook:
```
ansible-playbook wireguard.yml -e "hosts=wireguard" -l client # just for single host
ansible-playbook wireguard.yml -e "hosts=wireguard"           # or all hosts in wireguard hostgroup
```

 * On hosts behind vpn add accordingly:
```
ip r add 10.5.0.0/24 via <vpn-gw-ip>
```

TODO:
 * need to trigger wg restart when [peer] part added to delegated host
 * ping from client reaches global gw, but not further. Meanwhile global gw able to ping hosts behind local gw
 * define gateway "subnets" in its own config rather than "wgallow" on target host
