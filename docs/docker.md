# Docker: introdução e referência

Autor da síntese: J. Cesar Bertelli <jcbrtl-@-usp-.-br>

Este documento é mantido em https://jcbrtl.github.io/docs/docker

Versão do documento: 29.8.18

## Dedicatória
.

## Conteúdo
   * [Características](#características)
   * [Instalação (Docker CE, Edge release)](#instalação-docker-ce-edge-release)
      * [daemon](#daemon)
      * [Configurar grupo 'docker' para possível acesso root de um usuário](#configurar-grupo-docker-para-possível-acesso-root-de-um-usuário)
      * [Instalação dos ajudantes/manipuladores](#instalação-dos-ajudantesmanipuladores)
         * [docker-machine](#docker-machine)
         * [docker-compose](#docker-compose)
   * [Containers](#containers)
      * [Iniciar um container](#iniciar-um-container)
      * [Lendo logs:](#lendo-logs)
      * [Identificando processos:](#identificando-processos)
      * [Monitorando processos:](#monitorando-processos)
      * [Detalhes da configuração:](#detalhes-da-configuração)
      * [Conseguindo uma shell (bash) dentro do container:](#conseguindo-uma-shell-bash-dentro-do-container)
   * [Docker networks](#docker-networks)
      * [Identificando portas](#identificando-portas)
      * [Identificando IPs](#identificando-ips)
      * [Mostrar redes](#mostrar-redes)
      * [Inspecionar uma rede](#inspecionar-uma-rede)
      * [Criar uma rede](#criar-uma-rede)
      * [Ligar container a rede](#ligar-container-a-rede)
      * [Desligar container a rede](#desligar-container-a-rede)
      * [DNS](#dns)
   * [Dados (persistência)](#dados-persistência)
      * [Volumes](#volumes)
         * [Liga um volume nomeado](#liga-um-volume-nomeado)
         * [Lista os volumes existentes](#lista-os-volumes-existentes)
         * [Remove volumes não usados por container algum ('cleanup')](#remove-volumes-não-usados-por-container-algum-cleanup)
      * [Bind Mounts](#bind-mounts)
         * [Mapeando o diretorio público do nginx](#mapeando-o-diretorio-público-do-nginx)
         

## Licença

<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="Licença Creative Commons" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br />Este obra está licenciado com uma Licença <a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">Creative Commons Atribuição-NãoComercial 4.0 Internacional</a>.

https://creativecommons.org/licenses/by-nc/4.0/deed.pt_BR

## Referência
.


# Docker: introdução e referência
**Obs.: Rascunho, a terminar**

## Características

* Virtualização
* Isolamento em containers
* Facilidade de migração
* Velocidade
* Otimização de recursos

[Cloud Native Landscape](https://landscape.cncf.io)


## Instalação (Docker CE, Edge release)

### daemon
``` shell
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sh get-docker.sh
```

### Configurar grupo 'docker' para possível acesso root de um usuário
(Docker frequentemente precisa de acesso root)
``` shell
sudo usermod -aG docker julio
```

### Instalação dos ajudantes/manipuladores

#### docker-machine
``` shell
$ curl -L https://github.com/docker/machine/releases/download/v0.16.0/docker-machine-$(uname -s)-$(uname -m) -o /tmp/docker-machine &&
  sudo install /tmp/docker-machine /usr/local/bin/docker-machine
```

#### docker-compose
``` shell
$ curl -L https://github.com/docker/compose/releases/download/1.24.0-rc1/docker-compose-`uname -s`-`uname -m` -o /tmp/docker-compose &&
  sudo install /tmp/docker-compose /usr/local/bin/docker-compose
```

## Containers
Um container é uma instância de uma imagem (ou seja, de um 'app'), rodando
como um processo (ou mais) no SO. (No win/mac eles rodam numa mini-VM.)
Pode-se ter vários containers rodando uma mesma imagem
em processos separados. Pode-se adquirir imagens [aqui](https://hub.docker.com).


### Iniciar um container
Exemplo: nginx (servidor http(s)). Baixa a imagem do Hub, inicia um novo container com a imagem,
abre e encaminha o tráfego pela porta 8080 do host à porta 80 do container.
``` shell
docker container run --publish 8080:80 nginx #foreground
ou
docker container run --publish 8080:80 --detach nginx #background
```
Mais precisamente, o comando olha no cache local de imagens (docker image ls).
Se não a encontra,
procura no repositório remoto (Hub, padrão) e baixa a última versão
da imagem caso não seja especificada uma versão. Então, cria-se um
novo container baseado na imagem (cria uma camada que possibilita mudanças
em vez de uma cópia da imagem) e o prepara para o início. O comando também
atribui um IP virtual ao container, na rede privada do 'motor' do docker. Daí
abre a porta 8080 no host e encaminha o tráfego à porta 80 do container.
Por último, inicia o container usando o CMD no Dockerfile da imagem.

Obs.: pode-se omitir 'container' em alguns comandos.

Cada container tem um ID único, veja:
``` shell
docker container ls # verifique a opção -a, também, e docker ps
```

Pode-se especificar apenas parte do ID nos comandos caso não haja ambiguidade:
``` shell
docker container stop d52
```

Use --name um_nome para nomear um container:
``` shell
docker container run --publish 8080:80 --detach --name wsc nginx
```

Daí, use o nome nos comandos:
``` shell
docker container stop wsc #stop, start, restart, veja 'docker container --help'
```

### Lendo logs:
``` shell
docker container logs wsc
```

### Identificando processos:
``` shell
docker container top wsc
```

### Monitorando processos:
``` shell
docker container stats wsc
```

Comandos aceitam multiplos parâmetros:
``` shell
docker container rm -f 111f d526173 5e41
```

### Detalhes da configuração:
``` shell
docker container inspect wsc
```

### Conseguindo uma shell (bash) dentro do container:
``` shell
docker container exec -it wsc bash #existente
ou
docker container run -it --name foo nginx bash #novo

##-t aloca um pseudo-terminal
##-i sessão interativa (mantém STDIN aberta)
```
##docker container start -ai wsc


## Docker networks
### Identificando portas
``` shell
docker container port wsc
```

### Identificando IPs
Containers são ligados a uma rede virtual (bridge/docker0 (padrão)) que, por sua vez,
é ligada à interface no host, como enp7s0 ou wlp6s0, por exemplo. Portanto
containers estão numa subrede diferente da do host. De fato, pode-se criar uma nova rede virtual e ligar containers a ela, de modo a isolar containers em subredes específicas.
``` shell
docker container inspect --format '{{ .NetworkSettings.IPAddress }}' wsc
ou
docker container inspect wsc | grep IPAddress
```

### Mostrar redes
``` shell
docker network ls
```
*host* é uma rede virtual especial que liga diretamente um container à interface
no host. Ganha em performance (pois pula passos de virtualização), mas sai do modelo de segurança containerizada;
*none* não tá ligada à interface alguma no host;
*bridge* é a interface padrão do docker.

### Inspecionar uma rede
``` shell
docker network inspect *bridge* #docker0
```

### Criar uma rede
``` shell
docker network create avp_net
```

### Ligar container a rede
``` shell
docker network connect avp_net wsc
```

### Desligar container a rede
``` shell
docker network disconnect bridge wsc
```

### DNS
Docker usa o nome do container para a comunicação inter-container em novas
redes


## Dados (persistência)
Containers foram concebidos para serem imutáveis, mas temporários. Se uma modificação
não-trivial é feita, a boa prática sugere para gerar um novo container com
a modificação, exceto em caso de dados gerados pelo funcionamento do app/server
ou em caso de necessidade de dados persistentes ao funcionamento, como
num servidor http(s) ou num SGBD.

### Volumes
Cria um volume fora do UFS do container. Há persistência de dados 
ao longo de remoções de containers, e um volume preexistente pode ser ligado a outros
containers. Volumes aparecem como arquivos comuns no host, em
/var/lib/docker/volumes/.

#### Liga um volume nomeado
Use a opção -v nome_do_vol_no_host:caminho_no_container
``` shell
docker container run -d --name dbc -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v dbv:/var/lib/mysql mysql
```
Se precisa de um driver diferente do padrão, crie o volume de antemão
com *docker volume create*

#### Lista os volumes existentes
``` shell
docker volume ls
```

#### Remove volumes não usados por container algum ('cleanup')
``` shell
docker volume prune
```

### Bind Mounts
Mapeia um diretório (ou arquivo) do host a um diretório (ou arquivo) no UFS do
container. É como um compartilhamento de diretório (ou arquivo).

#### Mapeando o diretorio público do nginx
``` shell
docker container run -d --name wsc -p 8080:80 -v /home/tuca/__docker/__pub:/usr/share/nginx/html nginx

```

