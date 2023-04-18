# 1. O que é?

[Kubernetes](https://kubernetes.io/pt-br/) (K8s) é um produto Open Source utilizado para automatizar a implantação, o dimensionamento e o gerenciamento de aplicativos em contêiner.

# 2. Criando um cluster

Para execução dos nossos exemplos práticos vamos utilizar a ferramenta [minikube](https://minikube.sigs.k8s.io/docs/start/). Mas existem outras opções também bastante conhecidas como [Kind](https://kind.sigs.k8s.io/)

## 2.1. No Minikube
Para executar o minikube faz-se necessário que o usuário do sistema operacional pertença ao grupo `docker`. 
```shell
sudo usermod -aG docker $USER | newgrp docker
minikube start
```

## 2.2. No kind
```shell
kind create cluster
```

## 2.3. Obtendo informações sobre o cluster
Para que possamos interagir com o cluster vamos utilizar uma ferramenta de linha de comando (CLI) do Kubernetes, o [kubectl](https://kubernetes.io/docs/tasks/tools/).

Agora podemos acessar a API do kubernetes e interagir com o cluster. Por exemplo, podemos obter informações sobre o cluster que foi criado com minikube.
```shell
kubectl cluster-info default
```

# 3. Componentes do Kubernetes

Kubernetes possui diversos componentes que podem ser criados e configurados, a relação que segue apresenta alguns componentes principais:

- Pod
- Deployment
- Service
- ConfigMap
- Secrets
- Ingress
- Volumes
- StatefulSet

## 3.2. Pod
É a menor unidade K8s e representa uma abstração de um contêiner, para que o kubernetes seja independente da tecnologia de contêiner que esteja sendo utilizada.

Toda a interação ocorrerá sobre essa camada do kubernetes e não diretamente com a tecnologia do contêiner.

Apesar de um `pod` poder conter mais de um contêiner, normalmente teremos um único contêiner em um `pod`.

Outro aspecto importante é que cada `pod` recebe um endereço IP e não o contêiner.

Pods são efêmeros, podem ser descartados e criados a qualquer momento. A cada nova criação teremos um novo endereço IP associado.

## 3.1. Deployment
Trata-se de um *blueprint* para criação de `pods`. No kubernetes não criamos `pods` diretamente, o fazemos por meio do componente `deployment`, que permite definir/especificar diversas características sobre o `pod` ou `pods` que serão criados no kubernetes. Pelo menos precisamos especificar o nome desse componente e a imagem que será utilizada.


### 3.1.1. Criando um deployment via CLI
O comando que segue cria um `deployment` chamado `nginx-deployment` que é instanciado utilizando uma imagem atual do `nginx`:
```shell
kubectl create deployment nginx-deployment --image=nginx
```

### 3.1.2. Descobrindo os objetos criados
Os comandos que seguem apresentam como descobrir quais componentes foram criados. Temos comandos que permitem descobrir componentes específicos como `deployment`, `replicaset`, `pod`, ... ou para obter uma lista geral `all`:

```shell
kubectl get deployment
kubectl get replicaset
kubectl get pod
kubectl get pod -o wide
kubectl get all
```

### 3.1.3. Alterando on the fly o objeto deployment
Para alteração ou até mesmo a exclusão de um componente `deployment`, segue exemplos:
```shell
kubectl edit deployment nginx-deployment
kubectl delete deployment nginx-deployment
```
Ao alterar ou excluir um deployment, perceba que as alterações são propagadas para os componentes relacionados como `pods` ou `replicasets`, sendo estes atualizados ou excluídos.

### 3.1.4. Descobrindo informações sobre os objetos
Os comandos anteriores nos permitem descobrir quais componentes foram criados, mas não nos dizem muito sobre o que está ocorrendo com o componente, quais são as suas configurações ou status mais detalhado.

Para obter informações de logs gerados em um pod:
```shell
kubectl logs pod_id
```

Para obter os metadados de um pod:
```shell
kubectl describe pod pod_id
```

O comando `describe` pode ser utilizado para outros tipos de objetos do kubernetes como `deployment`, `service`, etc.

### 3.1.5. Abrindo um terminal e interagindo com o pod
Caso queira interagir com um `pod` você pode abrir um terminal e acessá-lo internamente:
```shell
kubectl exec -it pod_id -- bin/bash
```

## 3.2. Service
Um serviço Kubernetes (`service`) é uma abstração lógica para um grupo de pods em um cluster (que executam a mesma função).

Como os pods são efêmeros, um serviço permite que um grupo de pods receba um nome e um endereço IP exclusivo (`clusterIP`). Enquanto o serviço estiver executando, esse endereço IP não será alterado. Os serviços também definem políticas para seu acesso.

## Expondo a aplicação para acesso externo
Para criar um serviço para um pod e expor uma porta para que seja acessível pelo cluster:
```shell
kubectl expose deployment nginx-deployment --type=NodePort --port=80
```

Para permitir o acesso externo a um pod, para fins de teste pode ser feito um direcionamento:
```shell
kubectl port-forward service/nginx-deployment 7080:80
```

De forma alternativa você pode solicitar ao minikube para expor e abrir o navegador para você:
```shell
minikube service nginx-deployment
```

## Criando os arquivos de configuração dos componentes Kubernetes
```shell
kubectl apply -f file.yaml
```

## Criando a secret para armazenar nossas senhas
```shell
echo -n 'senha' | base64
```

## Permitindo o acesso externo a serviços no Kubernetes
```shell
kubectl port-forward service/mongo-express-service 8081:8081
kubectl patch svc mongo-express-service -p '{"spec":{"externalIPs":["192.168.15.42"]}}'
```

Para instalar o kubens para definir o namespace padrão.
```shell
sudo snap install kubectx --classic
```

Para instalar e criar o pod k8s-nginx-ingress-controller.
minikube addons enable ingress