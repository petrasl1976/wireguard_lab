# wireguard_lab

This role setups wireguard on host and deploys its public key and available subnets to its peers.

To setup wire4guard on the host we need to know in advance couple things like peer public key, peer wireguard ip, subnets available behind peer. So to setup environment we will create variable set for each host accordingly.

 * Command below could be used to generate WG key pair:
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
wgclients:
  client: "192.168.10.0/24, 192.168.20.0/24"
wggateways:
  - vpn-gw-site-10
  - vpn-gw-site-20
```

In the example above:

wgip       - ip for wireguard. Each wireguard peer should have unique ip.

wgpub      - public wireguard key

wgpriv     - link to private wireguard key. Actual key located in vault.

wgclients  - to hosts dict listed below extra routes will be pushed.

wggateways - to hosts list listetd below no extra routes will be pushed. 


 * With command below private keys could be stored in host_vars/host/vault_wg:
```
ansible-vault create host_vars/vpn-gw-global/vault_wg
```

 * Run playbook. It will setup WG on all hosts in wireguard hostgroup:
```
ansible-playbook wireguard.yml
```

 * Hosts behind WG gateway should have extra route to it (if it is not default). Run playbook:
```
ansible-playbook sitehosts.yml
```

TODO:
 * Need to trigger WG restart when peer config added on delegated host. Dont know how to do this for now... Workaround - reread config:
 ```
 asystemctl restart wg-quick@wg0
 ```

