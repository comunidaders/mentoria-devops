# Ansible - Uso de Variáveis

Neste módulo, vamos explorar como trabalhar com variáveis no Ansible, entender os diferentes tipos de variáveis, onde elas podem ser definidas, e como utilizá-las nos playbooks.

## 1. O que são Variáveis no Ansible?

As variáveis no Ansible são usadas para armazenar valores dinâmicos que podem ser reutilizados em diferentes partes de um playbook. Elas permitem que você escreva playbooks mais flexíveis, fáceis de manter, e com menos repetições de código.

### Exemplos de Variáveis:
- Definir o nome de um pacote
- Armazenar endereços IP de servidores
- Definir caminhos de diretórios ou arquivos

### Sintaxe:
As variáveis no Ansible são referenciadas usando a sintaxe `{{ nome_da_variavel }}`.

Exemplo:
```yaml
vars:
  pacote: nginx

tasks:
  - name: Instalar {{ pacote }}
    apt:
      name: "{{ pacote }}"
      state: present
```
2. Onde Definir Variáveis?
As variáveis no Ansible podem ser definidas em diferentes locais, dependendo do escopo e da necessidade. Aqui estão os locais mais comuns para definir variáveis:

#### 2.1. No Próprio Playbook:
Você pode definir variáveis diretamente dentro de um playbook na seção vars.

Exemplo:

```yaml

---
- name: Instalar pacotes
  hosts: all
  vars:
    pacote: nginx
  tasks:
    - name: Instalar {{ pacote }}
      apt:
        name: "{{ pacote }}"
        state: present
```
#### 2.2. Arquivos de Variáveis:
Você também pode definir variáveis em arquivos separados e carregá-las em um playbook. Isso ajuda a organizar melhor as variáveis.

Exemplo de arquivo de variáveis (vars/main.yml):

```yaml

pacote: nginx
port: 8080
```
Playbook referenciando o arquivo de variáveis:

```yaml

---
- name: Instalar e configurar serviço
  hosts: all
  vars_files:
    - vars/main.yml
  tasks:
    - name: Instalar {{ pacote }}
      apt:
        name: "{{ pacote }}"
        state: present
    - name: Configurar porta
      lineinfile:
        path: /etc/nginx/sites-available/default
        regexp: '^listen'
        line: "listen {{ port }};"
```
#### 2.3. Group Vars e Host Vars:
Você pode definir variáveis específicas para grupos de hosts ou para hosts individuais.

Group Vars: Definem variáveis para um grupo inteiro de hosts.
Host Vars: Definem variáveis específicas para um host individual.
Estrutura de diretórios:

```bash

group_vars/
  webservers.yml
host_vars/
  web1.yml
  web2.yml
```
Conteúdo do arquivo group_vars/webservers.yml:

```yaml

pacote: nginx
port: 80
```
#### 2.4. Definir Variáveis no Inventário:
As variáveis também podem ser definidas diretamente no arquivo de inventário.

Exemplo de inventário com variáveis:

```ini

[webservers]
web1 ansible_host=192.168.1.101 pacote=nginx
web2 ansible_host=192.168.1.102 pacote=apache
```

#### 2.5. Passagem de Variáveis pela Linha de Comando:
Você pode passar variáveis diretamente ao executar um playbook usando a opção -e.

Exemplo:

```bash
ansible-playbook playbook.yml -e "pacote=nginx"
```
3. Tipos de Variáveis
#### 3.1. Variáveis Escalares:
São variáveis simples que armazenam valores únicos.

Exemplo:

```yaml

vars:
  pacote: nginx
  port: 8080
```
#### 3.2. Variáveis de Lista:
As listas armazenam múltiplos valores.

Exemplo:

```yaml

vars:
  pacotes:
    - nginx
    - apache
    - mysql
```
Uso em uma tarefa:
```yaml

tasks:
  - name: Instalar pacotes
    apt:
      name: "{{ item }}"
      state: present
    loop: "{{ pacotes }}"
``` 
#### 3.3. Variáveis de Dicionário:
Dicionários são usados para armazenar pares chave-valor.

Exemplo:

```yaml

vars:
  servidor:
    nome: web1
    ip: 192.168.1.101
    porta: 8080
```

Uso em uma tarefa:
```yaml

tasks:
  - name: Exibir informações do servidor
    debug:
      msg: "Servidor {{ servidor.nome }} está no IP {{ servidor.ip }} e porta {{ servidor.porta }}"
```
### 4. Sobrescrevendo Variáveis
Quando várias fontes de variáveis estão em uso (como inventários, arquivos de variáveis, e variáveis passadas pela linha de comando), o Ansible tem uma ordem de precedência que determina quais variáveis têm prioridade.

Ordem de Precedência (da menor para a maior):
Defaults de Roles (defaults/main.yml)
Group Vars
Host Vars
Variáveis definidas em arquivos incluídos com vars_files
Variáveis definidas diretamente no playbook (vars)
Variáveis passadas pela linha de comando (-e)
Isso significa que as variáveis passadas pela linha de comando sempre sobrescrevem quaisquer outras.

### 5. Exemplo Completo
Aqui está um exemplo completo de playbook que usa diferentes tipos de variáveis e fontes de variáveis:

```yaml

---
- name: Configurar Webservers
  hosts: webservers
  vars:
    pacote: nginx
    port: 8080
    usuarios:
      - nome: deploy
      - nome: admin
  tasks:
    - name: Instalar {{ pacote }}
      apt:
        name: "{{ pacote }}"
        state: present

    - name: Configurar a porta do serviço
      lineinfile:
        path: /etc/nginx/sites-available/default
        regexp: '^listen'
        line: "listen {{ port }};"

    - name: Criar usuários
      user:
        name: "{{ item.nome }}"
        state: present
      loop: "{{ usuarios }}"

Neste exemplo, estamos:
```
Instalando o pacote nginx (definido como variável)
Configurando a porta do serviço Nginx
Criando usuários a partir de uma lista de variáveis
Com este módulo, você aprendeu como trabalhar com variáveis no Ansible, como defini-las em diferentes lugares, os tipos de variáveis disponíveis, e a ordem de precedência entre elas. Variáveis tornam os playbooks mais flexíveis e reutilizáveis.
