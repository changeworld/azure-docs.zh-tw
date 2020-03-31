---
title: 將 AAD 標識與 Web 服務一起使用
titleSuffix: Azure Machine Learning
description: 在 Azure Kubernetes 服務中的 Web 服務中使用 AAD 標識，在評分期間訪問雲資源。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122840"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>在 Azure 庫伯奈斯服務中使用 Azure AD 標識與機器學習 Web 服務

在此方法中，您將瞭解如何在 Azure Kubernetes 服務中為已部署的機器學習模型分配 Azure 活動目錄 （AAD） 標識。 [AAD Pod 標識](https://github.com/Azure/aad-pod-identity)專案允許應用程式使用[託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)和庫伯奈斯基元安全地使用 AAD 訪問雲資源。 這允許 Web 服務安全地訪問 Azure 資源，而無需直接在`score.py`腳本中嵌入憑據或管理權杖。 本文介紹在 Azure Kubernetes 服務群集中創建和安裝 Azure 標識並將標識分配給已部署的 Web 服務的步驟。

## <a name="prerequisites"></a>Prerequisites

- [機器學習服務的 Azure CLI 擴展](reference-azure-machine-learning-cli.md)、用於[Python 的 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)或 Azure[機器學習視覺化工作室代碼擴展](tutorial-setup-vscode-extension.md)。

- 使用`kubectl`命令訪問 AKS 群集。 有關詳細資訊，請參閱[連接到群集](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- 部署到 AKS 群集的 Azure 機器學習 Web 服務。

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>在 AKS 群集中創建和安裝 Azure 標識

1. 要確定 AKS 群集是否啟用了 RBAC，請使用以下命令：

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    此命令返回`true`是否啟用了 RBAC 的值。 此值確定在下一步中要使用的命令。

1. 要在 AKS 群集中安裝[AAD Pod 標識](https://github.com/Azure/aad-pod-identity#getting-started)，請使用以下命令之一：

    * 如果您的 AKS 群集**啟用了 RBAC，** 請使用以下命令：
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * 如果您的 AKS 群集**未啟用 RBAC，** 請使用以下命令：
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        該命令的輸出類似于以下文本：

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [按照](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity)AAD Pod 標識專案頁中顯示的步驟創建 Azure 標識。

1. 按照 AAD Pod 標識專案頁中顯示的步驟[安裝 Azure 標識](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity)。

1. 按照 AAD Pod 標識專案頁中顯示的步驟[安裝 Azure 標識綁定](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)。

1. 如果上一步中創建的 Azure 標識與 AKS 群集不在同一資源組中，請按照 AAD Pod 標識專案頁中顯示的步驟操作["為 MIC 設置許可權](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic)"。

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>將 Azure 標識分配給機器學習 Web 服務

以下步驟使用上一節中創建的 Azure 標識，並通過**選擇器標籤**將其分配給 AKS Web 服務。

首先，在要分配 Azure 標識的 AKS 群集中標識部署的名稱和命名空間。 您可以通過運行以下命令來獲取此資訊。 命名空間應為 Azure 機器學習工作區名稱，並且部署名稱應為終結點名稱，如門戶所示。

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

通過編輯部署規範，將 Azure 標識選擇器標籤添加到部署中。選擇器值應該是在[安裝 Azure 標識綁定](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)的步驟 5 中定義的值。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

編輯部署以添加 Azure 標識選擇器標籤。 轉到 下一節。 `/spec/template/metadata/labels` 您應該看到值，如`isazuremlapp: “true”`。 添加 aad-pod 標識標籤，如下所示。

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
      - aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

要驗證標籤是否正確添加，請運行以下命令。

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

要查看所有 Pod 狀態，運行以下命令。

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

啟動並運行窗格後，此部署的 Web 服務現在將能夠通過 Azure 標識訪問 Azure 資源，而無需將憑據嵌入到代碼中。 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>將適當的角色指派給 Azure 標識

[使用適當的角色指派 Azure 託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)以訪問其他 Azure 資源。 確保您分配的角色具有正確的**資料操作**。 例如，存儲[Blob 資料讀取器角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)將具有對存儲 Blob 的讀取權限，而通用[讀取器角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)可能沒有。

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>將 Azure 標識與機器學習 Web 服務一起使用

將模型部署到 AKS 群集。 該`score.py`腳本可以包含指向 Azure 標識有權訪問的 Azure 資源的操作。 確保已為嘗試訪問的資源安裝了所需的用戶端庫依賴項。 下面是如何使用 Azure 標識從服務訪問不同的 Azure 資源的幾個示例。

### <a name="access-key-vault-from-your-web-service"></a>從 Web 服務訪問金鑰保存庫

如果已授予 Azure 標識對**金鑰保存庫**內機密的讀取存取許可權，則可以使用`score.py`以下代碼訪問它。

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name) 
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

### <a name="access-blob-from-your-web-service"></a>從 Web 服務訪問 Blob

如果已授予 Azure 標識對**存儲 Blob**中資料的讀取存取許可權，`score.py`則可以使用以下代碼訪問它。

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>後續步驟

* 有關如何使用 Python Azure 標識用戶端庫的詳細資訊，請參閱 GitHub 上的[存儲庫](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python)。
* 有關將模型部署到 Azure 庫伯奈斯服務群集的詳細資訊，請參閱[方法](how-to-deploy-azure-kubernetes-service.md)。