# Ansible - Noções básicas do Ansible

Este módulo aborda funcionalidades básicas adicionais do Ansible, dando continuidade ao primeiro módulo introdutório.

## 1. Instalando Pacotes com Ansible

Vamos aprender como instalar pacotes em servidores usando o Ansible.  
Usaremos o módulo `package` para gerenciar pacotes em diferentes sistemas operacionais.

Exemplo Playbook:
```
- name: Instalar pacote nginx
  package:
    name: nginx
    state: present
```
Comando Ad-Hoc:

```
ansible all -m package -a "name=nginx state=present"
```

2. Copiando Arquivos com Ansible
Neste tópico, veremos como copiar arquivos locais para servidores remotos usando o módulo copy.

Exemplo Playbook:

```
- name: Copiar arquivo de configuração
  copy:
    src: ./meu_arquivo.conf
    dest: /etc/nginx/nginx.conf
```
Comando Ad-Hoc:

```
ansible all -m copy -a "src=./meu_arquivo.conf dest=/etc/nginx/nginx.conf"
```

3. Gerenciando Serviços com Ansible
Vamos explorar como iniciar, parar e reiniciar serviços em sistemas remotos utilizando o Ansible.

Exemplo Playbook:

```
- name: Reiniciar o serviço nginx
  service:
    name: nginx
    state: restarted
```
Comando Ad-Hoc:

```
ansible all -m service -a "name=nginx state=restarted"
```
4. Verificando o Status de Hosts com Ping
Neste tópico, usaremos o módulo ping para verificar se os hosts gerenciados estão acessíveis.

Exemplo Playbook:

```
- name: Verificar se os hosts estão acessíveis
  ping:
```
Comando Ad-Hoc:

```
ansible all -m ping
```

5. Atualizando o Sistema Operacional com Ansible
Automatizando a atualização do sistema operacional dos hosts com o módulo package.

Exemplo Playbook:

```
- name: Atualizar pacotes no Ubuntu
  apt:
    upgrade: dist
```
Comando Ad-Hoc:

```
ansible all -m apt -a "upgrade=dist"
```

6. Criando Diretórios com Ansible
Como criar diretórios em servidores remotos utilizando o módulo file.

Exemplo Playbook:

```
- name: Criar diretório /app
  file:
    path: /app
    state: directory
    mode: '0755'
```
Comando Ad-Hoc:

```
ansible all -m file -a "path=/app state=directory mode=0755"
```
7. Gerenciando Usuários e Grupos
Neste tópico, veremos como criar e gerenciar usuários e grupos em servidores.

Exemplo Playbook:

```
- name: Criar usuário 'deploy'
  user:
    name: deploy
    state: present
    groups: sudo
```
Comando Ad-Hoc:

```
ansible all -m user -a "name=deploy state=present groups=sudo"
```

8. Executando Comandos com Ansible
Aprenderemos a executar comandos diretamente em servidores remotos usando o módulo command.

Exemplo Playbook:

```
- name: Executar comando uptime
  command: uptime
```
Comando Ad-Hoc:

```
ansible all -a "uptime"
```
9. Configurando Permissões de Arquivos e Diretórios
Como configurar permissões de arquivos e diretórios usando o Ansible.

Exemplo Playbook:

```
- name: Ajustar permissões de diretório
  file:
    path: /app
    state: directory
    mode: '0755'
```
Comando Ad-Hoc:

```
ansible all -m file -a "path=/app state=directory mode=0755"
```

10. Automatizando Tarefas de Reinicialização
Neste tópico, aprenderemos como automatizar a reinicialização de servidores.

Exemplo Playbook:

```
- name: Reiniciar servidor
  reboot:
    reboot_timeout: 300
```
Comando Ad-Hoc:

```
ansible all -m reboot -a "reboot_timeout=300"
```

Este módulo é voltado para profissionais de infraestrutura que desejam expandir suas habilidades de automação com Ansible. Após concluir este módulo, você será capaz de realizar tarefas básicas de administração com mais eficiência, incluindo a execução de comandos ad-hoc para simplificar o gerenciamento de servidores.
Esse README combina os exemplos de playbooks com os comandos ad-hoc, oferecendo uma visão prática de como cada tópico pode ser aplicado no Ansible.






