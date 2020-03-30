---
title: 標識選擇者 UI 元素
description: 描述 Azure 門戶的 Microsoft.託管標識.標識選擇或 UI 元素。 用於將託管標識分配給資源。
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087540"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>微軟.託管標識.身份選擇者 UI 元素

用於為部署中的資源配置[託管標識](../../active-directory/managed-identities-azure-resources/overview.md)的控制項。

## <a name="ui-sample"></a>UI 範例

該控制項由以下元素組成：

![微軟.託管身份.身份選擇者第一步](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

當使用者選擇 **"添加**"時，將打開以下表單。 使用者可以為資源選擇一個或多個使用者分配的身份。

![微軟.託管身份.身份選擇或第二步](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

所選標識顯示在表中。 使用者可以從此表中添加或刪除專案。

![微軟.託管身份.身份選擇或第三步](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

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

- 使用**defaultValue.system 分配標識**為系統分配的標識選項控制項設置初始值。 預設值為 **"關閉**"。 允許以下值：
  - **打開**= 系統分配的標識分配給資源。
  - **關閉**= 系統分配的標識未分配給資源。
  - **僅限 =** 將系統分配的標識分配給資源。 在部署期間，使用者無法編輯此值。
  - **僅**關閉 - 未將系統分配的標識分配給資源。 在部署期間，使用者無法編輯此值。

- 如果**選項.hideSystem 分配標識**設置為**true，** 則不顯示用於配置系統分配標識的 UI。 此選項的預設值**為 false**。
- 如果**選項.hideUser 分配標識**設置為**true，** 則不顯示用於配置使用者分配標識的 UI。 未為資源配置使用者分配的標識。 此選項的預設值**為 false**。

## <a name="next-steps"></a>後續步驟

- 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
- 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。