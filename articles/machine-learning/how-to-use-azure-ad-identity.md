---
title: 使用 AAD 身分識別搭配您的 web 服務
titleSuffix: Azure Machine Learning
description: 在 Azure Kubernetes Service 中使用 AAD 身分識別搭配您的 web 服務，以在評分期間存取雲端資源。
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 02/10/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 1789f83f048a2ab0fb75aa33635e58b0850b865b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319126"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>在 Azure Kubernetes Service 中搭配使用 Azure AD 身分識別與您的機器學習 Web 服務

在此操作說明中，您將瞭解如何在 Azure Kubernetes Service 中，將 Azure Active Directory (AAD) 身分識別指派給已部署的機器學習模型。 [Aad Pod 身分識別](https://github.com/Azure/aad-pod-identity)專案可讓應用程式使用[受控識別](../active-directory/managed-identities-azure-resources/overview.md)和 Kubernetes 基本專案，以安全地存取雲端資源。 這可讓您的 web 服務安全地存取您的 Azure 資源，而不需要內嵌認證或直接在腳本中管理權杖 `score.py` 。 本文說明在 Azure Kubernetes Service 叢集中建立和安裝 Azure 身分識別的步驟，並將身分識別指派給已部署的 web 服務。

## <a name="prerequisites"></a>必要條件

- [Machine Learning 服務的 Azure CLI 延伸](reference-azure-machine-learning-cli.md)模組、[適用于 PYTHON 的 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)，或[Azure Machine Learning Visual Studio Code 延伸](tutorial-setup-vscode-extension.md)模組。

- 使用命令存取 AKS 叢集 `kubectl` 。 如需詳細資訊，請參閱[連接到](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)叢集

- 部署至 AKS 叢集的 Azure Machine Learning web 服務。

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>在您的 AKS 叢集中建立和安裝 Azure 身分識別

1. 若要判斷您的 AKS 叢集是否已啟用 RBAC，請使用下列命令：

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    `true`如果啟用 RBAC，此命令會傳回的值。 此值會決定要在下一個步驟中使用的命令。

1. 若要在您的 AKS 叢集中安裝 [AAD Pod 身分識別](https://github.com/Azure/aad-pod-identity#getting-started) ，請使用下列其中一個命令：

    * 如果您的 AKS 叢集已 **啟用 RBAC** ，請使用下列命令：
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * 如果您的 AKS 叢集 **並未啟用 RBAC** ，請使用下列命令：
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        命令的輸出類似下列文字：

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. 遵循 AAD Pod 身分識別專案頁面中顯示的步驟，[建立 Azure 身分識別](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity)。

1. 遵循 AAD Pod 身分識別專案頁面中顯示的步驟，[安裝 Azure 身分識別](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity)。

1. 遵循 AAD Pod 身分識別專案頁面中顯示的步驟，[安裝 Azure 身分識別](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)系結。

1. 如果在上一個步驟中建立的 Azure 身分識別不在與您的 AKS 叢集相同的資源群組中，請遵循 AAD Pod 身分識別專案頁面中顯示的步驟，依照 [設定 MIC 的許可權](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) 。

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>將 Azure 身分識別指派給 machine learning web 服務

下列步驟使用在上一節中建立的 Azure 身分識別，並透過 **選取器標籤** 將其指派給您的 AKS web 服務。

首先，請在您想要指派 Azure 身分識別的 AKS 叢集中，識別您部署的名稱和命名空間。 您可以藉由執行下列命令來取得此資訊。 命名空間應該是您 Azure Machine Learning 工作區名稱，而您的部署名稱應該是您的端點名稱，如入口網站中所示。

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

藉由編輯部署規格，將 Azure 身分識別選取器標籤新增至您的部署。選取器值應該是您在 [安裝 Azure 身分識別](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)系結的步驟5中所定義的值。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

編輯部署以新增 Azure 身分識別選取器標籤。 移至下一節 `/spec/template/metadata/labels` 。 您應該會看到像這樣的值 `isazuremlapp: “true”` 。 新增 aad-pod-身分識別標籤，如下所示。

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

若要確認已正確新增標籤，請執行下列命令。

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

若要查看所有 pod 狀態，請執行下列命令。

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

當 pod 啟動並執行時，此部署的 web 服務現在將能夠透過您的 Azure 身分識別存取 Azure 資源，而不需要在您的程式碼中內嵌認證。 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>將適當的角色指派給您的 Azure 身分識別

[使用適當的角色指派 Azure 受控識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) ，以存取其他 Azure 資源。 確定您指派的角色具有正確的 **資料動作** 。 例如， [儲存體 Blob 資料讀取器角色](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 將擁有儲存體 blob 的讀取權限，而一般讀取者 [角色](../role-based-access-control/built-in-roles.md#reader) 可能不會。

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>使用 Azure 身分識別搭配您的 machine learning web 服務

將模型部署到您的 AKS 叢集。 `score.py`腳本可以包含指向 azure 身分識別可存取之 azure 資源的作業。 確定您已針對您嘗試存取的資源，安裝必要的用戶端程式庫相依性。 以下是一些範例，說明如何使用 Azure 身分識別從您的服務存取不同的 Azure 資源。

### <a name="access-key-vault-from-your-web-service"></a>從您的 web 服務存取 Key Vault

如果您已將 Azure 身分識別的讀取權限提供給 **Key Vault** 內的秘密，您 `score.py` 可以使用下列程式碼來存取它。

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

> [!IMPORTANT]
> 此範例會使用 DefaultAzureCredential。 若要使用特定的存取原則來授與您的身分識別存取權，請參閱 [使用 Azure CLI 指派 Key Vault 存取原則](../key-vault/general/assign-access-policy-cli.md)。

### <a name="access-blob-from-your-web-service"></a>從您的 web 服務存取 Blob

如果您已將 Azure 身分識別讀取權限提供給 **儲存體 Blob** 內的資料，您 `score.py` 可以使用下列程式碼來存取它。

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

* 如需有關如何使用 Python Azure 身分識別用戶端程式庫的詳細資訊，請參閱 GitHub 上的存放 [庫](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) 。
* 如需將模型部署到 Azure Kubernetes Service 叢集的詳細指南，請參閱操作 [說明](how-to-deploy-azure-kubernetes-service.md)。