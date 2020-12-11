---
title: Azure Event Grid 安全性與驗證
description: 說明 Azure Event Grid 與其概念。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 24954ce0a0dc54a04720c0d0b495d14e950a2f71
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109584"
---
# <a name="authorizing-access-to-event-grid-resources"></a>授權存取事件方格資源
Azure 事件方格可讓您控制為不同的使用者提供的存取層級，以進行各種 **管理作業** ，例如列出事件訂閱、建立新的訂用帳戶，以及產生金鑰。 事件方格會使用 Azure 角色型存取控制 (Azure RBAC) 。

> [!NOTE]
> EventGrid 不支援將事件發佈至事件方格主題或網域的 Azure RBAC。 使用共用存取簽章 (SAS) 金鑰或權杖來驗證發佈事件的用戶端。 如需詳細資訊，請參閱 [驗證發行用戶端](security-authenticate-publishing-clients.md)。 

## <a name="operation-types"></a>作業類型
如需 Azure 事件方格所支援的作業清單，請執行下列 Azure CLI 命令： 

```azurecli-interactive
az provider operation show --namespace Microsoft.EventGrid
```

下列作業會傳回可能的秘密資訊，此資訊會被篩選掉正常的讀取作業。 建議您限制這些作業的存取。 

* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action


## <a name="built-in-roles"></a>內建角色

事件格線能提供兩個內建角色以用來管理事件訂閱。 它們在執行 [事件網域](event-domains.md) 時很重要，因為它們提供使用者訂閱事件網域中的主題所需的許可權。 這些角色會將焦點放在事件訂閱上，而且不會針對建立主題之類的動作授與存取權。

您可以[將這些角色指派給使用者或群組](../role-based-access-control/quickstart-assign-role-user-portal.md)。

**EventGrid EventSubscription 參與者**：管理事件方格訂用帳戶作業

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription Reader**：讀取事件方格訂用帳戶

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="custom-roles"></a>自訂角色

如果您需要指定不同於內建角色的權限，則可以建立自訂角色。

以下是允許使用者採取不同動作的事件方格角色定義範例。 這些自訂角色與內建角色不同，因為它們所授與的存取權會比只有事件訂閱更廣泛。

**EventGridReadOnlyRole.json**：只允許唯讀作業。

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**：允許限制的張貼動作，但不允許刪除。

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**：允許所有 Grid 動作。

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

您可以搭配 [PowerShell](../role-based-access-control/custom-roles-powershell.md)、[Azure CLI](../role-based-access-control/custom-roles-cli.md) 和 [REST](../role-based-access-control/custom-roles-rest.md) 建立自訂角色。



### <a name="encryption-at-rest"></a>待用加密

由事件方格服務寫入磁片的所有事件或資料都會由 Microsoft 管理的金鑰加密，以確保其會在待用時加密。 此外，遵循[事件方格重試原則](delivery-and-retry.md)，事件或資料的保留時間上限為 24 小時。 事件方格會在 24 小時或事件存留時間 (以較短者為準) 之後自動刪除所有事件或資料。

## <a name="permissions-for-event-subscriptions"></a>事件訂閱的許可權
如果您使用的事件處理常式不是 WebHook (例如事件中樞或佇列儲存體)，您將需要該資源的寫入存取權。 此權限檢查可防止未經授權的使用者將事件傳送至您的資源。

您在作為事件來源的資源上必須擁有 **Microsoft.EventGrid/EventSubscriptions/Write** 權限。 因為您要在該資源的範圍下寫入新的訂用帳戶，所以需要授權。 依您訂閱的是系統主題或自訂主題而定，所需資源會有所不同。 本節會說明這兩種類型。

### <a name="system-topics-azure-service-publishers"></a>系統主題 (Azure 服務發行者)
若您訂閱的是系統主題，您需要取得在資源發佈範圍下寫入新事件訂閱的權限。 資源的格式為：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

舉例來說，若要訂閱名為 **myacct** 之儲存體帳戶上的事件，您需要 Microsoft.EventGrid/EventSubscriptions/Write 授予您此權限：`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>自訂主題
若是自訂主題，您需要取得在 Event Grid 主題範圍下寫入新事件訂用帳戶的權限。 資源的格式為：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

舉例來說，若要訂閱名為 **mytopic** 之自訂主題，您需要 Microsoft.EventGrid/EventSubscriptions/Write 授予您此權限：`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>後續步驟

* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)
