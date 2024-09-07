# Ansible - Inventários e Facts

Neste módulo, vamos explorar como o Ansible gerencia inventários de hosts e coleta informações detalhadas sobre os sistemas através de "facts".

## 1. Inventários no Ansible

O inventário no Ansible é um arquivo onde são definidos os hosts e grupos de hosts que serão gerenciados.

### Tipos de Inventário:
- **Inventário Estático**: Definido em um arquivo, geralmente `hosts` ou `inventory`, no formato INI ou YAML.
- **Inventário Dinâmico**: Gerado dinamicamente a partir de uma fonte externa, como um provedor de nuvem (AWS, GCP, etc).

### Exemplo de Inventário Estático (INI):
```ini
[webservers]
web1 ansible_host=192.168.1.101
web2 ansible_host=192.168.1.102

[dbservers]
db1 ansible_host=192.168.1.201
db2 ansible_host=192.168.1.202
```

Exemplo de Inventário Estático (YAML):
```yaml
all:
  hosts:
    localhost:
      ansible_connection: local
  children:
    webservers:
      hosts:
        web1:
          ansible_host: 192.168.1.101
        web2:
          ansible_host: 192.168.1.102
    dbservers:
      hosts:
        db1:
          ansible_host: 192.168.1.201
        db2:
          ansible_host: 192.168.1.202
```
Comando Ad-Hoc para Verificar Inventário:
``````bash
ansible all --list-hosts
Usando o Inventário no Ansible Playbook:
```
```yaml

- name: Testar Conexão com Hosts
  hosts: webservers
  tasks:
    - name: Testar conexão com web1 e web2
      ping:
```
Comando Ad-Hoc:

```bash

ansible webservers -m ping
2. Ansible Facts
Os "facts" no Ansible são informações coletadas automaticamente sobre os hosts, como o sistema operacional, versão, rede, memória, e muito mais.

Coletando Facts:
O Ansible coleta facts automaticamente ao executar um playbook ou um comando ad-hoc, mas você pode coletá-los manualmente.

Comando Ad-Hoc para Coletar Facts:

```bash
ansible all -m setup
```


Exemplo de Uso de Facts em um Playbook:
```yaml

- name: Exibir informações do sistema
  hosts: all
  tasks:
    - name: Exibir o sistema operacional do host
      debug:
        msg: "O sistema operacional deste host é {{ ansible_facts['os_family'] }}."
```

Desativando Coleta de Facts:
Se você quiser desativar a coleta de facts ao executar um playbook, use a opção gather_facts: no.

Exemplo:

```yaml

- name: Playbook sem coleta de facts
  hosts: all
  gather_facts: no
  tasks:
    - name: Executar comando uptime
      command: uptime
```
Comando Ad-Hoc para Obter Informações Específicas:
Você pode filtrar os facts para obter informações específicas sobre um host. Por exemplo, para obter o endereço IP:
```bash

ansible all -m setup -a 'filter=ansible_default_ipv4'
```

3. Usando Facts para Condicionais
Facts podem ser usados para definir condicionais em seus playbooks. Por exemplo, você pode executar diferentes tarefas com base no sistema operacional do host.

Exemplo:

```yaml

- name: Instalar pacotes dependendo do sistema operacional
  hosts: all
  tasks:
    - name: Instalar Apache no Debian
      apt:
        name: apache2
      when: ansible_facts['os_family'] == "Debian"

    - name: Instalar Apache no RedHat
      yum:
        name: httpd
      when: ansible_facts['os_family'] == "RedHat"
```
Comando Ad-Hoc para Condicionais:
Se você deseja verificar uma condição com facts em um comando ad-hoc:

```bash

ansible all -m yum -a "name=httpd state=present" -e "ansible_facts.os_family=='RedHat'"
```

Neste módulo, aprendemos a gerenciar inventários de hosts e a utilizar os facts para obter informações detalhadas sobre os sistemas, além de aplicá-los em playbooks e comandos ad-hoc. Usar inventários corretamente e entender os facts são partes cruciais para automatizar sua infraestrutura de maneira eficiente com Ansible.

Esse README inclui tanto os exemplos de inventário (estático e YAML) quanto a utilização de facts