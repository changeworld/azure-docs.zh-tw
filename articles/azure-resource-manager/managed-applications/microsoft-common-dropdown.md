---
title: 下拉 UI 元素
description: 描述 Azure 入口網站的 Microsoft.Common.DropDown UI 元素。 用於在部署託管應用程式時從可用選項中進行選擇。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652381"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI 元素

下拉式清單中的選取控制項。

## <a name="ui-sample"></a>UI 範例

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>結構描述

```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>範例輸出

```json
"two"
```

## <a name="remarks"></a>備註

- `constraints.allowedValues` 的標籤是項目的顯示文字，其值為選取時的元素輸出值。
- 如果指定，預設值必須是 `constraints.allowedValues` 中存在的標籤。 如果未指定，則會選取 `constraints.allowedValues` 中的第一個項目。 預設值為 **null**。
- `constraints.allowedValues` 必須有至少一個項目。
- 若要模擬不需要的值，將包含標籤和 `""` (空字串) 值的項目新增至 `constraints.allowedValues`。

## <a name="next-steps"></a>後續步驟

* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
