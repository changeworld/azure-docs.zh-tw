---
title: IdentitySelector UI 元素
description: 描述 Azure 入口網站的 ManagedIdentity. IdentitySelector UI 元素。 用來將受控識別指派給資源。
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 91e3cd0b99825fd72eb342ce7a8555b046455538
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87063394"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>ManagedIdentity. IdentitySelector UI 元素

用於為部署中的資源指派 [受控](../../active-directory/managed-identities-azure-resources/overview.md) 識別的控制項。

## <a name="ui-sample"></a>UI 範例

此控制項是由下列元素所組成：

![ManagedIdentity. IdentitySelector 第一個步驟](./media/managed-application-elements/microsoft-managedidentity-identityselector-1.png)

當使用者選取 [ **新增**] 時，會開啟下列表單。 使用者可以為資源選取一個或多個使用者指派的身分識別。

![ManagedIdentity. IdentitySelector 第二步](./media/managed-application-elements/microsoft-managedidentity-identityselector-2.png)

選取的識別會顯示在表格中。 使用者可以在此資料表中新增或刪除專案。

![ManagedIdentity. IdentitySelector 第三步](./media/managed-application-elements/microsoft-managedidentity-identityselector-3.png)

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

- 使用 **defaultValue.systemAssignedIdentity** 來設定系統指派的身分識別選項控制項的初始值。 預設值是 **Off**。 允許的值如下：
  - **On** –系統指派的身分識別會指派給資源。
  - **Off** –未將系統指派的身分識別指派給資源。
  - **OnOnly** –系統指派的身分識別會指派給資源。 在部署期間，使用者無法編輯此值。
  - **OffOnly** –未將系統指派的身分識別指派給資源。 在部署期間，使用者無法編輯此值。

- 如果 **hideSystemAssignedIdentity** 設定為 **true**，則不會顯示用來設定系統指派身分識別的 UI。 此選項的預設值為 **false**。
- 如果 **hideUserAssignedIdentity** 設定為 **true**，則不會顯示用來設定使用者指派身分識別的 UI。 未將使用者指派的身分識別指派給資源。 此選項的預設值為 **false**。

## <a name="next-steps"></a>接下來的步驟

- 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
- 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。