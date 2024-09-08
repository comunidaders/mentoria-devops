# Ansible Playbook - Explicação de `hosts`, `remote_user`, `become`, e `become_user`

Neste documento, vamos explicar em detalhes o significado de algumas diretivas importantes em um playbook Ansible: `hosts`, `remote_user`, `become`, e `become_user`.

## 1. Diretiva `hosts`

### O que é?
A diretiva `hosts` define em quais nós ou grupos de nós o Ansible executará as tarefas descritas no playbook.

### Exemplo:
```yaml
hosts: all
```
#### Explicação:
all: O Ansible executará as tarefas em todos os hosts definidos no inventário. O valor all refere-se a todos os nós que estão especificados no seu arquivo de inventário.
Se você quiser executar as tarefas em um grupo específico de hosts, pode especificá-lo em vez de all:

```yaml
hosts: webservers
```
Nesse caso, as tarefas só serão executadas nos hosts que pertencem ao grupo webservers no inventário.

### 2. Diretiva remote_user
O que é?
A diretiva remote_user define qual usuário será usado para se conectar aos hosts remotos via SSH.

Exemplo:
```yaml
remote_user: adminlx
```
#### Explicação:
adminlx: Este é o nome de usuário utilizado para conectar-se via SSH aos nós gerenciados. Esse usuário precisa ter permissões adequadas para executar as tarefas no host remoto. Se o usuário não for especificado, o Ansible tentará usar o usuário padrão da máquina de controle ou o usuário especificado no arquivo de inventário.
Você também pode definir o usuário no comando ou no inventário, mas usar o remote_user no playbook é útil quando você deseja definir o usuário diretamente no próprio playbook.

3. Diretiva become
O que é?
A diretiva become é usada para ativar a escalada de privilégios, permitindo que as tarefas sejam executadas com um usuário diferente, normalmente root.

Exemplo:
```yaml

become: yes
```
#### Explicação:
yes: Quando become é definido como yes, o Ansible usará sudo ou outra ferramenta de escalada de privilégio (como su ou pbrun) para executar as tarefas. Isso é necessário quando o usuário SSH padrão (definido com remote_user) não tem permissões suficientes para realizar certas operações (como instalar pacotes ou alterar arquivos de configuração).
Em muitos casos, become é usado para garantir que as tarefas sejam executadas com permissões de superusuário.

### 4. Diretiva become_user
O que é?
A diretiva become_user define qual usuário será usado após a escalada de privilégios, ou seja, com qual usuário as tarefas serão executadas depois que o Ansible ativar o become.

Exemplo:
```yaml
become_user: root
```
#### Explicação:
root: O Ansible escalará os privilégios para o usuário root e executará as tarefas como esse usuário. Isso é útil quando você precisa de permissões administrativas (por exemplo, para gerenciar pacotes ou realizar tarefas de sistema).
Se você não especificar become_user, o Ansible assumirá que o usuário de escalada será root por padrão.

Você pode definir outros usuários, se necessário:

```yaml
become_user: admin
```
Neste caso, o Ansible escalaria os privilégios para o usuário admin em vez de root.

Exemplo Completo
Aqui está um exemplo completo de um playbook que utiliza todas essas diretivas:

```yaml
- name: Instalar e configurar Nginx em servidores
  hosts: all
  remote_user: adminlx  # Usuário usado para se conectar via SSH
  become: yes           # Ativa a escalada de privilégio
  become_user: root      # Usuário com o qual o comando será executado
  tasks:
    - name: Instalar pacote Nginx
      apt:
        name: nginx
        state: present
```
#### Explicação:
O Ansible se conectará a todos os hosts definidos no inventário (hosts: all).
Usará o usuário adminlx para se conectar aos hosts via SSH (remote_user: adminlx).
Ativará a escalada de privilégios (become: yes) para realizar operações que exigem permissões elevadas.
Executará as tarefas como o usuário root (become_user: root), garantindo acesso completo para instalar o pacote Nginx.
Com essas explicações, você agora entende como o Ansible define os hosts, usuários SSH e como ele realiza a escalada de privilégios para executar tarefas com permissões elevadas em seus playbooks.

