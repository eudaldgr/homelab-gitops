# Cert-Manager Cloudflare Configuration

## Crear Sealed Secret per Cloudflare API Token

### 1. Obtenir API Token de Cloudflare

1. Accedeix a https://dash.cloudflare.com/profile/api-tokens
2. Crea un token amb permisos:
   - **Zone - DNS - Edit**
   - **Zone - Zone - Read**

### 2. Crear el Secret (NO cometre'l!)

```bash
kubectl create secret generic cloudflare-api-token \
  --from-literal=api-token=YOUR_CLOUDFLARE_API_TOKEN \
  --namespace=cert-manager \
  --dry-run=client -o yaml > cloudflare-api-token-secret.yaml
```

### 3. Encriptar amb Sealed Secrets

```bash
kubeseal -f cloudflare-api-token-secret.yaml \
  -w apps/cert-manager/k8s-manifests/cloudflare-api-token-sealed.yaml \
  --controller-name=sealed-secrets-controller \
  --controller-namespace=sealed-secrets

# Eliminar el secret sense encriptar
rm cloudflare-api-token-secret.yaml
```

### 4. Cometre el Sealed Secret

```bash
git add apps/cert-manager/k8s-manifests/cloudflare-api-token-sealed.yaml
git commit -m "Add Cloudflare API token (sealed)"
git push
```

## Actualitzar l'Issuer

Edita `cloudflare-issuer.yaml` i canvia:
```yaml
email: YOUR_EMAIL@example.com  # ← El teu email de Cloudflare
```
