create cluster
```shell
k3d cluster create -c k3d-cluster.yaml
```

install knative operator
```shell
curl -sSL https://github.com/knative/operator/releases/download/knative-v1.14.0/operator.yaml \
  | grep 'gcr.io/' | awk '{print $2}' | sort | uniq \
  | xargs -n 1 \
    cosign verify -o text \
      --certificate-identity=signer@knative-releases.iam.gserviceaccount.com \
      --certificate-oidc-issuer=https://accounts.google.com

kubectl apply -f https://github.com/knative/operator/releases/download/knative-v1.14.0/operator.yaml
```
for some reason, traefik is installed as soon as we do this.
this interferes with the kourier loadbalancer we need for knative

NOTE: other lb alternatives exist. traefik does not support knative atm
[REF NEEDED: GITHUB ISSUE]
```shell
kubectl delete -n kube-system traefik
```

install serving
```shell
kubectl apply -f knative-serving.yaml
```
