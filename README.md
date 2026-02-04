# GET LAST STABLE VERSION
https://github.com/istio/istio

https://github.com/istio/istio/releases


# ADD HELM REPO
```sh
helm repo add istio https://istio-release.storage.googleapis.com/charts
```

# UPDATE HELM CHART AND CREATE DIRECTORY
```sh
helm repo update
export VERSION=1.28.3
mkdir  ${VERSION}
```

# INSTALAR O ISTIO BASE (CRD)
```sh
helm template istio-base  istio/base \
    --version ${VERSION} \
    --namespace istio-system > ./${VERSION}/istio-base.yaml
```

# INSTALAR ISTIOD (CONTROL PLANE) COM HA E PERSISTENT SESSION
```sh
helm template istiod istio/istiod \
  --version ${VERSION} \
  --namespace istio-system \
  --set pilot.autoscaleEnabled=true \
  --set pilot.autoscaleMin=2 \
  --set pilot.autoscaleMax=5 \
  --set pilot.resources.requests.cpu=500m \
  --set pilot.resources.requests.memory=2Gi \
  --set pilot.env.PILOT_ENABLE_PERSISTENT_SESSION_FILTER=true > ./${VERSION}/istiod.yaml
```

# VERFICAR SE A VARIAVEL DE AMBIENTE FOI CRIADA NO DEPLOYMENT DO ISTIOD PARA SESSÃO PERSISTENTES
kubectl get deploy istiod -n istio-system -o yaml | grep PILOT_ENABLE_PERSISTENT_SESSION_FILTER -A1