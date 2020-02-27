---
title: 使用 GitHub 動作來建立、測試容器，並將其部署至 Azure Kubernetes Service
description: 瞭解如何使用 GitHub 動作將您的容器部署至 Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595360"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>部署至 Kubernetes 服務的 GitHub 動作

[GitHub 動作](https://help.github.com/en/articles/about-github-actions)可讓您彈性地建立自動化軟體發展生命週期工作流程。 Kubernetes 動作[azure/aks-set-context@v1](https://github.com/Azure/aks-set-context)有助於 Azure Kubernetes Service 叢集的部署。 動作會設定目標 AKS 叢集內容，其可供其他動作使用，例如[azure/k8s-deploy](https://github.com/Azure/k8s-deploy/tree/master)、 [azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master)等，或執行任何 kubectl 命令。

工作流程是由存放庫中 `/.github/workflows/` 路徑中的 YAML （. yml）檔案所定義。 此定義包含組成工作流程的各種步驟和參數。

針對目標為 AKS 的工作流程，檔案有三個區段：

|區段  |工作  |
|---------|---------|
|**驗證** | 登入私人容器登錄（ACR） |
|**建置** | 組建 & 推送容器映射  |
|**部署** | 1. 設定目標 AKS 叢集 |
| |2. 在 Kubernetes 叢集中建立 generic/docker-登錄秘密  |
||3. 部署至 Kubernetes 叢集|

## <a name="create-a-service-principal"></a>建立服務主體

您可以使用[Azure CLI](https://docs.microsoft.com/cli/azure/)中的[az ad sp create-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令來建立[服務主體](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 您可以使用 Azure 入口網站中的[Azure Cloud Shell](https://shell.azure.com/)或選取 [**試試看**] 按鈕來執行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

在上述命令中，將預留位置取代為您的訂用帳戶識別碼和資源群組。 輸出是可提供資源存取權的角色指派認證。 此命令應輸出類似于此的 JSON 物件。

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
複製這個 JSON 物件，您可以使用它從 GitHub 進行驗證。

## <a name="configure-the-github-secrets"></a>設定 GitHub 秘密

請遵循下列步驟來設定密碼：

1. 在[GitHub](https://github.com/)中，流覽至您的存放庫，選取 [設定] **> 秘密 > 新增密碼**。

    ![密碼](media/kubernetes-action/secrets.png)

2. 將上述 `az cli` 命令的內容貼入 [秘密變數] 的值。 例如： `AZURE_CREDENTIALS` 。

3. 同樣地，針對容器登錄認證定義下列額外的秘密，並在 Docker 登入動作中加以設定。 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. 定義之後，您將會看到如下所示的秘密。

    ![kubernetes-秘密](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>建立容器映射並部署到 Azure Kubernetes Service 叢集

容器映射的組建和推送是使用 `Azure/docker-login@v1` 動作來完成。 若要將容器映射部署至 AKS，您必須使用 `Azure/k8s-deploy@v1` 動作。 此動作有五個參數：

| **參數**  | **說明**  |
|---------|---------|
| **namespace** | 選擇性選擇目標 Kubernetes 命名空間。 如果未提供命名空間，則命令會在預設命名空間中執行 | 
| **mdac** |  具備資訊清單檔案的路徑，將用於部署 |
| **images** | 選擇性要在資訊清單檔上用來替代之影像的完整資源 URL |
| **imagepullsecrets** | 選擇性已在叢集內設定的 docker 登錄密碼名稱。 在輸入資訊清單檔中找到的工作負載的 [imagePullSecrets] 欄位下，會新增每個秘密名稱 |
| **kubectl-版本** | 選擇性安裝特定版本的 kubectl 二進位檔 |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>部署到 Azure Kubernetes Service 叢集

建立容器映射和部署到 Azure Kubernetes Service 叢集的端對端工作流程。

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>後續步驟

您可以在 GitHub 上的不同存放庫中找到這組動作，其中每一個都包含檔和範例，以協助您使用 GitHub 來進行 CI/CD，並將您的應用程式部署至 Azure。

- [安裝程式-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-設定內容](https://github.com/Azure/k8s-set-context)

- [aks-設定內容](https://github.com/Azure/aks-set-context)

- [k8s-建立密碼](https://github.com/Azure/k8s-create-secret)

- [k8s-部署](https://github.com/Azure/k8s-deploy)

- [webapps-容器-部署](https://github.com/Azure/webapps-container-deploy)

- [動作-工作流程-範例](https://github.com/Azure/actions-workflow-samples)
