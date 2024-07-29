## Multipass

Multipass é uma ferramenta de virtualização desenvolvida pela Canonical, a empresa por trás do Ubuntu. Ele é projetado para facilitar a criação e o gerenciamento de máquinas virtuais (VMs) Ubuntu em várias plataformas, incluindo Windows, macOS e Linux. Multipass é particularmente útil para desenvolvedores e administradores de sistemas que precisam testar, desenvolver ou executar aplicativos em ambientes Ubuntu isolados.

### Configurando VM com multipass

Após a instalação do multipass, deverá entrar no diretório onde foi feito o clone e execultar as tarefas abaixo a seguir.

### Ambiente de desenvolvimento local com VM Docker

O comando abaixo vai criar uma instancia Ubuntu LTS para desenvolvimento

```shell
multipass launch docker --name vm-dev --network Wi-Fi --cloud-init .\dev.cloud-init.yaml
```

### Ambiente de homologação

O comando abaixo vai criar uma instancia Ubuntu LTS para homologação

```shell
multipass launch lts --name vm-dev --network Wi-Fi --cloud-init .\hml.cloud-init.yaml
```

### Ambiende para banco de dados

O comando abaixo vai criar uma instancia Ubuntu LTS para hospedar o banco de dados.

```shell
multipass launch lts --name vm-dev --network Wi-Fi --cloud-init .\vm-database\cloud-init.yaml
```

Configurando banco de dados para conceder permissão de acesso mediante de rede.

ir em `/etc/postgresql/<versão>/main/postgresql.conf` e alterar a propiedade `listen_addresses = '*'`
ir em `/etc/postgresql/<versão>/main/pg_hba.conf` e adicionar a linha de para IPs da rede poder comunincar com o banco de dados `host    all             all             192.168.1.0/24          md5`

acessando o banco de dados.

```shell
psql -h 192.168.1.250 -U postgres -d cs_base
```

Para muddar o IP do servidor, deverá acessar o arquivo de configuração.

```shell
sudo nano /etc/netplan/01-netcfg.yaml
```

Colar as configurações abaixo:

```yaml
network:
  version: 2
  ethernets:
    enp0s8:
      dhcp4: no
      addresses:
        - 192.168.1.250/24
```

concedendo permissões e aplicando as configuraçãos de rede.

```shell
sudo chmod 644 /etc/netplan/01-netcfg.yaml && sudo netplan apply
```