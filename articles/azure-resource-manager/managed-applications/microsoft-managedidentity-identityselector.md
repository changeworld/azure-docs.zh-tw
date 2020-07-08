---
title: IdentitySelector UI 元素
description: 描述 Azure 入口網站的 Microsoft.managedidentity. IdentitySelector UI 元素。 使用將受控識別指派給資源。
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77087540"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft.managedidentity. IdentitySelector UI 元素

控制項，用於指派部署中資源的[受控](../../active-directory/managed-identities-azure-resources/overview.md)識別。

## <a name="ui-sample"></a>UI 範例

控制項是由下列元素所組成：

![Microsoft.managedidentity. IdentitySelector 第一個步驟](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

當使用者選取 [**新增**] 時，會開啟下列表單。 使用者可以為資源選取一個或多個使用者指派的身分識別。

![Microsoft.managedidentity. IdentitySelector 第二個步驟](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

選取的身分識別會顯示在資料表中。 使用者可以在此資料表中加入或刪除專案。

![Microsoft.managedidentity. IdentitySelector 第三步](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

## <a name="schema"></a>結構描述

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>範例輸出

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>備註

- 使用**defaultValue.systemAssignedIdentity**來設定系統指派的身分識別選項控制項的初始值。 預設值為**Off**。 允許下列值：
  - **On** –系統指派的身分識別會指派給資源。
  - **關閉**–系統指派的身分識別不會指派給資源。
  - **OnOnly** –系統指派的身分識別會指派給資源。 在部署期間，使用者無法編輯此值。
  - **OffOnly** –系統指派的身分識別未指派給資源。 在部署期間，使用者無法編輯此值。

- 如果**hideSystemAssignedIdentity**設為**true**，則不會顯示設定系統指派身分識別的 UI。 此選項的預設值為**false**。
- 如果**hideUserAssignedIdentity**設為**true**，則不會顯示設定使用者指派身分識別的 UI。 未將使用者指派的身分識別指派給資源。 此選項的預設值為**false**。

## <a name="next-steps"></a>後續步驟

- 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
- 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。