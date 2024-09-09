# Ansible - Uso de Variáveis com Role e host_vars

Este guia vai explicar como usar variáveis no Ansible com o uso de roles criadas pelo **Ansible Galaxy** e arquivos `host_vars`, onde as variáveis específicas para cada servidor (host) são definidas.

## 1. O que são Variáveis no Ansible?

As variáveis no Ansible são usadas para armazenar valores dinâmicos que podem ser reutilizados em diferentes partes de um playbook ou role. Elas permitem escrever automações flexíveis e reutilizáveis. No nosso caso, vamos definir variáveis específicas para cada servidor em arquivos dentro do diretório `host_vars`.

### Exemplo de Uso de Variáveis com `host_vars` e Roles

## 2. Estrutura do Projeto com `host_vars` e Roles

O Ansible irá buscar os arquivos específicos de cada host no diretório `host_vars/`. Aqui está a estrutura do projeto:

```bash
.
├── playbook.yml           # O playbook principal
├── inventario.ini         # O arquivo de inventário
├── host_vars/             # Diretório contendo variáveis específicas de cada host
│   ├── web01.yml          # Variáveis específicas para web01
│   └── web02.yml          # Variáveis específicas para web02
└── roles/
    └── role_base/         # Role criada com Ansible Galaxy
        ├── tasks/
        │   └── main.yml   # Tarefas da role
        ├── defaults/
        │   └── main.yml   # Variáveis padrão (como `port`)
        ├── handlers/
        │   └── main.yml   # Handlers para reiniciar serviços
        ├── meta/
        │   └── main.yml   # Metadados da role
        └── README.md      # Descrição da role
```
## 3. Arquivo playbook.yml
Este é o playbook principal, que aplica a role role_base em todos os servidores definidos no inventário.

```yaml
---
- name: Aplicar configuração nos servidores web
  hosts: webservers
  become: yes
  roles:
    - role_base
```
## 4. Arquivo inventario
Este inventário lista os hosts web01 e web02 no grupo webservers. O Ansible utilizará os arquivos correspondentes em host_vars/ para buscar as variáveis específicas de cada host.

```ini

# inventario.ini
[webservers]
web01 ansible_host=192.168.1.101
web02 ansible_host=192.168.1.102
```

## 5. Diretório host_vars/ (Definindo Variáveis por Host)
Os arquivos host_vars/web01.yml e host_vars/web02.yml contêm as variáveis específicas de cada servidor (host). O Ansible vai procurar os arquivos de variáveis baseados no nome do host.

host_vars/web01.yml (para web01 rodando Ubuntu e Nginx)
```yaml

---
# Variáveis específicas para web01 (Ubuntu e Nginx)
pacote: nginx
ansible_host: 192.168.1.101
ansible_user: adminlx
ansible_become: yes
```
host_vars/web02.yml (para web02 rodando CentOS e Apache)
```yaml

---
# Variáveis específicas para web02 (CentOS e Apache)
pacote: httpd
ansible_host: 192.168.1.102
ansible_user: adminlx
ansible_become: yes
```

## 6. Role role_base (Estrutura da Role)
A role role_base foi criada com o comando ansible-galaxy init role_base. Ela contém as tarefas principais que serão aplicadas a ambos os servidores (com base nas variáveis definidas nos arquivos host_vars/).

Arquivo roles/role_base/tasks/main.yml
Este arquivo contém as tarefas que instalam o pacote adequado e configuram o serviço com base nas variáveis definidas para cada servidor.

```yaml
---
# Instalar o pacote correto com base no sistema operacional
- name: Instalar {{ pacote }} no Ubuntu
  apt:
    name: "{{ pacote }}"
    state: present
  when: ansible_distribution == "Ubuntu"
  notify: "Reiniciar Nginx"

- name: Instalar {{ pacote }} no CentOS
  yum:
    name: "{{ pacote }}"
    state: present
  when: ansible_distribution == "CentOS"
  notify: "Reiniciar Apache"

# Configurar o serviço no Ubuntu
- name: Configurar {{ pacote }} no Ubuntu
  lineinfile:
    path: /etc/nginx/nginx.conf
    regexp: '^listen'
    line: "listen {{ port }};"
  when: ansible_distribution == "Ubuntu"
  notify: "Reiniciar Nginx"

# Configurar o serviço no CentOS
- name: Configurar {{ pacote }} no CentOS
  lineinfile:
    path: /etc/httpd/conf/httpd.conf
    regexp: '^Listen'
    line: "Listen {{ port }}"
  when: ansible_distribution == "CentOS"
  notify: "Reiniciar Apache"
```

Arquivo roles/role_base/defaults/main.yml
Aqui definimos a variável port, que será a mesma para ambos os servidores.

```yaml
---
# Variável padrão compartilhada entre os servidores
port: 8080
```
Arquivo roles/role_base/handlers/main.yml
Este arquivo contém os handlers para reiniciar os serviços após as configurações serem aplicadas.

```yaml

---
# Handlers para reiniciar serviços
- name: Reiniciar Nginx
  service:
    name: nginx
    state: restarted

- name: Reiniciar Apache
  service:
    name: httpd
    state: restarted
```

## 7. Executando o Playbook
Para aplicar as configurações nos servidores web01 e web02, execute o seguinte comando:

```bash

ansible-playbook -i inventario.ini playbook.yml
```
## 8. Explicação:
Diretório host_vars/: Cada servidor tem seu próprio arquivo dentro de host_vars/, onde as variáveis específicas (como pacote e ansible_user) são definidas.
Diretório defaults/: Define variáveis padrão, como port, que é compartilhada entre os dois servidores.
Tarefas na Role: As tarefas da role role_base instalam os pacotes corretos (Nginx para web01 e Apache para web02) e configuram os serviços usando as variáveis do arquivo host_vars correspondente.
Com essa estrutura, o Ansible sabe qual arquivo de variáveis usar com base no nome do host definido no inventário, e as tarefas são executadas conforme necessário em cada servidor. Isso permite que a automação seja eficiente e flexível para diferentes ambientes.

