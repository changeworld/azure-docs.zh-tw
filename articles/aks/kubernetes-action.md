---
title: 使用 GitHub Actions 建立、測試及部署容器至 Azure Kubernetes Service
description: 瞭解如何使用 GitHub Actions 將您的容器部署至 Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: a0f64b0d19dd3f65d883237e9ead2c9f1303adaf
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95794778"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>部署至 Kubernetes 服務的 GitHub Actions

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) 可讓您彈性地建置自動化軟體開發生命週期工作流程。 您可以使用多個 Kubernetes 動作，從 Azure Container Registry 部署至容器，以 GitHub Actions 的 Azure Kubernetes Service。 

## <a name="prerequisites"></a>必要條件 

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- GitHub 帳戶。 如果您沒有帳戶，請[免費](https://github.com/join)註冊。  
- 正常運作的 Kubernetes 叢集
    - [教學課程：準備 Azure Kubernetes Service 的應用程式](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>工作流程檔案概觀

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

您可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) 命令來建立[服務主體](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 您可以使用 Azure 入口網站中的 [Azure Cloud Shell](https://shell.azure.com/)，或選取 [試試看] 按鈕來執行此命令。

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

## <a name="configure-the-github-secrets"></a>設定 GitHub 祕密

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

容器映射的組建和推送是使用動作來完成 `Azure/docker-login@v1` 。 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>部署到 Azure Kubernetes Service 叢集

若要將容器映射部署至 AKS，您必須使用該 `Azure/k8s-deploy@v1` 動作。 此動作有五個參數：

| **參數**  | **說明**  |
|---------|---------|
| **命名 空間** |  (選擇性) 選擇目標 Kubernetes 命名空間。 如果未提供命名空間，則命令會在預設命名空間中執行。 | 
| **體現** |   (需要的資訊清單檔案) 路徑，將用於部署 |
| **images** |  (選擇性) 映射 (s 的完整資源 URL，以在資訊清單檔案中用來進行替代)  |
| **imagepullsecrets** |  (選擇性) 已在叢集中設定的 docker 登錄秘密名稱。 針對輸入資訊清單檔案中的工作負載，在 imagePullSecrets 欄位下新增每個秘密名稱 |
| **kubectl-版本** |  (選擇性的) 安裝特定版本的 kubectl 二進位檔 |


在您可以部署至 AKS 之前，您必須先設定目標 Kubernetes 命名空間，並建立映射提取秘密。 請參閱將 [映射從 Azure container Registry 提取至 Kubernetes](../container-registry/container-registry-auth-kubernetes.md)叢集，以深入瞭解提取映射的運作方式。 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      force: true
```


使用動作完成您的部署 `k8s-deploy` 。 將環境變數取代為您的應用程式的值。 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>清除資源

當您的 Kubernetes 叢集、容器登錄和儲存機制不再需要時，請刪除資源群組和 GitHub 存放庫，以清除您所部署的資源。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解 Azure Kubernetes Service](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>其他 Kubernetes GitHub Actions

* [Kubectl 工具安裝程式](https://github.com/Azure/setup-kubectl) (`azure/setup-kubectl`) ：在執行器上安裝特定版本的 Kubectl。
* [Kubernetes set coNtext](https://github.com/Azure/k8s-set-context) (`azure/k8s-set-context`) ：設定將供其他動作使用或執行任何 kubectl 命令的目標 Kubernetes 叢集內容。
* [AKS set coNtext](https://github.com/Azure/aks-set-context) (`azure/aks-set-context`) ：設定目標 Azure Kubernetes Service 叢集內容。
* [Kubernetes 建立秘密](https://github.com/Azure/k8s-create-secret) (`azure/k8s-create-secret`) ：在 Kubernetes 叢集中建立一般秘密或 docker 登錄秘密。
* [Kubernetes 部署](https://github.com/Azure/k8s-deploy) (`azure/k8s-deploy`) ：製作，並將資訊清單部署至 Kubernetes 叢集。
* [設定 Helm](https://github.com/Azure/setup-helm) (`azure/setup-helm`) ：在執行器上安裝特定版本的 Helm 二進位檔。
* [Kubernetes 製作](https://github.com/Azure/k8s-bake) (`azure/k8s-bake`) ：要用於使用 helm2、kustomize 或 kompose 的部署的資訊清單檔。
* [Kubernetes](https://github.com/azure/k8s-lint) 不起毛 `azure/k8s-lint` 的 () ：驗證/不起毛您的資訊清單檔案。
