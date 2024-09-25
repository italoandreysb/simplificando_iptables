# Configurações básicas para linux com ip público válido

## 1. Limpar regras antigas
```
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
```

## 2. Permitir tráfego de entrada e saída para a rede 192.168.0.0/24
```
sudo iptables -A INPUT -s 192.168.0.0/24 -j ACCEPT
sudo iptables -A OUTPUT -d 192.168.0.0/24 -j ACCEPT
```

## 3. Definindo bloqueio como política padrão
```
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT
```
## 4. Permitir loopback e conexões estabelecidas
```
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```
## 5. Permitir SSH (só se for realmente necessário)
```
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```
  
## 6. Permitir HTTP e HTTPS - Caso tenha um serviço web rodando nas portas 80 e/ou 443
```
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT
```
  
## 7. Permitir ICMP (ping) - (Apenas se realmente necessário)
```
iptables -A INPUT -p icmp -j ACCEPT
```
# Salvar regras de forma persistente
```
apt install iptables-persistent
iptables-save > /etc/iptables/rules.v4
```
