# 🛡️ Laboratório Istio - AuthorizationPolicy no Minikube

Este laboratório tem como objetivo ensinar na prática como utilizar o recurso **AuthorizationPolicy** do **Istio**, controlando o acesso entre serviços em uma malha de serviços. Vamos usar o **Minikube** para criar um ambiente local.

---

## 🎯 Objetivo

- Demonstrar como usar o recurso **AuthorizationPolicy** para:
  - **Negar todo acesso por padrão**
  - **Permitir acesso apenas a serviços específicos**
- Visualizar as políticas usando o **Kiali**
- Realizar uma enquete baseada em um caso de uso real

---

## 📋 Pré-requisitos

- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Istioctl](https://istio.io/latest/docs/setup/getting-started/#download)

---

## 🚀 Etapas do Laboratório

### 1. Inicie o Minikube

```bash
minikube start --driver=kvm2 --cni=flannel --cpus=4 --memory=8000 -p=lab1
```

### 2. Instale o Istio

```bash
istioctl install --set profile=demo -y
```

### 3. Habilite a injeção automática de sidecars

```bash
kubectl label namespace default istio-injection=enabled
```

---

## 4. Deploy da Aplicação ```httpbin```

Execute:

```bash
kubectl apply -f app1.yaml
```

---

---

## 5. Deploy da Aplicação ```sleep```

Execute:

```bash
kubectl apply -f app2.yaml
```

---

## 6. 🔎 Testes sem AuthorizationPolicy (acesso liberado)

Testar comunicação entre sleep e httpbin:

```bash
kubectl exec deploy/sleep -c sleep -- curl -sS httpbin:8000/ip
```

✔️ Deve funcionar e retornar um JSON com o IP.

---

## 7. 🚫 Aplicando AuthorizationPolicy para bloquear todo acesso

Execute:

```bash
kubectl apply -f deny-all.yaml
```

Testar novamente:

```bash
kubectl exec deploy/sleep -c sleep -- curl -sS -o /dev/null -w "%{http_code}\n" httpbin:8000/ip
```

❌ Resultado esperado: ```403```

---

## 8. ✅ Aplicando AuthorizationPolicy para permitir acesso do serviço

```bash
kubectl apply -f allow-sleep.yaml
```

Testar novamente:

```bash
kubectl exec deploy/sleep -c sleep -- curl -sS -o /dev/null -w "%{http_code}\n" httpbin:8000/ip
```

✔️ Resultado esperado: ```200```

---

## 9. 🔒 Teste de serviço não autorizado

Criar um pod fora da autorização (ex.: busybox):

```bash
kubectl run busybox --image=busybox -it --rm --restart=Never -- sh
```

Testar dentro do pod:

```bash
wget -qO- httpbin.default.svc.cluster.local:8000/ip
```

---

## 🔍 Observabilidade com Kiali

1. Acesse [http://localhost:20001](http://localhost:20001)
2. Navegue até a aplicação `httpbin`
3. Visualize o tráfego entre as versões e os percentuais de roteamento
4. Use filtros e animações para entender o comportamento

---

## 📚 Recursos adicionais

- [Documentação do Istio](https://istio.io/latest/docs/)
- [Kiali](https://kiali.io/)
- [HTTPBin (test server)](https://httpbin.org/)

---

## 👨‍🏫 Créditos

Este laboratório foi desenvolvido para fins educacionais como parte da palestra sobre **Fundamentos de Istio**.

Fique à vontade para compartilhar e adaptar!