# Treinamento: Introdução ao Ansible

## Objetivo

Este treinamento tem como objetivo fornecer uma introdução ao Ansible, uma ferramenta de automação de TI, com foco em sistemas Ubuntu. Ao final desta sessão, você terá uma compreensão básica do Ansible, seus componentes principais e como escrever e executar um playbook simples, incluindo o uso de SSH com chaves, configuração do sudo sem senha e utilização de Ansible Facts.

## Agenda

1. O que é Ansible?
2. Por que usar Ansible?
3. Componentes principais do Ansible
4. Instalando o Ansible no Ubuntu
5. Configurando SSH para Ansible
6. Configurando sudo sem senha
7. Escrevendo seu primeiro Playbook
8. Ansible Facts
9. Executando Playbooks
10. Recursos adicionais

---

### 1. O que é Ansible?

Ansible é uma ferramenta de automação de TI de código aberto, utilizada para configuração de sistemas, gerenciamento de aplicativos, deploy e orquestração de tarefas complexas. Ele permite que você gerencie infraestrutura de maneira eficiente, segura e repetível.

### 2. Por que usar Ansible?

- **Simplicidade**: Ansible usa uma linguagem simples, YAML, para descrever tarefas de automação.
- **Agentless**: Não requer a instalação de agentes nos nós gerenciados.
- **Extensibilidade**: Suporta uma ampla gama de módulos para integração com diferentes serviços e sistemas.
- **Comunidade**: Possui uma grande comunidade e documentação abrangente.

### 3. Componentes principais do Ansible

- **Control Node**: O sistema onde o Ansible está instalado e de onde você executa seus comandos.
- **Managed Nodes**: Os sistemas que são gerenciados pelo Ansible.
- **Inventory**: Um arquivo que especifica os nós gerenciados.
- **Playbook**: Arquivo YAML que contém uma série de tarefas a serem executadas nos nós gerenciados.
- **Modules**: Unidades de trabalho executáveis que o Ansible pode executar nos nós gerenciados.
- **Roles**: Estrutura que permite organizar Playbooks e outros arquivos em um conjunto padrão de diretórios.

### 4. Instalando o Ansible no Ubuntu

1. Atualize a lista de pacotes:

    ```bash
    sudo apt update
    ```

2. Instale o Ansible:

    ```bash
    sudo apt install ansible -y
    ```

3. Verifique a instalação:

    ```bash
    ansible --version
    ```

### 5. Configurando SSH para Ansible

Para que o Ansible possa se comunicar com os nós gerenciados, é necessário configurar o acesso SSH usando chaves SSH.

1. **Gerar uma chave SSH**:

    ```bash
    ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa
    ```

2. **Copiar a chave pública para os nós gerenciados**:

    ```bash
    ssh-copy-id user@managed_node_ip
    ```

    Substitua `user` pelo nome de usuário do nó gerenciado e `managed_node_ip` pelo endereço IP do nó gerenciado.

3. **Verifique a conectividade SSH**:

    ```bash
    ssh user@managed_node_ip
    ```

### 6. Configurando sudo sem senha

Para permitir que o Ansible execute comandos com sudo sem solicitar uma senha, você pode configurar o sudoers.

1. No nó gerenciado, edite o arquivo sudoers:

    ```bash
    sudo visudo
    ```

2. Adicione a seguinte linha, substituindo `user` pelo nome de usuário:

    ```bash
    user ALL=(ALL) NOPASSWD: ALL
    ```

### 7. Escrevendo seu primeiro Playbook

Um Playbook básico (`site.yml`):

```yaml
---
- name: Exemplo de Playbook Ansible
  hosts: servers
  become: yes
  tasks:
    - name: Instalar pacote Nginx
      apt:
        name: nginx
        state: present

    - name: Iniciar serviço Nginx
      service:
        name: nginx
        state: started
---
- name: Exemplo de Playbook com Ansible Facts
  hosts: servers
  tasks:
    - name: Mostrar informações sobre o sistema
      debug:
        msg: "O sistema operacional é {{ ansible_distribution }} {{ ansible_distribution_version }} rodando em {{ ansible_architecture }}"
```

### 8. Ansible Facts
Ansible Facts são variáveis coletadas automaticamente por Ansible dos nós gerenciados, que fornecem informações sobre o sistema, como a versão do SO, arquitetura, interfaces de rede, entre outras.

Coletando Facts
Por padrão, Ansible coleta facts automaticamente antes de executar qualquer tarefa. Você pode acessar esses facts em seus playbooks.

Exemplo de Uso de Facts
Um Playbook que utiliza facts (facts.yml):


```yaml
---
- name: Exemplo de Playbook com Ansible Facts
  hosts: servers
  tasks:
    - name: Mostrar informações sobre o sistema
      debug:
        msg: "O sistema operacional é {{ ansible_distribution }} {{ ansible_distribution_version }} rodando em {{ ansible_architecture }}"
```
#### 9. Executando Playbooks

Depois de escrever seu primeiro playbook, o próximo passo é executá-lo. Para isso, use o comando ansible-playbook seguido do nome do arquivo do playbook.

Exemplo de execução de playbook:

```bash
ansible-playbook site.yml
```
Este comando executa o playbook site.yml nos hosts definidos no inventário. O Ansible vai se conectar aos hosts via SSH e executar as tarefas especificadas no playbook.

Se você quiser executar o playbook em um inventário específico, você pode usar a opção -i para especificar o arquivo de inventário:

```bash
ansible-playbook -i inventario.yml site.yml
```
Verificando o resultado: Após a execução, o Ansible exibirá um relatório das tarefas executadas, indicando se elas foram bem-sucedidas ou se houve alguma alteração no sistema (ex.: instalação de um pacote ou reinício de um serviço).

10. Recursos Adicionais

Para se aprofundar no Ansible e continuar sua jornada de automação, aqui estão alguns recursos adicionais recomendados:

Documentação oficial do Ansible: A documentação oficial do Ansible é um excelente ponto de partida para aprender mais sobre módulos, best practices, roles e outros tópicos avançados. Documentação do Ansible

Ansible Galaxy: Ansible Galaxy é uma comunidade onde você pode baixar e compartilhar roles criadas por outros usuários. Ansible Galaxy

