---
title: 管理您工作區中的角色
titleSuffix: Azure Machine Learning
description: 瞭解如何使用角色型存取控制 (RBAC) 來存取 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 07/24/2020
ms.custom: how-to, seodec18
ms.openlocfilehash: 8c2203fec71430840e547115d24330b661bda50f
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002220"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>管理 Azure Machine Learning 工作區的存取權
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何管理 Azure Machine Learning 工作區的存取權。 Azure[角色型存取控制 (AZURE RBAC) ](/azure/role-based-access-control/overview)用來管理 azure 資源的存取權。 Azure Active Directory 中的使用者會被指派特定角色，以授與資源的存取權。 Azure 提供內建角色，以及建立自訂角色的能力。

## <a name="default-roles"></a>預設角色

Azure Machine Learning 工作區是一項 Azure 資源。 就像其他 Azure 資源一樣，新的 Azure Machine Learning 工作區建立時，會隨附三個預設角色。 您可以將使用者新增至工作區，並將它們指派給這些內建角色的其中一個。

| 角色 | 存取層級 |
| --- | --- |
| **讀取者** | 工作區中的唯讀動作。 讀者可以在工作區中列出及查看資產，包括[資料](how-to-access-data.md)存放區認證。 讀者無法建立或更新這些資產。 |
| **參與者** | 查看、建立、編輯或刪除 (，其中適用于工作區中的) 資產。 例如，參與者可以建立實驗、建立或連結計算叢集、提交執行，以及部署 Web 服務。 |
| **擁有者** | 工作區的完整存取權，包括能夠查看、建立、編輯或刪除工作區中適用) 資產的 (。 此外，您可以變更角色指派。 |
| **自訂角色** | 可讓您自訂工作區內特定控制或資料平面作業的存取權。 例如，提交執行、建立計算、部署模型或註冊資料集。 |

> [!IMPORTANT]
> 角色存取的範圍可以設定為 Azure 中的多個層級。 例如，具有工作區之擁有者存取權的人，可能不會擁有包含該工作區之資源群組的擁有者存取權。 如需詳細資訊，請參閱[RBAC 的運作方式](/azure/role-based-access-control/overview#how-rbac-works)。

如需特定內建角色的詳細資訊，請參閱[Azure 的內建角色](/azure/role-based-access-control/built-in-roles)。

## <a name="manage-workspace-access"></a>管理工作區存取

如果您是工作區的擁有者，您可以新增和移除工作區的角色。 您也可以將角色指派給使用者。 使用下列連結來探索如何管理存取權：
- [Azure 入口網站 UI](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure 資源管理員範本](/azure/role-based-access-control/role-assignments-template)

如果您已安裝[AZURE MACHINE LEARNING cli](reference-azure-machine-learning-cli.md)，您可以使用 cli 命令將角色指派給使用者：

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user`欄位是工作區父訂用帳戶所在 Azure Active Directory 實例中現有使用者的電子郵件地址。 以下是如何使用此命令的範例：

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> Azure Active Directory B2B 的同盟帳戶無法使用 [az ml workspace share] 命令。 請使用 Azure UI 入口網站，而不是命令。


## <a name="azure-machine-learning-operations"></a>Azure Machine Learning 作業

Azure Machine Learning 許多作業和工作的內建動作。 如需完整清單，請參閱[Azure 資源提供者作業](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)。

## <a name="create-custom-role"></a>建立自訂角色

如果內建角色不足，您可以建立自訂角色。 自訂角色可能具有該工作區中的讀取、寫入、刪除和計算資源許可權。 您可以在特定工作區層級、特定的資源群組層級或特定的訂用帳戶層級上，讓角色可供使用。

> [!NOTE]
> 您必須是該層級資源的擁有者，才能在該資源中建立自訂角色。

若要建立自訂角色，請先建立角色定義 JSON 檔案，以指定角色的許可權和範圍。 下列範例會定義名為「資料科學家自訂」的自訂角色，範圍限定在特定工作區層級：

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
> 您可以變更 `AssignableScopes` 欄位，在訂用帳戶層級、資源群組層級或特定工作區層級設定此自訂角色的範圍。
> 上述自訂角色只是一個範例，請參閱[Azure Machine Learning 服務的一些建議自訂角色](#customroles)。

這個自訂角色可以在工作區中執行所有專案，但下列動作除外：

- 它無法建立或更新計算資源。
- 它無法刪除計算資源。
- 它無法新增、刪除或改變角色指派。
- 無法刪除工作區。

若要部署這個自訂角色，請使用下列 Azure CLI 命令：

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

部署之後，此角色會在指定的工作區中變成可用。 現在您可以在 Azure 入口網站中新增和指派此角色。 或者，您可以使用 CLI 命令將此角色指派給使用者 `az ml workspace share` ：

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

如需自訂角色的詳細資訊，請參閱[Azure 自訂角色](/azure/role-based-access-control/custom-roles)。 如需有關作業的詳細資訊 (動作，而不是動作) 可與自訂角色搭配使用，請參閱[資源提供者作業](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)。

## <a name="frequently-asked-questions"></a>常見問題集


### <a name="q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service"></a>Q. 在 Azure Machine Learning 服務中執行一些常見案例所需的許可權為何？

下表是 Azure Machine Learning 活動的摘要，以及在最少範圍執行它們所需的許可權。 例如，如果您可以使用工作區範圍來執行活動 (資料行 4) ，則具有該許可權的所有更高範圍也會自動生效：

> [!IMPORTANT]
> 此資料表中開頭為的所有路徑 `/` 都是的**相對路徑** `Microsoft.MachineLearningServices/` ：

| 活動 | 訂用帳戶層級範圍 | 資源群組層級範圍 | 工作區層級範圍 |
| ----- | ----- | ----- | ----- |
| 建立新的工作區 | 不需要 | 擁有者或參與者 | 在建立後，N/A (成為擁有者，或繼承較高的範圍角色)  |
| 更新工作區的版本 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許：`/workspaces/write` |
| 要求訂用帳戶層級 Amlcompute 配額或設定工作區層級配額 | 擁有者、參與者或自訂角色 </br>便`/locations/updateQuotas/action`</br> 在訂用帳戶範圍 | 未獲授權 | 未獲授權 |
| 建立新的計算叢集 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許：`/workspaces/computes/write` |
| 建立新的計算實例 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許：`/workspaces/computes/write` |
| 正在提交任何類型的執行 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許：`"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| 發行管線端點 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許：`"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| 在 AKS/ACI 資源上部署已註冊的模型 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許：`"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| 針對已部署的 AKS 端點進行評分 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許： `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (當您未使用 AAD 驗證時) 或 `"/workspaces/read"` (當您使用權杖驗證時)  |
| 使用互動式筆記本存取儲存體 | 不需要 | 不需要 | 擁有者、參與者或自訂角色允許：`"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*"` |
| 建立新的自訂角色 | 允許的擁有者、參與者或自訂角色`Microsoft.Authorization/roleDefinitions/write` | 不需要 | 擁有者、參與者或自訂角色允許：`/workspaces/computes/write` |


### <a name="q-are-we-publishing-azure-built-in-roles-for-the-machine-learning-service"></a>Q. 我們是否發佈了適用于 Machine Learning 服務的 Azure 內建角色？

我們目前不會發佈適用于 Machine Learning 服務的[Azure 內建角色](/azure/role-based-access-control/built-in-roles)。 內建角色一旦發佈就無法更新，我們仍會根據客戶案例和意見反應來確認角色定義。 

<a id="customroles"></a>

### <a name="q-are-there-some-custom-role-templates-for-the-most-common-scenarios-in-machine-learning-service"></a>Q. Machine Learning 服務中最常見的案例是否有一些自訂角色範本？

是一些常見的案例，其中包含自訂的提議角色定義，可供您用來做為定義自訂角色的基礎：

* __資料科學家自訂__：允許資料科學家在工作區中執行所有作業，**除了**：

    * 建立計算
    * 將模型部署到生產環境 AKS 叢集
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

* __資料科學家受限制的自訂__：在允許的動作中，不含萬用字元的更受限制角色定義。 它可以在工作區中執行所有作業，**除了**：

    * 建立計算
    * 將模型部署到生產環境 AKS 叢集
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

* __MLOps 自訂__：可讓您將角色指派給服務主體，並使用它來自動化您的 MLOps 管線。 例如，針對已發行的管線提交執行：

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

* __工作區系統管理員__：可讓您執行工作區範圍內的所有作業，**但除外**：

    * 新增新的工作區
    * 指派訂用帳戶或工作區層級配額
    * 升級工作區的版本

    工作區管理員也無法建立新的角色。 它只能指派其工作區範圍內的現有內建或自訂角色：

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
* __Labeler Custom__：可讓您定義僅限於標記資料的角色：

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

### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Q. 如何? 列出我的訂用帳戶中的所有自訂角色嗎？

在 Azure CLI 中，執行下列命令。

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-operations-supported-by-the-machine-learning-service"></a>Q. 如何? 尋找 Machine Learning 服務支援的作業嗎？

在 Azure CLI 中，執行下列命令。

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

您也可以在[資源提供者作業](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)清單中找到它們。


### <a name="q-what-are-some-common-gotchas-when-using-azure-rbac"></a>Q. 使用 Azure RBAC 時有哪些常見的問題？

當您使用 Azure 角色型存取控制 (Azure RBAC) 時，以下是一些要注意的事項：

- 當您在 Azure 中建立資源（例如工作區）時，您不是直接擁有工作區的擁有者。 您的角色會繼承自您在該訂用帳戶中授權的最高範圍角色。 舉例來說，如果您是網路系統管理員，而且擁有建立 Machine Learning 工作區的許可權，則會針對該工作區指派網路管理員角色，而不是擁有者角色。
- 若有兩個角色指派給相同的 AAD 使用者，且動作/NotActions 有衝突的區段，則如果您在另一個角色中也將這些作業列為動作，則從某個角色 NotActions 列出的作業可能不會生效。 若要深入瞭解 Azure 如何剖析角色指派，請參閱[AZURE RBAC 如何判斷使用者是否有資源的存取權](/azure/role-based-access-control/overview#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)
- 若要在 VNet 內部署您的計算資源，您必須在該 VNet 資源上明確擁有「Microsoft 網路/virtualNetworks/聯結/動作」的許可權。
- 有時候，您的新角色指派可能需要最多1小時的時間，才會對整個堆疊的快取許可權生效。


### <a name="q-what-permissions-do-i-need-to-use-a-user-assigned-managed-identity-with-my-amlcompute-clusters"></a>Q. 我需要哪些許可權才能將使用者指派的受控識別與我的 Amlcompute 叢集搭配使用？

若要在 Amlcompute 叢集上指派使用者指派的身分識別，必須擁有寫入權限才能建立計算並具有[受控識別操作員角色](/azure/role-based-access-control/built-in-roles#managed-identity-operator)。 如需具有受控識別之 RBAC 的詳細資訊，請參閱[如何管理使用者指派](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)的身分識別


### <a name="q-do-we-support-role-based-access-control-on-the-studio-portal"></a>Q. 我們是否支援 Studio 入口網站上的角色型存取控制？

Azure Machine Learning Studio 支援 Azure RBAC)  (以角色為基礎的存取控制。 

> [!IMPORTANT]
> 一旦您將具有特定許可權的自訂角色指派給工作區中的資料科學家，使用者就會自動隱藏對應的動作 (例如新增計算按鈕) 。 隱藏這些專案可避免在使用時看到從服務傳回未經授權存取通知的控制項。

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Q. 如何? 在我的訂用帳戶中尋找角色的角色定義嗎？

在 Azure CLI 中，執行下列命令。 `<role-name>`應該使用上述命令所傳回的相同格式。

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Q. 如何? 更新角色定義？

在 Azure CLI 中，執行下列命令。

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

您必須擁有新角色定義之整個範圍的許可權。 例如，如果這個新角色具有三個訂用帳戶之間的範圍，您就必須擁有這三個訂用帳戶的許可權。 

> [!NOTE]
> 角色更新可能需要15分鐘到1小時的時間，才能套用到該範圍內的所有角色指派。
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Q. 我可以定義防止更新工作區版本的角色嗎？ 

是，您可以定義防止更新工作區版本的角色。 因為工作區更新是在工作區物件上進行的修補呼叫，所以您可以將下列動作放在 `"NotActions"` JSON 定義的陣列中： 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Q. 在工作區中執行配額作業需要哪些許可權？ 

您需要訂用帳戶層級許可權，才能在工作區中執行任何與配額相關的作業。 這表示只有在訂用帳戶範圍具有寫入權限時，才會為受控計算資源設定訂用帳戶層級配額或工作區層級配額。 


## <a name="next-steps"></a>後續步驟

- [企業安全性概觀](concept-enterprise-security.md)
- [安全地在虛擬網路中執行實驗和推斷/分數](how-to-enable-virtual-network.md)
- [教學課程：訓練模型](tutorial-train-models-with-aml.md)
- [資源提供者作業](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
