# Example: Deployment with Private Registry

This example demonstrates deploying a container from a private Azure Container Registry.

```yaml
name: Deploy from Private Registry

on:
  workflow_dispatch:

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Build and push to ACR
        run: |
          az acr login --name myregistry
          docker build -t myregistry.azurecr.io/myapp:${{ github.sha }} .
          docker push myregistry.azurecr.io/myapp:${{ github.sha }}
      
      - name: Deploy to Container Apps
        uses: dinhbach306/azure-container-app-deploy@v1
        with:
          azure-credentials: ${{ secrets.AZURE_CREDENTIALS }}
          resource-group: 'production-rg'
          container-app-name: 'my-production-app'
          container-image: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
          container-app-environment: 'production-env'
          registry-server: 'myregistry.azurecr.io'
          registry-username: ${{ secrets.ACR_USERNAME }}
          registry-password: ${{ secrets.ACR_PASSWORD }}
          cpu: '2.0'
          memory: '4.0Gi'
          min-replicas: '3'
          max-replicas: '20'
```

## Key Points:

1. **Registry Authentication**: Uses registry credentials to access private images
2. **Image Tagging**: Uses git SHA for unique versioning
3. **Resource Sizing**: Configured for production workloads with higher CPU/memory
4. **Scaling**: Higher replica counts for production availability

## Required Secrets:

- `AZURE_CREDENTIALS`: Azure Service Principal credentials
- `ACR_USERNAME`: Azure Container Registry username
- `ACR_PASSWORD`: Azure Container Registry password

## Best Practices:

- Always tag images with unique identifiers (SHA, version, etc.)
- Use environment-specific resource groups
- Configure appropriate scaling parameters for your workload
- Store sensitive credentials in GitHub Secrets
