---
title: Avere vFXT 非擁有者因應措施 - Azure
description: 可讓沒有訂用帳戶擁有者權限的使用者部署 Avere vFXT for Azure 的因應措施
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 0d9b1060ee35af6cbc2e1b95b0f7813072c52d2e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85505370"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>授權非擁有者部署 Avere vFXT

這些指示是可讓沒有訂用帳戶擁有者權限的使用者建立 Avere vFXT for Azure 系統的因應措施。

(部署 Avere vFXT 系統的建議方式是讓具有擁有者權限的使用者執行建立步驟，如[準備建立 Avere vFXT](avere-vfxt-prereqs.md)中所述。)  

此因應措施牽涉到建立額外的存取角色，讓其使用者有足夠的權限可安裝叢集。 該角色必須由訂用帳戶擁有者建立，而且擁有者必須將它指派給適當的使用者。

訂用帳戶擁有者也必須為 Avere vFXT Marketplace [接受使用規定](avere-vfxt-prereqs.md)。

> [!IMPORTANT]
> 這些步驟全都必須由具有訂用帳戶擁有者權限的使用者 (將用於叢集) 執行。

1. 複製這些行並將它們儲存在檔案中 (例如 `averecreatecluster.json`)。 在 `AssignableScopes` 陳述式中使用您的訂用帳戶識別碼。

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",

           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. 執行此命令以建立角色：

   `az role definition create --role-definition <PATH_TO_FILE>`

    範例：

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. 將此角色指派給將會建立叢集的使用者：

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

完成此程式之後，此角色會為任何使用者指派訂用帳戶的下列許可權：

* 建立並設定網路基礎結構
* 建立叢集控制器
* 從叢集控制器執行叢集建立指令碼以建立叢集
