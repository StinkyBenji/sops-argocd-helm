# Argo CD secret management examples

## Prerequisites
- CLI: sops, age, helm, oc/kubectl, kustomize
- (optional) VSCode extension: @signageos/vscode-sops-beta

## SOPS - Secrets OPerationS
[sops](https://github.com/mozilla/sops) is an editor of encrypted files that supports YAML, JSON, ENV, INI and BINARY 
formats and encrypts with AWS KMS, GCP KMS, Azure Key Vault, age, and PGP.

### Encryptin with age
This example uses [age](https://age-encryption.org/) to encrypt secret files. 
You can use the following command to generate the key file.
```
age-keygen -o key.txt
```
On macOS, please run
```
mkdir -p $HOME/Library/Application Support/sops/age/
mv key.txt $HOME/Library/Application Support/sops/age/keys.txt
export SOPS_AGE_KEY_FILE=$HOME/Library/Application Support/sops/age/keys.txt
source ~/.zshrc
```
For other OS users, please refer to the [sops](https://github.com/mozilla/sops#encrypting-using-age) for detailed information.

You can encrypt a file for one or more age recipients (comma separated) using the `--age` option 
or the **SOPS_AGE_RECIPIENTS** environment variable:
```
sops --encrypt --age <your-age-public-key> secret.yaml > secret.enc.yaml
```
and decrypt it using:

```
sops --decrypt secret.enc.yaml
```
### Configure sops using .sops.yaml



## KSOPS 
[ksops](https://github.com/viaduct-ai/kustomize-sops) is a Flexible Kustomize Plugin 
for SOPS Encrypted Resource. It can be [integrated with Argo CD](https://github.com/viaduct-ai/kustomize-sops#argo-cd-integration-)
to manage the secrets.

### Generator options
KSOPS supports kustomize exec plugins annotation based generator options. 
The supported annotations are:

`kustomize.config.k8s.io/needs-hash`
`kustomize.config.k8s.io/behavior`

### Encrypted secret overlays w/ generator options
If you want to replace the default secrets in your overlay or you want to partially 
update, or merge the secrets, You can add the following annotations to your encrypted secrets:
```
apiVersion: v1
kind: Secret
metadata:
  name: argocd-secret
  annotations:
    # replace the base secret data/stringData values with these encrypted data/stringData values
    kustomize.config.k8s.io/behavior: replace 
    # kustomize.config.k8s.io/behavior: merge # (for merge secrets)
type: Opaque
data:
  # Encrypted data here
stringData:
  # Encrypted data here
....
```


## Useful links
- https://github.com/viaduct-ai/kustomize-sops
- https://github.com/argoproj-labs/argocd-vault-plugin
- https://github.com/FiloSottile/age
- https://github.com/mozilla/sops
- https://github.com/jkroepke/helm-secrets/wiki/ArgoCD-Integration
- https://hackernoon.com/how-to-handle-kubernetes-secrets-with-argocd-and-sops-r92d3wt1
- https://github.com/kubernetes-sigs/kustomize/blob/fd7a353df6cece4629b8e8ad56b71e30636f38fc/examples/kvSourceGoPlugin.md#secret-values-from-anywhere
