# Docker Swarm Cluster e HAProxy Setup

Este repositório fornece instruções para configurar e gerenciar um cluster Docker Swarm com um balanceador de carga HAProxy. O HAProxy será configurado para distribuir o tráfego entre os nós do cluster.
Pré-requisitos

* Mínimo de três máquinas para o cluster Docker Swarm (VMs ou servidores físicos) com Docker instalado.
* Uma máquina adicional para o HAProxy.
* Conhecimento básico em Docker e HAProxy.

# Configuração Inicial do Cluster

## Inicialização do Swarm

Inicialize o Swarm no Manager Principal:

```
 docker swarm init --advertise-addr 192.168.18.172
```
Substitua o IP pelo IP do seu docker-manager1.

### Adição de Nós ao Cluster

Obtenha o Token de Worker e Manager:

```
docker swarm join-token worker
docker swarm join-token manager

```
### Adicione os Workers e Managers:

Execute o comando de join nos nós docker-manager2 e docker-worker.

## Configuração do HAProxy

# Prepare a Máquina do HAProxy (haproxy-server):
  
```
 Endereço IP: 192.168.18.175.
```
### Configuração do HAProxy:
Crie um arquivo de configuração haproxy.cfg com o seguinte conteúdo:
```
    haproxy

    global
        log /dev/log local0
        log /dev/log local1 notice
        chroot /var/lib/haproxy
        user haproxy
        group haproxy
        daemon

    defaults
        log     global
        mode    http
        option  httplog
        timeout connect 5000ms
        timeout client  50000ms
        timeout server  50000ms

    frontend http_front
       bind *:80
       stats uri /haproxy?stats
       default_backend http_back

    backend http_back
       balance roundrobin
       server docker-manager1 192.168.18.172:80 check
       server docker-manager2 192.168.18.171:80 check
       server docker-worker 192.168.18.170:80 check

```
Este exemplo configura o HAProxy para balancear o tráfego HTTP entre os nós do Swarm.

### Inicie o HAProxy com a Nova Configuração.

Gerenciamento do Cluster

(Seguem as mesmas instruções de gerenciamento do cluster Docker Swarm conforme a seção anterior.)
Recomendações

    Configure o monitoramento e alertas para o HAProxy e os nós do Docker Swarm.
    Teste a configuração em um ambiente de teste antes de aplicar em produção.

Contribuição

Contribuições para melhorar a configuração, scripts, e documentação são bem-vindas.
