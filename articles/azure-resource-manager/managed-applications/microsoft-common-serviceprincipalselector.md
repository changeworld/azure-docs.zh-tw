---
title: ServicePrincipalSelector UI 元素
description: 描述 Azure 入口網站的 ServicePrincipalSelector UI 元素。 提供控制項來選擇應用程式和文字方塊，以輸入密碼或憑證指紋。
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184445"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>ServicePrincipalSelector UI 元素

此控制項可讓使用者選取現有的 [服務主體](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 或註冊新的應用程式。 當您選取 [ **建立新** 的] 時，請遵循下列步驟來註冊新的應用程式。 當您選取現有的應用程式時，控制項會提供一個文字方塊來輸入密碼或憑證指紋。

## <a name="ui-samples"></a>UI 範例

您可以使用預設應用程式、建立新的應用程式，或是使用現有的應用程式。

### <a name="use-default-application-or-create-new"></a>使用預設應用程式或建立新的

預設的視圖取決於屬性中的值 `defaultValue` ，而 **服務主體類型** 會設定為 [ **建立新** 的]。 如果 `principalId` 屬性包含 (GUID) 的有效全域唯一識別碼，控制項就會搜尋應用程式的 `objectId` 。 如果使用者未從控制項進行選取，則會套用預設值。

如果您想要註冊新的應用程式，請選取 [ **變更選取專案** ]，並顯示 [ **註冊應用程式** ] 對話方塊。 輸入 **名稱**、 **支援的帳戶類型**，然後選取 [ **註冊** ] 按鈕。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="ServicePrincipalSelector 初始觀點。":::

註冊新的應用程式之後，請使用 **驗證類型** 來輸入密碼或憑證指紋。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="ServicePrincipalSelector authentication。":::

### <a name="use-existing-application"></a>使用現有的應用程式

若要使用現有的應用程式，請選擇 [ **選取現有** 的]，然後選取 [ **進行** 選取]。 使用 [ **選取應用程式** ] 對話方塊可搜尋應用程式的名稱。 從結果中選取應用程式，然後選取 [ **選取** ] 按鈕。 選取應用程式之後，控制項會顯示 **驗證類型** 以輸入密碼或憑證指紋。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="ServicePrincipalSelector 選取現有的應用程式。":::

## <a name="schema"></a>結構描述

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>備註

- 必要的屬性如下所示：
  - `name`
  - `type`
  - `label`
  - `defaultValue`：指定預設值 `principalId` 和 `name` 。

- 選用的屬性如下所示：
  - `toolTip`：將工具提示附加 `infoBalloon` 至每個標籤。
  - `visible`：隱藏或顯示控制項。
  - `options`：指定是否應讓憑證指紋選項可供使用。
  - `constraints`：密碼驗證的 Regex 條件約束。

## <a name="example"></a>範例

以下是控制項的範例 `Microsoft.Common.ServicePrincipalSelector` 。 `defaultValue`屬性會設定 `principalId` 為 `<default guid>` 預設應用程式識別碼 GUID 的預留位置。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>範例輸出

`appId`是您選取或建立的應用程式註冊的識別碼。 `objectId`是針對選取的應用程式註冊所設定之服務主體的物件識別碼陣列。

當控制項未進行任何選取時， `newOrExisting` 屬性值為 **new**：

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

從控制項選取 [ **建立新** 的] 或 [現有的應用程式] 時， `newOrExisting` 屬性值會是 [已 **存在**]：

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>後續步驟

- 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
- 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。