---
title: 管理工作區中的角色
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure RBAC)  (的 Azure 角色型存取控制來存取 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 11/09/2020
ms.custom: how-to, seodec18, devx-track-azurecli, contperfq2
ms.openlocfilehash: 90abd46e73ecb50b5e6de40218571d0ec899752e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95012970"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>管理對 Azure Machine Learning 工作區的存取

在本文中，您將瞭解如何管理 (授權) 到 Azure Machine Learning 工作區的存取權。 [Azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/overview.md) 用來管理對 azure 資源的存取，例如建立新資源或使用現有資源的能力。 您 Azure Active Directory (Azure AD) 中的使用者會被指派特定角色，以授與資源的存取權。 Azure 提供內建角色和建立自訂角色的能力。

> [!TIP]
> 雖然本文著重于 Azure Machine Learning，但 Azure ML 依賴的個別服務提供自己的 RBAC 設定。 例如，使用本文中的資訊，您可以設定誰可以提交評分要求至部署為 Azure Kubernetes Service 上 web 服務的模型。 但 Azure Kubernetes Service 提供自己的一組 Azure 角色。 針對 Azure Machine Learning 可能有用的服務特定 RBAC 資訊，請參閱下列連結：
>
> * [控制對 Azure Kubernetes 叢集資源的存取](../aks/azure-ad-rbac.md)
> * [使用 Azure RBAC 進行 Kubernetes 授權](../aks/manage-azure-rbac.md)
> * [使用 Azure RBAC 來存取 blob 資料](/azure/storage/common/storage-auth-aad-rbac-portal)

> [!WARNING]
> 套用某些角色可能會限制其他使用者的 Azure Machine Learning studio 中的 UI 功能。 例如，如果使用者的角色沒有建立計算實例的能力，則在 studio 中將無法使用建立計算實例的選項。 這是預期的行為，並可防止使用者嘗試會傳回拒絕存取錯誤的作業。

## <a name="default-roles"></a>預設角色

Azure Machine Learning 工作區是一項 Azure 資源。 就像其他 Azure 資源一樣，新的 Azure Machine Learning 工作區建立時，會隨附三個預設角色。 您可以將使用者新增至工作區，並將其指派給其中一個內建角色。

| 角色 | 存取層級 |
| --- | --- |
| **讀取者** | 工作區中的唯讀動作。 讀者可以列出和查看工作區中的資產，包括 [資料](how-to-access-data.md) 存放區認證。 讀者無法建立或更新這些資產。 |
| **參與者** | 查看、建立、編輯或刪除工作區中適用) 資產 (。 例如，參與者可以建立實驗、建立或連結計算叢集、提交執行，以及部署 Web 服務。 |
| **擁有者** | 工作區的完整存取權，包括在工作區中適用) 資產的查看、建立、編輯或刪除 (功能。 此外，您可以變更角色指派。 |
| **自訂角色** | 可讓您自訂工作區中特定控制項或資料平面作業的存取權。 例如，提交執行、建立計算、部署模型或註冊資料集。 |

> [!IMPORTANT]
> 角色存取範圍可以設定為 Azure 中的多個層級。 例如，具有工作區存取權的人員可能無法擁有包含該工作區之資源群組的擁有者存取權。 如需詳細資訊，請參閱 [AZURE RBAC 的運作方式](../role-based-access-control/overview.md#how-azure-rbac-works)。

目前沒有 Azure Machine Learning 特有的其他內建角色。 如需內建角色的詳細資訊，請參閱 [Azure 內建角色](../role-based-access-control/built-in-roles.md)。

## <a name="manage-workspace-access"></a>管理工作區存取

如果您是工作區的擁有者，您可以新增和移除工作區的角色。 您也可以將角色指派給使用者。 使用下列連結來探索如何管理存取：
- [Azure 入口網站 UI](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI](../role-based-access-control/role-assignments-cli.md)
- [REST API](../role-based-access-control/role-assignments-rest.md)
- [Azure 資源管理員範本](../role-based-access-control/role-assignments-template.md)

如果您已安裝 [AZURE MACHINE LEARNING cli](reference-azure-machine-learning-cli.md)，您可以使用 cli 命令將角色指派給使用者：

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

此 `user` 欄位是工作區父訂用帳戶所在之 Azure Active Directory 實例中現有使用者的電子郵件地址。 以下是如何使用此命令的範例：

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> Azure Active Directory B2B 的同盟帳戶無法使用 [az ml workspace share] 命令。 請使用 Azure UI 入口網站而非命令。

## <a name="create-custom-role"></a>建立自訂角色

如果內建角色不足，您可以建立自訂角色。 自訂角色可能具有該工作區中的讀取、寫入、刪除和計算資源許可權。 您可以讓角色可在特定工作區層級、特定資源群組層級或特定訂用帳戶層級上使用。

> [!NOTE]
> 您必須是該層級資源的擁有者，才能在該資源內建立自訂角色。

若要建立自訂角色，請先建立角色定義 JSON 檔案，以指定角色的許可權和範圍。 下列範例會定義名為「資料科學家自訂」的自訂角色（範圍在特定工作區層級）：

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> 您可以變更 `AssignableScopes` 欄位，以在訂用帳戶層級、資源群組層級或特定工作區層級設定此自訂角色的範圍。
> 上述的自訂角色只是一個範例，請參閱 [Azure Machine Learning 服務的一些建議的自訂角色](#customroles)。

此自訂角色可以執行工作區中的所有專案，但不包括下列動作：

- 它無法建立或更新計算資源。
- 無法刪除計算資源。
- 它無法新增、刪除或改變角色指派。
- 無法刪除工作區。

若要部署此自訂角色，請使用下列 Azure CLI 命令：

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

部署之後，此角色會在指定的工作區中變成可用。 現在您可以在 Azure 入口網站中新增並指派此角色。 或者，您可以使用 CLI 命令將此角色指派給使用者 `az ml workspace share` ：

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

如需自訂角色的詳細資訊，請參閱 [Azure 自訂角色](../role-based-access-control/custom-roles.md)。 

### <a name="azure-machine-learning-operations"></a>Azure Machine Learning 作業

如需作業的詳細資訊 (動作，而不是) 可與自訂角色搭配使用的動作，請參閱 [資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)。 您也可以使用下列 Azure CLI 命令來列出作業：

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>列出自訂角色

在 Azure CLI 中，執行下列命令：

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

若要查看特定自訂角色的角色定義，請使用下列 Azure CLI 命令。 的 `<role-name>` 格式應與上述命令所傳回的格式相同：

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

## <a name="update-a-custom-role"></a>更新自訂角色

在 Azure CLI 中，執行下列命令：

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

您必須擁有新角色定義的整個範圍的許可權。 例如，如果這個新的角色在三個訂用帳戶中有一個範圍，您就必須擁有所有三個訂用帳戶的許可權。 

> [!NOTE]
> 角色更新可能需要15分鐘到一小時的時間，才能套用到該範圍內的所有角色指派。

## <a name="common-scenarios"></a>常見的案例

下表是 Azure Machine Learning 活動的摘要，以及在最少的範圍執行這些活動所需的許可權。 例如，如果可以使用工作區範圍執行活動 (資料行 4) ，則具有該許可權的所有較高範圍也會自動運作：

> [!IMPORTANT]
> 此資料表中開頭為的所有路徑 `/` 都是 **相對路徑** `Microsoft.MachineLearningServices/` ：

| 活動 | 訂用帳戶層級範圍 | 資源群組層級範圍 | 工作區層級範圍 |
| ----- | ----- | ----- | ----- |
| 建立新的工作區 | 不需要 | 擁有者或參與者 | N/A (成為擁有者，或在建立後繼承較高範圍的角色)  |
| 要求訂用帳戶層級 Amlcompute 配額或設定工作區層級配額 | 擁有者、參與者或自訂角色 </br>允許 `/locations/updateQuotas/action`</br> 在訂用帳戶範圍 | 未授權 | 未授權 |
| 建立新的計算叢集 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許： `/workspaces/computes/write` |
| 建立新的計算實例 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許： `/workspaces/computes/write` |
| 提交任何類型的執行 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許： `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| 發佈管線端點 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許： `"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| 在 AKS/ACI 資源上部署已註冊的模型 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許： `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| 針對已部署的 AKS 端點評分 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許： `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` 當您在使用權杖驗證時，不使用 Azure Active Directory auth) 或 `"/workspaces/read"` (時 ()  |
| 使用互動式筆記本存取儲存體 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許： `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| 建立新的自訂角色 | 擁有者、參與者或自訂角色允許 `Microsoft.Authorization/roleDefinitions/write` | 不需要 | 擁有者、參與者或自訂角色允許： `/workspaces/computes/write` |

> [!TIP]
> 如果您在第一次嘗試建立工作區時遇到失敗，請確定您的角色允許 `Microsoft.MachineLearningServices/register/action` 。 此動作可讓您向 Azure 訂用帳戶註冊 Azure Machine Learning 資源提供者。

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>使用者指派的受控識別與 Azure ML compute cluster

若要將使用者指派的身分識別指派給 Azure Machine Learning 計算叢集，您需要有寫入權限，才能建立計算和 [受控識別操作員角色](../role-based-access-control/built-in-roles.md#managed-identity-operator)。 如需 Azure RBAC 與受控識別的詳細資訊，請參閱[如何管理使用者指派](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)的身分識別

### <a name="mlflow-operations"></a>MLflow 作業

若要使用您的 Azure Machine Learning 工作區執行 MLflow 作業，請使用下列範圍的自訂角色：

| MLflow 操作 | 影響範圍 |
| --- | --- |
| 列出工作區追蹤存放區中的所有實驗、依識別碼取得實驗、依名稱取得實驗 | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| 使用名稱建立實驗、在實驗上設定標記、還原標記為刪除的實驗| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| 刪除實驗 | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| 取得執行和相關的資料和中繼資料、取得指定執行之指定度量的所有值清單、執行的清單構件 | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| 在實驗中建立新的回合、刪除執行、還原已刪除的回合、在目前的執行下設定標籤、在執行時設定標籤、在執行時刪除標記、在執行時刪除標記、記錄參數 (索引鍵/值組) 用於執行、記錄一批的計量、參數和標記以執行、更新執行狀態 | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| 依名稱取得註冊的模型、提取登錄中所有已註冊模型的清單、搜尋已註冊的模型、每個要求階段的最新版本模型、取得已註冊模型的版本、搜尋模型版本、取得模型版本成品儲存所在的 URI、搜尋由實驗識別碼執行的專案 | `Microsoft.MachineLearningServices/workspaces/models/read` |
| 建立新的已註冊模型、更新已註冊模型的名稱/描述、重新命名現有的已註冊模型、建立模型的新版本、更新模型版本的描述、將已註冊的模型轉換成其中一個階段 | `Microsoft.MachineLearningServices/workspaces/models/write` |
| 刪除已註冊的模型及其所有版本，並刪除已註冊模型的特定版本 | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>自訂角色範例

### <a name="data-scientist"></a>資料科學家

允許資料科學家執行工作區內的所有作業， **但** 下列情況除外：

* 建立計算
* 將模型部署到生產 AKS 叢集
* 在生產環境中部署管線端點

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/*/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="data-scientist-restricted"></a>資料科學家受限

在允許的動作中，不含萬用字元的更受限制角色定義。 它可以執行工作區內的所有作業， **除了**：

* 建立計算
* 將模型部署到生產 AKS 叢集
* 在生產環境中部署管線端點

`data_scientist_restricted_custom_role.json` :
```json
{
    "Name": "Data Scientist Restricted Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/computes/start/action",
        "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
        "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
        "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
        "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
        "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/modules/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/write",
        "Microsoft.MachineLearningServices/workspaces/datastores/delete"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```
     
### <a name="mlflow-data-scientist"></a>MLflow 資料科學家

允許資料科學家執行所有 MLflow AzureML 支援的作業， **但** 下列情況除外：

* 建立計算
* 將模型部署到生產 AKS 叢集
* 在生產環境中部署管線端點

`mlflow_data_scientist_custom_role.json` :
```json
{
    "Name": "MLFlow Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/experiments/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/delete",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/models/read",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/models/delete"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```   

### <a name="mlops"></a>MLOps

可讓您將角色指派給服務主體，並使用它來自動化您的 MLOps 管線。 例如，若要針對已發佈的管線提交執行：

`mlops_custom_role.json` :
```json
{
    "Name": "MLOps Custom",
    "IsCustom": true,
    "Description": "Can run pipelines against a published pipeline endpoint",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
        "Microsoft.MachineLearningServices/workspaces/environments/read",    
        "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
        "Microsoft.MachineLearningServices/workspaces/modules/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/read",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="workspace-admin"></a>工作區管理員

可讓您執行工作區範圍內的所有作業， **除了**：

* 新增新的工作區
* 指派訂用帳戶或工作區層級配額

工作區管理員也無法建立新的角色。 它只能指派工作區範圍內的現有內建或自訂角色：

`workspace_admin_custom_role.json` :
```json
{
    "Name": "Workspace Admin Custom",
    "IsCustom": true,
    "Description": "Can perform all operations except quota management and upgrades",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/write",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.Authorization/roleAssignments/*"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

<a name="labeler"></a>
### <a name="data-labeler"></a>資料標籤人員

可讓您定義範圍僅限於標記資料的角色：

`labeler_custom_role.json` :
```json
{
    "Name": "Labeler Custom",
    "IsCustom": true,
    "Description": "Can label data for Labeling",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

## <a name="troubleshooting"></a>疑難排解

當您使用 Azure 角色型存取控制 (Azure RBAC) 時，請注意以下幾點：

- 當您在 Azure 中建立資源（例如工作區）時，您不會直接成為資源的擁有者。 您的角色繼承自您在該訂用帳戶中授權的最高範圍角色。 舉例來說，如果您是網路系統管理員，並且擁有建立 Machine Learning 工作區的許可權，系統就會將該工作區的網路管理員角色指派給您，而非擁有者角色。

- 若要在工作區中執行配額作業，您需要訂用帳戶層級許可權。 這表示只有在訂用帳戶範圍有寫入權限時，才會針對您的受控計算資源設定訂用帳戶層級配額或工作區層級配額。

- 如果有兩個角色指派給相同的 Azure Active Directory 使用者，且動作/NotActions 有衝突的區段，則您在 NotActions 中所列的作業如果也會在另一個角色中列為動作，則可能不會生效。 若要深入瞭解 Azure 如何剖析角色指派，請閱讀 [AZURE RBAC 如何判斷使用者是否有權存取資源](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)

- 若要在 VNet 內部署您的計算資源，您需要明確具有下列動作的許可權：
    - `Microsoft.Network/virtualNetworks/join/action` 在 VNet 資源上。
    - `Microsoft.Network/virtualNetworks/subnet/join/action` 在子網資源上。
    
    如需有關具有網路功能的 Azure RBAC 的詳細資訊，請參閱 [網路內建角色](../role-based-access-control/built-in-roles.md#networking)。

- 有時可能需要1小時的時間，新的角色指派才會對整個堆疊的快取許可權生效。

## <a name="next-steps"></a>下一步

- [企業安全性概觀](concept-enterprise-security.md)
- [虛擬網路隔離和隱私權總覽](how-to-network-security-overview.md)
- [教學課程：訓練模型](tutorial-train-models-with-aml.md)
- [資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)