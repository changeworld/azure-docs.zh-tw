---
title: TagsByResource UI 元素
description: 描述 Azure 入口網站的 TagsByResource UI 元素。 使用，在部署期間將標記套用至資源。
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652199"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>TagsByResource UI 元素

控制項，用於將[標記](../management/tag-resources.md)與部署中的資源產生關聯。

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

- 必須至少指定陣列中的`resources`一個專案。
- 中的`resources`每個元素都必須是完整的資源類型。 這些元素會出現在 [**資源**] 下拉式清單中，並由使用者 taggable。
- 控制項的輸出會格式化以方便指派 Azure Resource Manager 範本中的標記值。 若要在範本中接收控制項的輸出，請在您的範本中包含參數，如下列範例所示：

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  針對每個可以加上標籤的資源，將 tags 屬性指派給該資源類型的參數值：

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- 存取 tagsByResource 參數時，請使用[if](../templates/template-functions-logical.md#if)函數。 它可讓您在未將標記指派給指定的資源類型時，指派空的物件。

## <a name="next-steps"></a>後續步驟

- 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
- 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
