# Azure Container Apps Deploy Action

A GitHub Action for deploying containerized applications to Azure Container Apps.

## Features

- 🚀 Deploy container applications to Azure Container Apps
- 🔄 Automatically create or update Container Apps
- 🌐 Support for external ingress configuration
- 🔐 Secure authentication with Azure credentials
- ⚙️ Configurable CPU, memory, and scaling parameters
- 🐳 Support for private container registries
- 🔧 Environment variables configuration

## Prerequisites

- An Azure subscription
- An Azure Container Registry (ACR) or access to a container registry
- Azure Service Principal credentials with appropriate permissions

## Usage

### Basic Example

```yaml
name: Deploy to Azure Container Apps

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Deploy to Azure Container Apps
        uses: dinhbach306/azure-container-app-deploy@v1
        with:
          azure-credentials: ${{ secrets.AZURE_CREDENTIALS }}
          resource-group: 'my-resource-group'
          container-app-name: 'my-container-app'
          container-image: 'myregistry.azurecr.io/myapp:latest'
          container-app-environment: 'my-container-env'
          location: 'eastus'
```

### Advanced Example with Custom Configuration

```yaml
- name: Deploy to Azure Container Apps
  uses: dinhbach306/azure-container-app-deploy@v1
  with:
    azure-credentials: ${{ secrets.AZURE_CREDENTIALS }}
    resource-group: 'my-resource-group'
    container-app-name: 'my-container-app'
    container-image: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
    container-app-environment: 'my-container-env'
    location: 'eastus'
    target-port: '8080'
    ingress-enabled: 'true'
    registry-server: 'myregistry.azurecr.io'
    registry-username: ${{ secrets.REGISTRY_USERNAME }}
    registry-password: ${{ secrets.REGISTRY_PASSWORD }}
    cpu: '1.0'
    memory: '2.0Gi'
    min-replicas: '2'
    max-replicas: '20'
    environment-variables: '[{"name":"NODE_ENV","value":"production"},{"name":"API_KEY","secretRef":"api-key"}]'
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `azure-credentials` | Azure credentials (Service Principal) in JSON format | Yes | - |
| `resource-group` | Name of the Azure Resource Group | Yes | - |
| `container-app-name` | Name of the Azure Container App | Yes | - |
| `container-image` | Container image name with tag | Yes | - |
| `container-app-environment` | Name of the Azure Container App Environment | No | - |
| `location` | Azure region for deployment | No | `eastus` |
| `target-port` | Target port for the container application | No | `80` |
| `ingress-enabled` | Enable external ingress for the container app | No | `true` |
| `registry-server` | Container registry server | No | - |
| `registry-username` | Container registry username | No | - |
| `registry-password` | Container registry password | No | - |
| `cpu` | CPU allocation for the container | No | `0.5` |
| `memory` | Memory allocation for the container | No | `1.0Gi` |
| `min-replicas` | Minimum number of replicas | No | `1` |
| `max-replicas` | Maximum number of replicas | No | `10` |
| `environment-variables` | Environment variables in JSON format | No | - |

## Outputs

| Output | Description |
|--------|-------------|
| `container-app-url` | URL of the deployed Container App |
| `container-app-fqdn` | Fully Qualified Domain Name of the Container App |

## Setting up Azure Credentials

1. Create an Azure Service Principal:

```bash
az ad sp create-for-rbac --name "github-actions-sp" --role contributor \
  --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
  --sdk-auth
```

2. Copy the JSON output and add it as a secret named `AZURE_CREDENTIALS` in your GitHub repository.

The output should look like this:

```json
{
  "clientId": "<client-id>",
  "clientSecret": "<client-secret>",
  "subscriptionId": "<subscription-id>",
  "tenantId": "<tenant-id>"
}
```

## Examples

### Deploy with Environment Variables

```yaml
- name: Deploy with Environment Variables
  uses: dinhbach306/azure-container-app-deploy@v1
  with:
    azure-credentials: ${{ secrets.AZURE_CREDENTIALS }}
    resource-group: 'my-resource-group'
    container-app-name: 'my-app'
    container-image: 'myregistry.azurecr.io/myapp:v1.0'
    environment-variables: |
      [
        {"name": "DATABASE_URL", "value": "${{ secrets.DATABASE_URL }}"},
        {"name": "LOG_LEVEL", "value": "info"}
      ]
```

### Deploy with Private Registry

```yaml
- name: Deploy with Private Registry
  uses: dinhbach306/azure-container-app-deploy@v1
  with:
    azure-credentials: ${{ secrets.AZURE_CREDENTIALS }}
    resource-group: 'my-resource-group'
    container-app-name: 'my-app'
    container-image: 'myregistry.azurecr.io/myapp:latest'
    registry-server: 'myregistry.azurecr.io'
    registry-username: ${{ secrets.ACR_USERNAME }}
    registry-password: ${{ secrets.ACR_PASSWORD }}
```

## Troubleshooting

### Common Issues

1. **Authentication Failed**: Ensure your Azure credentials are correctly set up and have the necessary permissions.

2. **Container App Environment Not Found**: If you don't specify a `container-app-environment`, the action will use the default environment. Specify one explicitly if needed.

3. **Image Pull Failed**: Verify that:
   - The container image exists and is accessible
   - Registry credentials are correct (if using a private registry)
   - The registry server URL is correct

4. **Deployment Timeout**: Large container images may take longer to deploy. Consider:
   - Optimizing your container image size
   - Using multi-stage Docker builds
   - Checking Azure region availability

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT License - see the [LICENSE](LICENSE) file for details.

## Support

If you encounter any issues or have questions, please [open an issue](https://github.com/dinhbach306/azure-container-app-deploy/issues).