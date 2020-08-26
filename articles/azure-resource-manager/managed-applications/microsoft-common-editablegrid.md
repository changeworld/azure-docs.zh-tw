---
title: EditableGrid UI 元素
description: 描述 Azure 入口網站的 EditableGrid UI 元素。 可讓使用者收集表格式輸入。
author: tfitzmac
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
ms.openlocfilehash: 04f86883a75110985d1cbe050fe3fd3e0582986a
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893711"
---
# <a name="microsoftcommoneditablegrid-ui-element"></a>EditableGrid UI 元素

用於收集表格式輸入的控制項。 方格內的所有欄位都是可編輯的，且資料列的數目可能會不同。

## <a name="ui-sample"></a>UI 範例

:::image type="content" source="./media/managed-application-elements/microsoft-common-editablegrid.png" alt-text="EditableGrid":::

## <a name="schema"></a>結構描述

```json
{
  "name": "people",
  "type": "Microsoft.Common.EditableGrid",
  "ariaLabel": "Enter information per person",
  "label": "People",
  "constraints": {
    "width": "Full",
    "rows": {
      "count": {
        "min": 1,
        "max": 10
      }
    },
    "columns": [
      {
        "id": "colName",
        "header": "Name",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.TextBox",
          "placeholder": "Full name",
          "constraints": {
            "required": true,
            "validations": [
              {
                "isValid": "[startsWith(last(take(steps('grid').people, $rowIndex)).colName, 'contoso')]",
                "message": "Must start with 'contoso'."
              },
              {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
              }
            ]
          }
        }
      },
      {
        "id": "colGender",
        "header": "Gender",
        "width": "1fr",
        "element": {
          "name": "dropDown1",
          "type": "Microsoft.Common.DropDown",
          "placeholder": "Select a gender...",
          "constraints": {
            "allowedValues": [
              {
                "label": "Female",
                "value": "female"
              },
              {
                "label": "Male",
                "value": "male"
              },
              {
                "label": "Other",
                "value": "other"
              }
            ],
            "required": true
          }
        }
      },
      {
        "id": "colContactPreference",
        "header": "Contact preference",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.OptionsGroup",
          "constraints": {
            "allowedValues": [
              {
                "label": "Email",
                "value": "email"
              },
              {
                "label": "Text",
                "value": "text"
              }
            ],
            "required": true
          }
        }
      }
    ]
  }
}
```

## <a name="sample-output"></a>範例輸出

```json
{
  "colName": "contoso",
  "colGender": "female",
  "colContactPreference": "email"
}
```

## <a name="remarks"></a>備註

- Columns 陣列中唯一有效的控制項是 [TextBox](microsoft-common-textbox.md)、 [Microsoft.common.optionsgroup](microsoft-common-optionsgroup.md)和 [下拉式清單](microsoft-common-dropdown.md)。
- `$rowIndex`變數僅適用于方格資料行之子系內所含的運算式。 它是代表專案之相對資料列索引的整數，而且計數會從1開始，並以1為單位遞增。 如架構的 `"columns":` 一節所示， `$rowIndex` 會使用來進行驗證。
- 使用變數執行驗證時 `$rowIndex` ，您可以藉由結合和命令來取得目前資料列的值 `last()` `take()` 。

  例如：

  `last(take(<reference_to_grid>, $rowIndex))`

- `label`屬性不會顯示為控制項的一部分，而是顯示在最後一個索引標籤摘要中。
- `ariaLabel`屬性是方格的協助工具標籤。 為使用螢幕閱讀程式的使用者指定有用的文字。
- `constraints.width`屬性是用來設定方格的整體寬度。 選項為 _Full_、 _Medium_、 _Small_。 預設值為 _Full_。
- 資料 `width` 行之子系上的屬性會決定資料行的寬度。 寬度是使用分數單位（例如 _3fr_）來指定，其中的總空間會分配給以比例計算的單位。 如果未指定資料行寬度，則預設值為 _1fr_。

## <a name="next-steps"></a>後續步驟

- 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
- 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
