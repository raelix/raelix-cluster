# raelix-cluster

```
helm repo add argo https://argoproj.github.io/argo-helm
helm upgrade argo-cd infrastructure/production/argo-cd --version 4.5.11 --namespace argocd --create-namespace --install 
helm upgrade argo-cd infrastructure/production/argo-cd --version 4.5.11 --namespace argocd --create-namespace --install  --reuse-values -f infrastructure/production/argo-cd/argocd-values-after.yaml

NOTES:
In order to access the server UI you have the following options:

1. kubectl port-forward service/argo-cd-argocd-server -n argocd 8080:443

    and then open the browser on http://localhost:8080 and accept the certificate

2. enable ingress in the values file `server.ingress.enabled` and either
      - Add the annotation for ssl passthrough: https://github.com/argoproj/argo-cd/blob/master/docs/operator-manual/ingress.md#option-1-ssl-passthrough
      - Add the `--insecure` flag to `server.extraArgs` in the values file and terminate SSL at your ingress: https://github.com/argoproj/argo-cd/blob/master/docs/operator-manual/ingress.md#option-2-multiple-ingress-objects-and-hosts


After reaching the UI the first time you can login with username: admin and the random password generated during the installation. You can find the password by running:

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

(You should delete the initial secret afterwards as suggested by the Getting Started Guide: https://github.com/argoproj/argo-cd/blob/master/docs/getting_started.md#4-login-using-the-cli)
```
## Replaced keys
A key can be generated early in two ways
1. Label the current latest key as compromised (any value other than active)
`kubectl label secrets <keyname> sealedsecrets.bitnami.com/sealed-secrets-key=compromised`.
2. (since v0.9.3) pass current timestamp to the controller into a flag called `--key-cutoff-time` or an env var called `SEALED_SECRETS_KEY_CUTOFF_TIME`. Expected format is RFC1123, you can generate it with the `date -R` unix command.

Seal secrets
```bash
cat secret.yaml | \
kubeseal --controller-name sealed-secrets-controller --controller-namespace kube-system  --format yaml \
> sealed-secret.yaml
```

## TODO
[ X ] - Fix group in OID keycloak
[ X ] - Add sealed secrets
[ ] - Create a common launcher
[ ] - use one entrypoint even for home assistant?