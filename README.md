# Configurar knot-resolver e dnscrypt para Openwrt - knot-resolver e odhcp substitui o dnsmasq

## Funcionalidades
- Cache com knot resolver: possui a função prefill configurada no arquivo, que baixa um arquivo [root.zone](https://www.internic.net/domain/root.zone) e popula o cache para performance
- Knot resolver redireciona requisições dns através do dnscrypt, caso queira usar as configurações de dns do knot resolver acesse sua [documentação](https://knot-resolver.readthedocs.io/en/stable/)
- Tráfego de internet criptografado com dnscrypt
- knot resolver configurado seguindo boas práticas de [Quad9](https://docs.quad9.net/Quad9_For_Organizations/DNS_Forwarder_Best_Practices/)
  
## Segue configurações de como usá-los:

Obs: como dnsmasq será removido, é necessário um pacote, como o odhcpd ou kea dhcp, para realizar o papel do dhcp. Aqui uso o odhcp

```ash
opkg update
opkg remove dnsmasq odhcpd-ipv6only
opkg install odhcpd odhcp6c knot-resolver dnscrypt-proxy2
uci -q delete dhcp.@dnsmasq[0]
uci set dhcp.lan.dhcpv4="server"
uci set dhcp.odhcpd.maindhcp="1"
uci commit dhcp
service odhcpd restart
```
### Dnscrypt
Você pode usar as configurações do tutorial do openwrt ou use as configurações prontas para funcionar com o knot-resolver abaixo

```ash
wget https://raw.githubusercontent.com/ramonalvesmodesto2/openwrt-config/main/dnscrypt/dnscrypt-proxy.toml -O /etc/dnscrypt-proxy2/dnscrypt-proxy.toml
service dnscrypt-proxy restart
```

### Knot-resolver
As configurações padrões não funcionaram para mim, então tive que realizar algumas modificações, faça suas modificações se necessário nos arquivos desse repositório

```ash
wget https://raw.githubusercontent.com/ramonalvesmodesto2/openwrt-config/main/knot-resolver/kresd.config -O /etc/knot-resolver/kresd.config
wget https://raw.githubusercontent.com/ramonalvesmodesto2/openwrt-config/main/knot-resolver/kresd -O /etc/init.d/kresd 
service kresd restart
```

### Aplique as configurações de dns 
- wan e/ou lan `127.0.0.1`
- wan6 `::1`
