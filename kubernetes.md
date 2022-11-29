# Criando um cluster

## No Minikube
```shell
sudo usermod -aG docker $USER | newgrp docker
minikube start
```

# No kind
```shell
kind create cluster
```

# Obtendo informações sobre o cluster
```shell
kubectl cluster-info default
```

# Deployment

## Criando um deployment via CLI
```shell
kubectl create deployment nginx-deployment --image=nginx
```

## Descobrindo os objetos criados
```shell
kubectl get all
kubectl get deployment
kubectl get replicaset
kubectl get pod
kubectl get pod -o wide
```

## Descobrindo informações sobre os objetos
Para obter informações de logs gerados em um pod:
```shell
kubectl logs pod_id
```

Para obter os metadados de um pod:
```shell
kubectl describe pod pod_id
```

O comando `describe` pode ser utilizado para outros tipos de objetos do kubernetes como `deployment`, `service`, etc.

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

## Abrindo um terminal e interagindo com o pod
```shell
kubectl exec -it pod_id -- bin/bash
```

## Alterando on the fly o objeto deployment
```shell
kubectl edit deployment nginx-deployment
kubectl delete deployment nginx-deployment
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