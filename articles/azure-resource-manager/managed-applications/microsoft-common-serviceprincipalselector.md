---
title: ServicePrincipalSelector UI 元素
description: 描述 Azure 入口網站的 ServicePrincipalSelector UI 元素。 提供下拉式清單來選擇應用程式識別碼和文字方塊，以輸入密碼或憑證指紋。
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 73b242754bfae53b6df5abd9c2c8dee33b973dad
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575991"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>ServicePrincipalSelector UI 元素

此控制項可讓使用者選取現有的服務主體或註冊新的服務主體。 當您選取 [ **建立新**的] 時，您會經歷註冊新應用程式的步驟。 當您選取現有的應用程式時，控制項會提供一個文字方塊來輸入密碼或憑證指紋。

## <a name="ui-sample"></a>UI 範例

預設視圖是由屬性中的值所決定 `defaultValue` 。 如果 `principalId` 屬性包含 (GUID) 的有效全域唯一識別碼，控制項就會搜尋應用程式的物件識別碼。 如果使用者未從下拉式清單中進行選取，則會套用預設值。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="ServicePrincipalSelector 初始視圖":::

當您從下拉式清單中選取 [ **建立新** 的或現有的應用程式識別碼] 時，就會顯示 [ **驗證類型** ]，在文字方塊中輸入密碼或憑證指紋。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="ServicePrincipalSelector 初始視圖":::

## <a name="schema"></a>結構描述

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
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

- 必要的屬性為：
  - `name`
  - `type`
  - `label`
  - `defaultValue`：指定預設值 `principalId` 和 `name` 。

- 選用屬性如下：
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
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
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

`appId`是您選取或建立的應用程式註冊的識別碼。 `objectId`是針對選取的應用程式註冊所設定之服務主體的 objectid 陣列。

從下拉式清單中沒有選取專案時， `newOrExisting` 屬性值為 **new**：

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

從下拉式清單中選取 [ **建立新** 的或現有的應用程式識別碼] 時， `newOrExisting` 屬性值會是 [已 **存在**]：

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
