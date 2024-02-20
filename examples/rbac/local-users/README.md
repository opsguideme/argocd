# Local users/accounts

Authentication method for testing and development purposes.

[From documentation](https://argo-cd.readthedocs.io/en/stable/operator-manual/user-management/#local-usersaccounts):

The local users/accounts feature serves two main use cases:
- Auth tokens for Argo CD management automation. It is possible to configure an API account with limited permissions and generate an authentication token. Such token can be used to automatically create applications, projects etc.
- Additional users for a very small team where use of SSO integration might be considered an overkill. The local users don't provide advanced features such as groups, login history etc. So if you need such features it is strongly recommended to use SSO.


## Adding local account to the argocd-cm ConfigMap

```bash
kubectl patch -n argocd cm argocd-cm --type=json --patch '[{ "op": "add", "path": "/data/accounts.john", "value": "login"}]'
```

## Assigning readonly applications and projects permissions to john account

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.tester-overlay.csv: |
    p, role:read-only, applications, get, */*, allow
    p, role:read-only, projects, get, *, allow
    g, john, role:read-only
```
## Updating john password

```bash
argocd account update-password --account john
```

## Login as john

```bash
argocd login --username=john
```

## Check john's permissions
```bash
argocd account can-i get applications '*'
argocd account can-i get projects '*'
argocd account can-i sync applications '*'
argocd account can-i create clusters '*'
```