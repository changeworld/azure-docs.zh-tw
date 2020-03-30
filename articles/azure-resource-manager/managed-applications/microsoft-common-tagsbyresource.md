---
title: 標記ByResource UI 元素
description: 描述 Azure 門戶的 Microsoft.Common.TagByResource UI 元素。 用於在部署期間將標記應用於資源。
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652199"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>微軟.通用.標記資源 UI 元素

用於將[標記](../management/tag-resources.md)與部署中的資源關聯的控制項。

## <a name="ui-sample"></a>UI 範例

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>結構描述

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>範例輸出

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>備註

- 必須至少指定一個陣列`resources`中的項。
- 中的每個`resources`元素都必須是完全限定的資源類型。 這些元素顯示在 **"資源"** 下拉清單中，並且由使用者標記。
- 控制項的輸出設置格式，以便輕鬆在 Azure 資源管理器範本中分配標記值。 要在範本中接收控制項的輸出，請在範本中包括一個參數，如以下示例所示：

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  對於可標記的每個資源，將標記屬性分配給該資源類型的參數值：

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- 訪問標記 ByResource 參數時，請使用[if](../templates/template-functions-logical.md#if)函數。 它使您能夠在未為給定資源類型分配標記時分配空物件。

## <a name="next-steps"></a>後續步驟

- 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
- 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
