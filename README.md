# Setting up Kong using APIClarity w/ self-created Postgres db instance

## Steps

1. Create namespace

```
$ kubectl create namespace apiclarity-poc
```

2. Install ApiClarity Engine and Postgres database

```
$ cd apiclarity
$ helm install apiclarity ./ -n apiclarity-poc
```

3. Install Kong / Uninstall Kong

```
$ kubectl apply -f https://bit.ly/kong-ingress-dbless
$ kubectl delete -f https://bit.ly/kong-ingress-dbless
```

4. Configure External Routes for Kong using Ingress

- Will be using Github API as an example...
- Use the 2 config files in `kong_helpers` folder to configure...

```
$ kubectl apply -f githubIngress.yaml
$ kubectl apply -f githubService.yaml
```

5. Configure Kong plugin.

- `cd` to apiclarity src code kong deployment folder

```
KONG_PROXY_CONTAINER_NAME=proxy-kong \
KONG_GATEWAY_DEPLOYMENT_NAME=ingress-kong \
KONG_GATEWAY_DEPLOYMENT_NAMESPACE=kong \
KONG_GATEWAY_INGRESS_NAME=echo \
KONG_GATEWAY_INGRESS_NAMESPACE=default \
UPSTREAM_TELEMETRY_HOST_NAME=apiclarity-apiclarity.apiclarity:9000 \
deploy/deploy.sh
```

- uninstall

```
KONG_PROXY_CONTAINER_NAME=ingress-kong \
KONG_GATEWAY_DEPLOYMENT_NAME=kong \
KONG_GATEWAY_DEPLOYMENT_NAMESPACE=kong \
KONG_GATEWAY_INGRESS_NAME= \
KONG_GATEWAY_INGRESS_NAMESPACE=<namespace> ./deploy/undeploy.sh
```

## Commands and error....

```
$ KONG_GATEWAY_DEPLOYMENT_NAME=ingress-kong \
> KONG_GATEWAY_DEPLOYMENT_NAMESPACE=kong \
> KONG_GATEWAY_INGRESS_NAME=echo \
> KONG_GATEWAY_INGRESS_NAMESPACE=gavin-poc \
> UPSTREAM_TELEMETRY_HOST_NAME=apiclarity-apiclarity.apiclarity:9000 \
> deploy/deploy.sh
kongplugin.configuration.konghq.com/apiclarity-plugin created
configmap/kongsnapshot created
The Deployment "ingress-kong" is invalid: spec.template.spec.containers[0].image: Required value
ingress.networking.k8s.io/echo patched (no change)
```

## Debugging..
```
kubectl patch deployments.app -n kong ingress-kong --patch 
```