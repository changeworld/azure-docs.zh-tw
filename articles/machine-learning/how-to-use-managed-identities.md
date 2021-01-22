---
title: '使用受控識別進行存取控制 (預覽) '
titleSuffix: Azure Machine Learning
description: 瞭解如何使用受控識別，從 Azure Machine Learning 工作區控制對 Azure 資源的存取。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: b0b0c43039648737b229edc79dd4e0a3dc45f38e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683335"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>使用受控識別搭配 Azure Machine Learning (preview) 

[受控](../active-directory/managed-identities-azure-resources/overview.md) 識別可讓您使用 *存取資源所需的最低許可權* 來設定您的工作區。 

以值得信任的方式設定 Azure Machine Learning 工作區時，請務必確定與工作區相關聯的不同服務具有正確的存取層級。 例如，在機器學習工作流程期間，工作區需要存取 Azure Container Registry (適用于 Docker 映射的 ACR) ，以及用於定型資料的儲存體帳戶。 

此外，受控識別可讓您更精細地控制許可權，例如，您可以將特定計算資源的存取權授與或撤銷特定 ACR。

在本文中，您將瞭解如何使用受控識別來進行下列動作：

 * 針對您的 Azure Machine Learning 工作區設定和使用 ACR，而不需要讓系統管理員使用者存取 ACR。
 * 存取工作區外部的私用 ACR，以提取用於定型或推斷的基底映射。

