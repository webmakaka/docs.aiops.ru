---
layout: page
title: Kubernetes for Generative AI Solutions
permalink: /
---

# Kubernetes for Generative AI Solutions

<br/>

// GitHub  
https://github.com/webmakaka/Kubernetes-for-Generative-AI-Solutions/tree/main

<br/>

### Подготовка окружения

<br/>

// HuggingFace
https://huggingface.co/docs/huggingface_hub/en/installation

<br/>

```
$ pip install --upgrade huggingface_hub
```

<br/>

```
// https://huggingface.co/docs/huggingface_hub/en/guides/cli
$ curl -LsSf https://hf.co/cli/install.sh | bash


// GENERATE TOKEN
https://huggingface.co/settings/tokens

$ hf auth login
```

<br/>

### Запуск в docker

<br/>

```
$ git clone github.com:webmakaka/Kubernetes-for-Generative-AI-Solutions.git

$ cd Kubernetes-for-Generative-AI-Solutions/ch02
```

<br/>

```
$ hf download TheBloke/Llama-2-7B-Chat-GGUF llama-2-7b-chat.Q2_K.gguf --local-dir .
```

<br/>

```
$ docker build -t my-llama .
```

<br/>

```
$ docker tag my-llama webmakaka/my-llama
$ docker push webmakaka/my-llama
```

<br/>

```
$ docker run -p 8000:5000 webmakaka/my-llama
```

<br/>

```
// OK!
$ curl -X POST http://localhost:8000/predict \
  -H "Content-Type: application/json" \
  -d '{"prompt":"Create a poem about humanity?","sys_msg":"You are a helpful, respectful, and honest assistant. Always provide safe, unbiased, and positive responses. Avoid harmful, unethical, or illegal content. If a question is unclear or incorrect, explain why. If unsure, do not provide false information."}' \
  | jq .
```

<br/>

### Повтор в kubernetes

<br/>

Minikube + Metal LB

<br/>

```yaml
$ kubectl create deploy my-llama --image webmakaka/my-llama
```

<br/>

```
$ kubectl get pods
NAME                        READY   STATUS    RESTARTS   AGE
my-llama-8649ff89c6-djdvd   1/1     Running   0          5m35s
```

<br/>

```yaml
$ cat << 'EOF' | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  labels:
    app: my-llama-svc
  name: my-llama-svc
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "external"
    service.beta.kubernetes.io/aws-load-balancer-scheme: "internet-facing"
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 5000
  type: LoadBalancer
  selector:
    app: my-llama
EOF
```

<br/>

```
$ export NLB_URL=$(kubectl get svc my-llama-svc -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
$ echo ${NLB_URL}
```

<br/>

```
// OK!
$ curl -X POST http://${NLB_URL}/predict \
  -H "Content-Type: application/json" \
  -d '{"prompt":"Create a poem about humanity?","sys_msg":"You are a helpful, respectful, and honest assistant. Always provide safe, unbiased, and positive responses. Avoid harmful, unethical, or illegal content. If a question is unclear or incorrect, explain why. If unsure, do not provide false information."}' \
  | jq .
```
