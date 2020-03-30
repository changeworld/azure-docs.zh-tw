---
title: 管理工作區中的角色
titleSuffix: Azure Machine Learning
description: 瞭解如何使用基於角色的存取控制 （RBAC） 訪問 Azure 機器學習工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270091"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>管理對 Azure 機器學習工作區的訪問
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何管理對 Azure 機器學習工作區的訪問。 [基於角色的存取控制 （RBAC）](/azure/role-based-access-control/overview)用於管理對 Azure 資源的訪問。 Azure 活動目錄中的使用者被分配特定角色，這些角色授予對資源的許可權。 Azure 提供內置角色和創建自訂角色的能力。

## <a name="default-roles"></a>預設角色

Azure 機器學習工作區是 Azure 資源。 與其他 Azure 資源一樣，創建新的 Azure 機器學習工作區時，它附帶三個預設角色。 您可以將使用者添加到工作區，並將他們分配給這些內置角色之一。

| 角色 | 存取層級 |
| --- | --- |
| **閱讀程式** | 工作區中的唯讀操作。 讀者可以在工作區中列出和查看資產，但不能創建或更新這些資產。 |
| **參與者** | 在工作區中查看、創建、編輯或刪除（如果適用）資產。 例如，參與者可以創建實驗、創建或附加計算群集、提交運行和部署 Web 服務。 |
| **擁有者** | 對工作區的完全存取權限，包括查看、創建、編輯或刪除工作區中（如果適用）資產的能力。 此外，您還可以更改角色指派。 |

> [!IMPORTANT]
> 角色訪問可以限定到 Azure 中的多個級別。 例如，具有工作區擁有者存取權限的人員可能無法擁有者訪問包含工作區的資源組。 有關詳細資訊，請參閱[RBAC 的工作原理](/azure/role-based-access-control/overview#how-rbac-works)。

有關特定內置角色的詳細資訊，請參閱[Azure 的內置角色](/azure/role-based-access-control/built-in-roles)。

## <a name="manage-workspace-access"></a>管理工作區訪問

如果您是工作區的擁有者，則可以添加和刪除工作區的角色。 您還可以將角色指派給使用者。 使用以下連結瞭解如何管理訪問：
- [Azure 門戶 UI](/azure/role-based-access-control/role-assignments-portal)
- [電源外殼](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure 資源管理員範本](/azure/role-based-access-control/role-assignments-template)

如果已安裝[Azure 機器學習 CLI，](reference-azure-machine-learning-cli.md)也可以使用 CLI 命令向使用者分配角色。

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

此欄位`user`是工作區父訂閱所在的 Azure 活動目錄實例中現有使用者的電子郵件地址。 下面是如何使用此命令的示例：

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>建立自訂角色

如果內置角色不足，您可以創建自訂角色。 自訂角色可能具有該工作區中的讀取、寫入、刪除和計算資源許可權。 您可以在特定工作區級別、特定資源組級別或特定訂閱級別使角色可用。

> [!NOTE]
> 您必須是該級別的資源的擁有者才能在該資源中創建自訂角色。

要創建自訂角色，請首先構造角色定義 JSON 檔，該檔指定角色的許可權和範圍。 以下示例定義在特定工作區級別範圍範圍的名為"資料科學家"的自訂角色：

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

可以更改該`AssignableScopes`欄位以在訂閱級別、資源組級別或特定工作區級別設置此自訂角色的範圍。

此自訂角色可以執行工作區中除以下操作之外的所有操作：

- 無法創建或更新計算資源。
- 無法刪除計算資源。
- 無法添加、刪除或更改角色指派。
- 無法刪除工作區。

要部署此自訂角色，請使用以下 Azure CLI 命令：

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

部署後，此角色在指定的工作區中可用。 現在，您可以在 Azure 門戶中添加和分配此角色。 或者，您可以使用`az ml workspace share`CLI 命令將此角色指派給使用者：

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

有關自訂角色的詳細資訊，請參閱[Azure 資源的自訂角色](/azure/role-based-access-control/custom-roles)。

有關自訂角色可用的操作（操作）的詳細資訊，請參閱[資來源提供者操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)。


## <a name="frequently-asked-questions"></a>常見問題集


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>Q. 在 Azure 機器學習服務中執行各種操作所需的許可權是什麼？

下表是 Azure 機器學習活動的摘要，以及至少執行這些活動所需的許可權。 例如，如果可以使用工作區作用域（列 4）執行活動，則具有該許可權的所有較高作用域也將自動工作。 此表中的所有路徑都是 到`Microsoft.MachineLearningServices/`**的相對路徑**。

| 活動 | 訂閱級別範圍 | 資源組級作用域 | 工作區級作用域 |
|---|---|---|---|
| 建立新的工作區 | 不需要 | 擁有者或貢獻者 | 不適用（創建後成為擁有者或繼承更高作用域的角色） |
| 創建新的計算群集 | 不需要 | 不需要 | 擁有者、參與者或自訂角色，允許：`workspaces/computes/write` |
| 創建新的筆記本 VM | 不需要 | 擁有者或貢獻者 | 不可能 |
| 創建新的計算實例 | 不需要 | 不需要 | 擁有者、參與者或自訂角色，允許：`workspaces/computes/write` |
| 資料平面活動，如提交運行、訪問資料、部署模型或發佈管道 | 不需要 | 不需要 | 擁有者、參與者或自訂角色，允許：`workspaces/*/write` <br/> 請注意，您還需要註冊到工作區的資料存儲，以便 MSI 訪問存儲帳戶中的資料。 |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Q. 如何列出訂閱中的所有自訂角色？

在 Azure CLI 中，運行以下命令。

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Q. 如何查找訂閱中角色的角色定義？

在 Azure CLI 中，運行以下命令。 請注意，`<role-name>`該格式應與上述命令返回的相同格式。

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Q. 如何更新角色定義？

在 Azure CLI 中，運行以下命令。

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

請注意，您需要對新角色定義的整個範圍具有許可權。 例如，如果此新角色具有跨三個訂閱的範圍，則需要對所有三個訂閱具有許可權。 

> [!NOTE]
> 角色更新可能需要 15 分鐘到 1 小時才能應用於該範圍內的所有角色指派。
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Q. 是否可以定義阻止更新工作區版本的角色？ 

可以，您可以定義一個阻止更新工作區版本的角色。 由於工作區更新是工作區物件的 PATCH 調用，因此通過在 JSON 定義中將以下操作放在`"NotActions"`陣列中來執行此操作： 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Q. 在工作區中執行配額操作需要哪些許可權？ 

您需要訂閱級別許可權才能在工作區中執行任何與配額相關的操作。 這意味著，只有在訂閱作用域具有寫入權限時，才能為託管計算資源設置訂閱級別配額或工作區級別配額。 


## <a name="next-steps"></a>後續步驟

- [企業安全性概觀](concept-enterprise-security.md)
- [在虛擬網路中安全地運行實驗和推理/評分](how-to-enable-virtual-network.md)
- [教學課程：訓練模型](tutorial-train-models-with-aml.md)
- [資來源提供者操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