> [!IMPORTANT]
> 使用受控識別來控制具有 Azure Machine Learning 之資源的存取權目前為預覽狀態。 預覽功能是以「原樣」提供，不保證支援或服務等級協定。 如需詳細資訊，請參閱 [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="prerequisites"></a>Prerequisites

- Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。
- [Machine Learning 服務的 Azure CLI 擴充](reference-azure-machine-learning-cli.md)功能
- [Azure Machine Learning PYTHON SDK](/python/api/overview/azure/ml/intro?view=azure-ml-py)。
- 若要指派角色，您的 Azure 訂用帳戶的登入必須具有 [受控識別操作員](../role-based-access-control/built-in-roles.md#managed-identity-operator) 角色，或授與必要動作的其他角色 (例如 __擁有__ 者) 。
- 您必須熟悉如何建立和使用 [受控](../active-directory/managed-identities-azure-resources/overview.md)識別。

## <a name="configure-managed-identities"></a>設定受控識別

在某些情況下，必須禁止系統管理員使用者存取 Azure Container Registry。 例如，您可以共用 ACR，而您必須禁止其他使用者存取系統管理員。 或者，訂用帳戶層級原則不允許以已啟用的系統管理員使用者建立 ACR。

> [!IMPORTANT]
> 使用 Azure Machine Learning 在 Azure 容器實例上進行推斷時 (ACI) ， __需要__ 在 ACR 上進行系統管理使用者存取。 如果您打算將模型部署到 ACI 進行推斷，請勿停用它。

當您建立 ACR 而不啟用系統管理使用者存取權時，會使用受控識別來存取 ACR，以建立和提取 Docker 映射。

當您建立工作區時，您可以將自己的 ACR 與系統管理員使用者停用。 或者，讓 Azure Machine Learning 建立工作區 ACR，並在之後停用系統管理員使用者。

### <a name="bring-your-own-acr"></a>攜帶您自己的 ACR

如果訂用帳戶原則不允許 ACR 管理使用者，您應該先建立不具系統管理員使用者的 ACR，然後將它與工作區產生關聯。 此外，如果您的現有 ACR 已停用系統管理員使用者，您可以將它附加至工作區。

[從 Azure CLI 建立 ACR](../container-registry/container-registry-get-started-azure-cli.md) 而不設定 ```--admin-enabled``` 引數，或從 Azure 入口網站建立，而不啟用系統管理使用者。 然後，在建立 Azure Machine Learning 工作區時，請指定 ACR 的 Azure 資源識別碼。 下列範例示範如何建立新的 Azure ML 工作區，以使用現有的 ACR：

> [!TIP]
> 若要取得參數的值 `--container-registry` ，請使用 [az acr show](/cli/azure/acr#az_acr_show) 命令來顯示 acr 的資訊。 `id`欄位包含您 ACR 的資源識別碼。

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>讓 Azure Machine Learning 服務建立工作區 ACR

如果您沒有攜帶自己的 ACR，Azure Machine Learning 服務會在您執行需要的作業時，為您建立一個。 例如，提交定型回合以 Machine Learning Compute、建立環境或部署 web 服務端點。 工作區所建立的 ACR 將會啟用系統管理使用者，而您需要手動停用系統管理員使用者。

1. 建立新的工作區

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. 執行需要 ACR 的動作。 例如， [訓練模型的教學課程](tutorial-train-models-with-aml.md)。

1. 取得叢集建立的 ACR 名稱：

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    此命令會傳回類似下列文字的值。 您只需要文字的最後部分，也就是 ACR 實例名稱：

    ```output
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. 更新 ACR 以停用系統管理使用者：

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>建立具有受控識別的計算，以存取 Docker 映射以進行定型

若要存取工作區 ACR，請使用已啟用系統指派的受控識別來建立 machine learning compute cluster。 您可以在建立計算時從 Azure 入口網站或 Studio 啟用身分識別，或從 Azure CLI 使用

# <a name="python"></a>[Python](#tab/python)

使用 [AmlComputeProvisioningConfiguration](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration?view=azure-ml-py)建立計算叢集時，請使用 `identity_type` 參數來設定受控識別類型。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

如需在 studio 中建立計算叢集時設定受控識別的詳細資訊，請參閱 [設定受控識別](how-to-create-attach-compute-studio.md#managed-identity)。

---

受控識別會自動授與工作區 ACR 的 ACRPull 角色，以啟用提取 Docker 映射以進行定型。

> [!NOTE]
> 如果您先建立計算，則在建立工作區 ACR 之前，您必須手動指派 ACRPull 角色。

## <a name="access-base-images-from-private-acr"></a>從私用 ACR 存取基底映射

根據預設，Azure Machine Learning 會使用來自 Microsoft 所管理之公用存放庫的 Docker 基底映射。 然後，它會在這些影像上建立定型或推斷環境。 如需詳細資訊，請參閱 [什麼是 ML 環境？](concept-environments.md)。

若要使用您企業內部的自訂基礎映射，您可以使用受控識別來存取您的私人 ACR。 有兩個使用案例：

 * 依原樣使用基底映射進行定型。
 * 以自訂映射作為基礎來建立 Azure Machine Learning 受控映射。

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>將 Docker 基礎映射提取至 machine learning compute 叢集，以進行定型

如先前所述，使用已啟用系統指派的受控識別來建立 machine learning compute cluster。 然後，判斷受控識別的主體識別碼。

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

（選擇性）您可以更新計算叢集以指派使用者指派的受控識別：

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

若要允許計算叢集提取基礎映射，請將私人 ACR 上的受控服務識別 ACRPull 角色授與

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

最後，提交定型回合時，請在 [環境定義](how-to-use-environments.md#use-existing-environments)中指定基底映射的位置。

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> 若要確保基底映射直接提取到計算資源，請設定 `user_managed_dependencies = True` 且不要指定 Dockerfile。 否則 Azure Machine Learning 服務會嘗試建立新的 Docker 映射並失敗，因為只有計算叢集有權從 ACR 提取基底映射。

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>從私用 ACR 將 Azure Machine Learning 受控環境建立為定型或推斷的基礎映射

在此案例中，Azure Machine Learning 服務會在您從私用 ACR 提供的基底映射之上建立定型或推斷環境。 由於「映射組建」工作會在工作空間 ACR 上使用 ACR 工作執行，因此您必須執行其他步驟，才能允許存取。

1. 建立 __使用者指派的受控識別__ ，並將 __私人 ACR__ 的存取權授與身分識別 ACRPull。  
1. 從上一個步驟中的 __使用者指派受控識別__，將受控識別操作員角色授與工作區 __系統指派的受控識別__。 此角色可讓工作區將使用者指派的受控識別指派給 ACR 工作，以建立受控環境。 

    1. 取得工作區系統指派的受控識別的主體識別碼：

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. 授與受控識別操作員角色：

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        UAI 資源識別碼是使用者指派身分識別的 Azure 資源識別碼，格式為 `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` 。

1. 使用 [Workspace.set_connection 方法](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-)，在工作區連接中指定 __使用者指派受控識別__ 的外部 ACR 和用戶端識別碼：

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

完成設定之後，您就可以在建立定型或推斷的環境時，從私用 ACR 使用基底映射。 下列程式碼片段示範如何在環境定義中指定基底映射 ACR 和映射名稱：

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

（選擇性）您可以使用 [RegistryIdentity](/python/api/azureml-core/azureml.core.container_registry.registryidentity?view=azure-ml-py)，在環境定義中指定受控識別資源 URL 和用戶端識別碼。 如果您明確使用登錄身分識別，它會覆寫先前指定的任何工作區連接：

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="use-docker-images-for-inference"></a>使用 Docker 映射進行推斷

當您設定 ACR 但未如先前所述的系統管理使用者之後，您可以從 Azure Kubernetes service 存取 Docker 映射以進行推斷，而不需要系統管理員金鑰， (AKS) 。 當您建立 AKS 或將其附加至工作區時，會自動將工作區 ACR 的 ACRPull 存取權指派給叢集的服務主體。

> [!NOTE]
> 如果您攜帶自己的 AKS 叢集，叢集必須啟用服務主體，而不是受控識別。

## <a name="next-steps"></a>下一步

* 深入瞭解 [Azure Machine Learning 中的企業安全性](concept-enterprise-security.md)。