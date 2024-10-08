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

# inventory
[webservers]
web01 ansible_host=1192.168.2.74
```

## 5. Diretório host_vars/ (Definindo Variáveis por Host)
Os arquivos host_vars/web01.yml e host_vars/web02.yml contêm as variáveis específicas de cada servidor (host). O Ansible vai procurar os arquivos de variáveis baseados no nome do host.

host_vars/web01.yml (para web01 rodando Ubuntu e Nginx)
```yaml

---
# Variáveis específicas para web01 (Ubuntu e Nginx)
pacote: nginx
ansible_host: 192.168.2.74
server_name: web01

```

## 6. Role role_base (Estrutura da Role)
A role role_base foi criada com o comando ansible-galaxy init role_base. Ela contém as tarefas principais que serão aplicadas a ambos os servidores (com base nas variáveis definidas nos arquivos host_vars/).

Arquivo roles/role_base/tasks/main.yml
Este arquivo contém as tarefas que instalam o pacote adequado e configuram o serviço com base nas variáveis definidas para cada servidor.

```yaml
---
---

- name: Criar o diretório de logs do Nginx
  file:
    path: /var/log/nginx/{{ server_name }}
    state: directory
    owner: www-data
    group: www-data
    mode: '0755'

- name: Remover o link simbólico padrão do Nginx
  file:
    path: /etc/nginx/sites-enabled/default
    state: absent
  when: ansible_distribution == "Ubuntu"
  notify: "Reiniciar Nginx"

- name: Aplicar template de Virtual Host e substituir o padrão
  template:
    src: templates/virtual_host.j2
    dest: /etc/nginx/sites-enabled/default
  when: ansible_distribution == "Ubuntu"
  notify: "Reiniciar Nginx"

- name: Verificar a configuração do Nginx
  command: nginx -t
  register: nginx_config_test
  when: ansible_distribution == "Ubuntu"
  changed_when: false

- name: Exibir saída da verificação do Nginx
  when: ansible_distribution == "Ubuntu"
  debug:
    var: nginx_config_test.stdout

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

ansible-playbook -i inventory playbook.yml
```
## 8. Explicação:
Diretório host_vars/: Cada servidor tem seu próprio arquivo dentro de host_vars/, onde as variáveis específicas (como pacote e ansible_user) são definidas.
Diretório defaults/: Define variáveis padrão, como port, que é compartilhada entre os dois servidores.
Tarefas na Role: As tarefas da role role_base instalam os pacotes corretos (Nginx para web01 e Apache para web02) e configuram os serviços usando as variáveis do arquivo host_vars correspondente.
Com essa estrutura, o Ansible sabe qual arquivo de variáveis usar com base no nome do host definido no inventário, e as tarefas são executadas conforme necessário em cada servidor. Isso permite que a automação seja eficiente e flexível para diferentes ambientes.

