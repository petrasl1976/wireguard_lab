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
wgip: "10.5.0.4"
wgpub: "e1JRFLwEO/uS1crt8dos1iyVvBI2YgpXPj7w/vF82j8="
wgpriv: '{{ vault_wgpriv }}'
wgpeers:
  - vpn-gw-global
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
