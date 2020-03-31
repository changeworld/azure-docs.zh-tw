---
title: 使用 GitHub 操作將容器構建、測試和部署到 Azure 庫伯內斯服務
description: 瞭解如何使用 GitHub 操作將容器部署到庫貝內特斯
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595360"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>用於部署到庫伯奈斯服務的 GitHub 操作

[GitHub 操作](https://help.github.com/en/articles/about-github-actions)使您能夠靈活地構建自動化的軟體發展生命週期工作流。 庫伯內斯操作[azure/aks-set-context@v1](https://github.com/Azure/aks-set-context)有助於部署到 Azure 庫伯奈斯服務群集。 該操作設置目標 AKS 群集上下文，該上下文可用於其他操作，如[azure/k8s](https://github.com/Azure/k8s-deploy/tree/master)部署[、azure/k8s 創建-機密](https://github.com/Azure/k8s-create-secret/tree/master)等，或運行任何 kubectl 命令。

工作流由存儲庫中路徑中的`/.github/workflows/`YAML （.yml） 檔定義。 此定義包含構成工作流的各種步驟和參數。

對於面向 AKS 的工作流，該檔有三個部分：

|區段  |工作  |
|---------|---------|
|[驗證]**** | 登錄到專用容器註冊表 （ACR） |
|**建立** | 生成&推送容器映射  |
|**部署** | 1. 設置目標 AKS 群集 |
| |2. 在庫伯內斯群集中創建通用/docker 註冊金鑰  |
||3. 部署到庫伯奈斯群集|

## <a name="create-a-service-principal"></a>建立服務主體

可以使用[Azure CLI](https://docs.microsoft.com/cli/azure/)中的[az ad sp 創建 rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令創建[服務主體](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 可以使用 Azure 門戶中的[Azure 雲外殼](https://shell.azure.com/)運行此命令，也可以選擇"**試用"** 按鈕。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

在上面的命令中，將預留位置替換為訂閱 ID 和資源組。 輸出是提供對資源存取權限的角色指派憑據。 該命令應輸出與此類似的 JSON 物件。

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
複製此 JSON 物件，可用於從 GitHub 進行身份驗證。

## <a name="configure-the-github-secrets"></a>配置 GitHub 機密

按照步驟配置機密：

1. 在[GitHub](https://github.com/)中，流覽到您的存儲庫，選擇 **"設置>機密>添加新機密**。

    ![密碼](media/kubernetes-action/secrets.png)

2. 將上述`az cli`命令的內容粘貼為機密變數的值。 例如： `AZURE_CREDENTIALS` 。

3. 同樣，為容器註冊表憑據定義以下其他機密，並在 Docker 登錄操作中設置它們。 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. 定義後，您將看到如下所示的機密。

    ![庫伯內斯-秘密](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>生成容器映射並部署到 Azure 庫伯奈斯服務群集

容器映射的生成和推送使用`Azure/docker-login@v1`操作完成。 要將容器映射部署到 AKS，您需要使用該`Azure/k8s-deploy@v1`操作。 此操作有五個參數：

| **參數**  | **說明**  |
|---------|---------|
| **namespace** | （可選）選擇目標庫伯內斯命名空間。 如果未提供命名空間，則命令將在預設命名空間中運行 | 
| **體現** |  （必需）清單檔的路徑，將用於部署 |
| **圖像** | （可選）用於清單檔替換的圖像的完全限定資源 URL |
| **圖像拉秘** | （可選）已在群集中設置的 Docker 註冊表機密的名稱。 這些機密名稱中的每一個都添加到 imagePullSecrets 欄位下，用於輸入清單檔中找到的工作負載 |
| **庫布克特爾版本** | （可選）安裝庫布克特爾二進位的特定版本 |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>部署到 Azure 庫伯奈斯服務群集

端到端工作流，用於構建容器映射並部署到 Azure 庫伯奈斯服務群集。

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

您可以在 GitHub 上的不同存儲庫中找到我們的操作集，每個存儲庫都包含文檔和示例，以説明您使用 GitHub 進行 CI/CD 並將應用部署到 Azure。

- [設置-庫布ectl](https://github.com/Azure/setup-kubectl)

- [k8s 設置上下文](https://github.com/Azure/k8s-set-context)

- [aks 設置上下文](https://github.com/Azure/aks-set-context)

- [k8s-創建-秘密](https://github.com/Azure/k8s-create-secret)

- [k8s 部署](https://github.com/Azure/k8s-deploy)

- [網路應用-容器部署](https://github.com/Azure/webapps-container-deploy)

- [操作-工作流-示例](https://github.com/Azure/actions-workflow-samples)
