## Instalação do Ansible AWX em um cluster Kubernetes usando o AWX Operator.

### Guia de Instalação do Ansible AWX

Este repositório fornece um guia passo a passo para instalar o **Ansible AWX** em um cluster Kubernetes utilizando o **AWX Operator**.

## Pré-requisitos

Antes de começar, certifique-se de que você possui os seguintes itens:

1. **Cluster Kubernetes** (Testado com K3D, Minikube, e outras distribuições Kubernetes)
2. **kubectl** (v1.14 ou posterior)
3. **Helm** (Opcional, para gerenciar pacotes Kubernetes)
4. **Git** para clonar este repositório

## Passo 1: Clonar o Repositório do AWX Operator

Comece clonando o repositório oficial do **AWX Operator** para sua máquina local.

```bash
git clone https://github.com/ansible/awx-operator.git
cd awx-operator
```
Passo 2: Escolher a Versão Desejada
Após clonar o repositório, você deve escolher a tag/versão do AWX Operator que deseja instalar.
```
git tag  # Lista todas as tags disponíveis
git checkout tags/<tag-desejada>  # Exemplo: git checkout tags/2.7.2
```
Exporte a versão escolhida como uma variável de ambiente:

```
export VERSION=<tag-desejada>  # Exemplo: export VERSION=2.7.2
```
Passo 3: Implantar o AWX Operator
Você pode implantar o AWX Operator no seu cluster Kubernetes utilizando kustomize.

Criar o arquivo kustomization.yaml
Crie um arquivo chamado kustomization.yaml no seu repositório com o seguinte conteúdo. Certifique-se de substituir <tag> pela versão correta que deseja instalar:

```
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - github.com/ansible/awx-operator/config/default?ref=<tag>

# Definir as tags de imagem para corresponder à versão do git escolhida
images:
  - name: quay.io/ansible/awx-operator
    newTag: <tag>
namespace: awx

```
Aplicar o arquivo de Kustomização
Execute o seguinte comando para implantar o AWX Operator no seu cluster:

```
kubectl apply -k .
```
Este comando instalará o AWX Operator e as Custom Resource Definitions (CRDs) necessárias.

Passo 4: Implantar a Instância do AWX
Assim que o AWX Operator estiver rodando, você pode implantar a instância do AWX.

Criar o Arquivo de Configuração da Instância do AWX
Crie um arquivo chamado awx-instance.yaml com o seguinte conteúdo. Você pode ajustar o service_type (ex.: NodePort, ClusterIP) dependendo das suas necessidades de acesso.

```
---
apiVersion: awx.ansible.com/v1beta1
kind: AWX
metadata:
  name: awx-demo
spec:
  service_type: nodeport  # Altere para 'clusterip' ou 'loadbalancer' se necessário
```
Aplicar a Configuração da Instância do AWX
Aplique a instância do AWX:

```
kubectl apply -f awx-instance.yaml
```
Isso criará os recursos necessários, incluindo o aplicativo web do AWX e o banco de dados PostgreSQL associado.

Passo 5: Acessar a Interface Web do AWX
Opção 1: Usando NodePort
Se você configurou service_type como NodePort, pode acessar o AWX usando a porta NodePort atribuída ao serviço. Execute o seguinte comando para encontrar a porta:

```
kubectl get svc -n awx
```
Encontre o NodePort na saída e acesse o AWX através da seguinte URL:

```
http://<IP-do-seu-cluster>:<NodePort>
```
Opção 2: Usando Port Forwarding
Se preferir, você também pode acessar o AWX utilizando port forwarding:

```
kubectl port-forward svc/awx-demo-service 8080:80 -n awx
```
Você pode então acessar o AWX em:

```
http://localhost:8080
```
Passo 6: Recuperar a Senha de Admin
Para fazer login na interface do AWX, você precisará da senha de administrador. Você pode recuperá-la utilizando o seguinte comando:

```
kubectl get secret awx-demo-admin-password -o jsonpath="{.data.password}" | base64 --decode
```
Passo 7: (Opcional) Monitorar os Logs do Operador AWX
Se encontrar algum problema, você pode verificar os logs do AWX Operator:

```
kubectl logs -f deployments/awx-operator-controller-manager -n awx -c awx-manager
```
Solução de Problemas
Se encontrar problemas durante a instalação, verifique o seguinte:

Conclusão
Parabéns! Você instalou com sucesso o AWX no seu cluster Kubernetes utilizando o AWX Operator. Agora você pode começar a criar e gerenciar seus playbooks Ansible através da interface web do AWX.

Notas
Sinta-se à vontade para modificar este README conforme necessário para o seu ambiente e configurações adicionais que você possa precisar.

