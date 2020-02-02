---
title: 管理您工作區中的角色
titleSuffix: Azure Machine Learning
description: 瞭解如何使用角色型存取控制（RBAC）存取 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: aba613911328b1272ebb07eeae633932cb4a442f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935362"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>管理 Azure Machine Learning 工作區的存取權
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何管理 Azure Machine Learning 工作區的存取權。 [角色型存取控制（RBAC）](/azure/role-based-access-control/overview)是用來管理 Azure 資源的存取權。 Azure Active Directory 中的使用者會被指派特定角色，以授與資源的存取權。 Azure 提供內建角色，以及建立自訂角色的能力。

## <a name="default-roles"></a>預設角色

Azure Machine Learning 工作區是一種 Azure 資源。 就像其他 Azure 資源一樣，建立新的 Azure Machine Learning 工作區時，它會隨附三個預設角色。 您可以將使用者新增至工作區，並將它們指派給這些內建角色的其中一個。

| 角色 | 存取層級 |
| --- | --- |
| **讀取者** | 工作區中的唯讀動作。 讀者可以列出及查看工作區中的資產，但無法建立或更新這些資產。 |
| **參與者** | 在工作區中查看、建立、編輯或刪除（如果適用）資產。 例如，參與者可以建立實驗、建立或附加計算叢集、提交執行，以及部署 web 服務。 |
| **擁有者** | 工作區的完整存取權，包括能夠在工作區中查看、建立、編輯或刪除（如果適用）資產。 此外，您可以變更角色指派。 |

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

如果您已安裝[AZURE MACHINE LEARNING CLI](reference-azure-machine-learning-cli.md)，您也可以使用 cli 命令來指派角色給使用者。

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

[`user`] 欄位是工作區父訂用帳戶所在 Azure Active Directory 實例中現有使用者的電子郵件地址。 以下是如何使用此命令的範例：

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>建立自訂角色

如果內建角色不足，您可以建立自訂角色。 自訂角色可能具有該工作區中的讀取、寫入、刪除和計算資源許可權。 您可以在特定工作區層級、特定的資源群組層級或特定的訂用帳戶層級上，讓角色可供使用。

> [!NOTE]
> 您必須是該層級資源的擁有者，才能在該資源中建立自訂角色。

若要建立自訂角色，請先建立角色定義 JSON 檔案，以指定角色的許可權和範圍。 下列範例會定義名為「資料科學家」的自訂角色，範圍限定在特定工作區層級：

`data_scientist_role.json`：
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

您可以變更 [`AssignableScopes`] 欄位，在訂用帳戶層級、資源群組層級或特定工作區層級設定此自訂角色的範圍。

這個自訂角色可以在工作區中執行所有專案，但下列動作除外：

- 它無法建立或更新計算資源。
- 它無法刪除計算資源。
- 它無法新增、刪除或改變角色指派。
- 無法刪除工作區。

若要部署這個自訂角色，請使用下列 Azure CLI 命令：

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

部署之後，此角色會在指定的工作區中變成可用。 現在您可以在 Azure 入口網站中新增和指派此角色。 或者，您可以使用 `az ml workspace share` CLI 命令，將此角色指派給使用者：

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

如需自訂角色的詳細資訊，請參閱[適用于 Azure 資源的自訂角色](/azure/role-based-access-control/custom-roles)。

如需自訂角色可用之作業（動作）的詳細資訊，請參閱[資源提供者作業](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)。

## <a name="next-steps"></a>後續步驟

- [企業安全性總覽](concept-enterprise-security.md)
- [安全地在虛擬網路中執行實驗和推斷/分數](how-to-enable-virtual-network.md)
- [教學課程：訓練模型](tutorial-train-models-with-aml.md)
- [資源提供者作業](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)