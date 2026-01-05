# Example: Simple Deployment

This example shows the simplest way to deploy a container to Azure Container Apps.

```yaml
name: Simple Deploy

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - uses: dinhbach306/azure-container-app-deploy@v1
        with:
          azure-credentials: ${{ secrets.AZURE_CREDENTIALS }}
          resource-group: 'my-resource-group'
          container-app-name: 'my-app'
          container-image: 'nginx:latest'
```

## What this does:

1. Triggers on pushes to the main branch
2. Checks out your code
3. Deploys the nginx:latest image to Azure Container Apps
4. Uses default settings:
   - Location: eastus
   - CPU: 0.5
   - Memory: 1.0Gi
   - Min replicas: 1
   - Max replicas: 10
   - Target port: 80
   - Ingress: enabled

## Prerequisites:

- Azure subscription
- Resource group created in Azure
- AZURE_CREDENTIALS secret configured in your repository
