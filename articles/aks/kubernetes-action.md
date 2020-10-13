---
title: 使用 GitHub Actions 建立、測試及部署容器至 Azure Kubernetes Service
description: 瞭解如何使用 GitHub Actions 將您的容器部署至 Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 7743a3a8d6e77affd6229b648ab79b5b2f07a0af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564095"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>部署至 Kubernetes 服務的 GitHub Actions

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) 可讓您彈性地建置自動化軟體開發生命週期工作流程。 Kubernetes 動作 [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) 可促進 Azure Kubernetes Service 叢集的部署。 此動作會設定目標 AKS 叢集內容，此內容可供其他動作（例如 [azure/k8s-部署](https://github.com/Azure/k8s-deploy/tree/master)、 [azure/k8s-建立秘密](https://github.com/Azure/k8s-create-secret/tree/master) 等）使用，或執行任何 kubectl 命令。

工作流程是由您存放庫內 `/.github/workflows/` 路徑中的 YAML (. yml) 檔案所定義的。 此定義包含組成工作流程的各種步驟與參數。

針對以 AKS 為目標的工作流程，檔案有三個區段：

|區段  |工作  |
|---------|---------|
|**驗證** | 登入私人容器登錄 (ACR)  |
|**建置** | 組建 & 推送容器映射  |
|**部署** | 1. 設定目標 AKS 叢集 |
| |2. 在 Kubernetes 叢集中建立泛型/docker 登錄秘密  |
||3. 部署至 Kubernetes 叢集|

## <a name="create-a-service-principal"></a>建立服務主體

您可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令來建立[服務主體](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 您可以使用 Azure 入口網站中的 [Azure Cloud Shell](https://shell.azure.com/)，或選取 [試試看] 按鈕來執行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

在上述命令中，將預留位置取代為您的訂用帳戶識別碼和資源群組。 輸出是提供資源存取權的角色指派認證。 此命令應該會輸出類似下面的 JSON 物件。

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
複製這個 JSON 物件，您可以用它從 GitHub 進行驗證。

## <a name="configure-the-github-secrets"></a>設定 GitHub 秘密

遵循下列步驟來設定秘密：

1. 在 [GitHub](https://github.com/)中，流覽至您的存放庫、選取 [ **設定] > 秘密 > 新增秘密**。

    ![螢幕擷取畫面顯示 [新增存放庫的秘密] 連結。](media/kubernetes-action/secrets.png)

2. 貼上上述命令的內容 `az cli` 作為 secret 變數的值。 例如： `AZURE_CREDENTIALS` 。

3. 同樣地，為容器登錄認證定義下列其他秘密，並在 Docker 登入動作中進行設定。 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. 一旦定義之後，您將會看到如下所示的秘密。

    ![螢幕擷取畫面：顯示存放庫的現有秘密。](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>建立容器映射並部署至 Azure Kubernetes Service 叢集

容器映射的組建和推送是使用動作來完成 `Azure/docker-login@v1` 。 若要將容器映射部署至 AKS，您必須使用該 `Azure/k8s-deploy@v1` 動作。 此動作有五個參數：

| **參數**  | **說明**  |
|---------|---------|
| **命名 空間** |  (選擇性) 選擇目標 Kubernetes 命名空間。 如果未提供命名空間，則命令會在預設命名空間中執行。 | 
| **體現** |   (需要的資訊清單檔案) 路徑，將用於部署 |
| **images** |  (選擇性) 映射 (s 的完整資源 URL，以在資訊清單檔案中用來進行替代)  |
| **imagepullsecrets** |  (選擇性) 已在叢集中設定的 docker 登錄秘密名稱。 針對輸入資訊清單檔案中的工作負載，在 imagePullSecrets 欄位下新增每個秘密名稱 |
| **kubectl-版本** |  (選擇性的) 安裝特定版本的 kubectl 二進位檔 |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>部署到 Azure Kubernetes Service 叢集

建立容器映射及部署至 Azure Kubernetes Service 叢集的端對端工作流程。

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

您可以在 GitHub 上的不同存放庫中找到一組動作，每個都包含檔和範例，以協助您使用 GitHub 進行 CI/CD，並將您的應用程式部署到 Azure。

- [設定-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-coNtext](https://github.com/Azure/k8s-set-context)

- [aks-set-coNtext](https://github.com/Azure/aks-set-context)

- [k8s-建立-秘密](https://github.com/Azure/k8s-create-secret)

- [k8s-部署](https://github.com/Azure/k8s-deploy)

- [webapps-容器-部署](https://github.com/Azure/webapps-container-deploy)

- [動作-工作流程-範例](https://github.com/Azure/actions-workflow-samples)
