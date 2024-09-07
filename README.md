# Módulo Ansible - Gerenciador de Configuração

Neste módulo, iremos configurar três máquinas: uma para ser o servidor Ansible e duas para serem os clientes. Usaremos o Ansible como um gerenciador de configuração para automatizar e orquestrar tarefas em diversos sistemas operacionais.

## 1. Requisitos de Infraestrutura

Para este módulo, será necessário configurar três máquinas virtuais ou físicas com os seguintes sistemas operacionais:

- **Ansible Server**: Ubuntu Server
- **Client 1**: CentOS
- **Client 2**: Ubuntu Server

## 2. O que é o Ansible?

O **Ansible** é uma ferramenta de automação de TI de código aberto usada para configurar sistemas, implantar software e orquestrar tarefas mais complexas, como atualizações contínuas, balanceamento de carga e coordenação entre sistemas distribuídos. Diferentemente de outras ferramentas de automação, o Ansible é agentless, ou seja, ele não precisa de um agente rodando nos hosts que ele gerencia, o que simplifica a configuração e manutenção.

### Características Principais:
- **Simples**: Utiliza YAML para definir playbooks, o que facilita o uso e a leitura.
- **Agentless**: Não requer instalação de agentes nos nós gerenciados.
- **Idempotente**: Garante que as tarefas sejam executadas apenas quando necessário, sem aplicar mudanças repetidas vezes.

## 3. O Papel do Ansible em DevOps

O Ansible desempenha um papel crucial em **DevOps**, pois permite que as equipes de desenvolvimento e operações automatizem a configuração de infraestrutura e o gerenciamento de servidores de maneira simples e eficaz. Ele facilita a criação de ambientes consistentes e evita erros manuais na configuração.

### Benefícios para DevOps:
- **Automação de Infraestrutura**: Com o Ansible, a criação e configuração de servidores e ambientes podem ser totalmente automatizadas, desde a instalação de pacotes até a configuração de serviços.
- **Integração Contínua/Entrega Contínua (CI/CD)**: O Ansible pode ser integrado a pipelines de CI/CD, automatizando o processo de deploy de aplicações e configurações.
- **Gerenciamento de Configuração**: Ele permite que todas as configurações sejam definidas como código, tornando-as auditáveis e versionáveis.
- **Escalabilidade**: É possível gerenciar centenas ou até milhares de máquinas com o Ansible, simplificando o trabalho de administração de sistemas.

## 4. Ganho de Produtividade com Ansible

Ao usar o Ansible, equipes de DevOps podem economizar uma quantidade significativa de tempo e esforço na execução de tarefas repetitivas, como a instalação de pacotes, configuração de serviços e atualização de sistemas. Algumas das principais vantagens em termos de produtividade incluem:

- **Redução de Erros Manuais**: Como as tarefas são definidas em playbooks, o Ansible elimina a necessidade de configurar manualmente cada servidor.
- **Reutilização de Configurações**: Um único playbook pode ser reutilizado para diferentes servidores e ambientes, economizando tempo de configuração.
- **Rapidez no Deploy**: Playbooks podem ser executados em múltiplos servidores simultaneamente, acelerando o processo de deploy.

## 5. Estrutura de Configuração

### Ansible Server:
1. **Sistema Operacional**: Ubuntu Server
2. **Função**: Controlará e executará as tarefas nos clientes.

### Client 1:
1. **Sistema Operacional**: CentOS
2. **Função**: Receberá as configurações e automações definidas pelo Ansible.

### Client 2:
1. **Sistema Operacional**: Ubuntu Server
2. **Função**: Também receberá as configurações e automações definidas pelo Ansible.

---

Com o Ansible, você pode gerenciar seus servidores de maneira eficiente, automatizar tarefas repetitivas e garantir consistência em seu ambiente DevOps. Ao longo deste módulo, exploraremos como configurar e utilizar o Ansible para facilitar a gestão de infraestrutura, permitindo que você se concentre em outras atividades de maior valor.
