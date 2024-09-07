# Ansible - Noções Básicas do Playbook

Neste módulo, vamos abordar os conceitos básicos de playbooks no Ansible, entender sua estrutura, e como usar o Ansible Galaxy para iniciar novos projetos com o comando `galaxy init`.

## 1. Estrutura de um Playbook

Um **playbook** no Ansible é um arquivo YAML que descreve uma série de tarefas que serão executadas em hosts remotos. A estrutura básica de um playbook inclui:

### Principais Componentes:
- **Hosts**: Define em quais hosts ou grupos de hosts as tarefas serão executadas.
- **Tasks**: São as ações individuais a serem executadas nos hosts.
- **Handlers**: Ações que são acionadas apenas quando determinadas mudanças ocorrem.
- **Variables**: Definição de variáveis que podem ser reutilizadas ao longo do playbook.
- **Roles**: Conjunto de tarefas organizadas em uma estrutura reutilizável (geralmente gerada via Ansible Galaxy).

### Exemplo Básico de Estrutura:
```yaml
---
- name: Nome do playbook (ex: Configurar Webserver)
  hosts: webservers
  become: yes  # Escalonar privilégios para o usuário root
  vars:
    http_port: 80
  tasks:
    - name: Instalar pacote nginx
      apt:
        name: nginx
        state: present
    - name: Iniciar serviço nginx
      service:
        name: nginx
        state: started

  handlers:
    - name: Reiniciar o serviço nginx
      service:
        name: nginx
        state: restarted
```
### Explicação:
- **name**: Nome do playbook ou das tarefas, útil para manter o código organizado.
- **hosts**: Define o grupo de máquinas onde as tarefas serão executadas, que pode ser um nome de grupo ou all.
- **become**: Usado para executar comandos como superusuário (root).
- **vars**: Seção onde as variáveis são definidas, como o valor da porta HTTP no exemplo.
- **tasks**: Cada tarefa representa uma ação a ser realizada nos hosts definidos. Aqui, estamos instalando e iniciando o serviço nginx.
- **handlers**: São ações acionadas por mudanças em outras tarefas, como reiniciar o serviço nginx se o arquivo de configuração for alterado.

### 2. Ansible Galaxy e o Comando galaxy init
O Ansible Galaxy não é apenas um repositório para baixar roles, mas também oferece uma estrutura padrão para criar suas próprias roles. Para isso, usamos o comando ansible-galaxy init.

Como Criar uma Nova Role com galaxy init:
O comando ansible-galaxy init gera a estrutura básica de uma role, ajudando a organizar as tarefas, handlers, variáveis, e outras configurações em um projeto bem estruturado.

Comando:

```bash

ansible-galaxy init nome_da_role
```
Isso cria uma estrutura de diretórios com todos os arquivos necessários para começar a desenvolver uma role.

Estrutura Gerada pelo ansible-galaxy init:
```bash

nome_da_role/
├── README.md
├── defaults/
│   └── main.yml
├── files/
├── handlers/
│   └── main.yml
├── meta/
│   └── main.yml
├── tasks/
│   └── main.yml
├── templates/
├── tests/
│   ├── inventory
│   └── test.yml
└── vars/
    └── main.yml
```
### Descrição dos Diretórios:
- **tasks**/: Onde você define as tarefas principais da role.
- **handlers**/: Handlers que serão chamados quando houver mudanças nas tarefas.
- **vars**/: Definição de variáveis específicas da role.
- **defaults**/: Variáveis padrão que podem ser sobrescritas.
- **files**/: Arquivos que podem ser copiados ou referenciados nas tarefas.
- **templates**/: Arquivos de template, geralmente em formato Jinja2.
- **meta**/: Informações sobre a role, como dependências.
- **tests**/: Arquivos para testar a role.
Exemplo de Uso:
Após criar uma nova role com ansible-galaxy init, você pode começar a adicionar tarefas na pasta tasks/main.yml.

```yaml

---
# tasks file for nome_da_role
- name: Instalar Apache
  apt:
    name: apache2
    state: present
Agora, você pode chamar essa role em seu playbook:

```yaml

---
- name: Aplicar role personalizada
  hosts: webservers
  roles:
    - nome_da_role
```

3. Exemplo Prático de Playbook
Vamos ver um exemplo prático de um playbook que utiliza uma role criada com ansible-galaxy init.

Exemplo:
```yaml

---
- name: Configurar Webservers com Role Personalizada
  hosts: webservers
  become: yes

  roles:
    - nome_da_role

  vars:
    apache_port: 8080
```

Neste exemplo, estamos utilizando a role que foi criada com o comando ansible-galaxy init. A role instala e configura o servidor Apache.

Com este módulo, você aprendeu a estrutura básica de um playbook no Ansible, como criar uma role usando o comando galaxy init para organizar suas tarefas, e viu um exemplo prático da aplicação dessas roles em playbooks. O uso de roles facilita a reutilização de código e a organização de tarefas complexas.
