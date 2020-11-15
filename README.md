# wireguard_lab

This role setups wireguard on host and deploys its public key and available subnets to peers

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
wgclients:
  client: "192.168.10.0/24, 192.168.20.0/24"
wggateways:
  - vpn-gw-site-10
  - vpn-gw-site-20
```
In the example above:

wgip - ip for wireguard. Each particiopant should have its own.

wgpub - public wireguard key for this host

wgpriv - private wireguard key for this host. It is located in vault.

wgclients - host clients. Extra routes will be pushed.

wggateways - host gateways. No routes will be pushed. 

 * Put private key to host_vars/<host>/vault_wg:
```
ansible-vault create ...
```

 * Run playbook:
```
ansible-playbook wireguard.yml
```

 * Hosts behind vpn should have extra route to WG gateway. Run playbook:
```
ansible-playbook sitehosts.yml
```

TODO:
 * need to trigger wg restart when [peer] added on delegated host. Dont know how to do this for now... Workaround - restart wg-quick@wg0 on hosts to reread config.
