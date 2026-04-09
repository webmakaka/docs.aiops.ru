---
layout: page
title: Запуск модели qwen в MiniKube
description: Запуск модели qwen в MiniKube
keywords: ubuntu, containers, kubernetes, minikube, run, gpu
permalink: /llm/models/qwen/
---

# Запуск модели qwen в MiniKube

<br/>

**Минут 6 устанавливаетя.**

```yaml
$ cat << EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  name: qwen-service
spec:
  type: NodePort
  selector:
    app: qwen
  ports:
    - port: 11434
      targetPort: 11434
      nodePort: 30434
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: qwen-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: qwen
  template:
    metadata:
      labels:
        app: qwen
    spec:
      containers:
        - name: ollama
          image: ollama/ollama:latest
          resources:
            limits:
              nvidia.com/gpu: 1 # Тот самый лимит, который мы настраивали!
          ports:
            - containerPort: 11434
          env:
            - name: OLLAMA_MODELS
              value: '/root/.ollama'
EOF
```

<br/>

```shell
$ kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
qwen-deployment-d57b558dd-w9vwd   1/1     Running   0          6m40s
```

<br/>

```shell
$ kubectl exec -it $(kubectl get pod -l app=qwen -o name) -- ollama run qwen2.5:1.5b
```

<br/>

```shell
$ minikube ip
```

<br/>

```shell
$ curl http://192.168.49.2:30434/api/generate -d '{
  "model": "qwen2.5:1.5b",
  "prompt": "Привет! Ты работаешь на моей GTX 1650 внутри Kubernetes?",
  "stream": false
}' | jq -r '.response'
```

**response:**

```
Да, я работаю в вашем кластере Kubernetes с NVIDIA GeForce GTX 1650 видеокартой для выполнения задачи. Настройка и управление рабочими нагрузками на GPU у меня не вызывает проблем, так как это мой основной функционал в текущей роли
```
