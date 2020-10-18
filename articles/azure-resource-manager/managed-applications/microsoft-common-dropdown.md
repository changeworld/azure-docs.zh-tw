---
title: 下拉式 UI 元素
description: 描述 Azure 入口網站的 Microsoft.Common.DropDown UI 元素。 用來在部署受控應用程式時，從可用的選項中進行選取。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: b9b27a432776635290c7e8e796e84d8c1e0e8675
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168336"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI 元素

下拉式清單中的選取控制項。 您可以只允許選取單一專案或多個專案。 您也可以選擇性地包含專案的描述。

## <a name="ui-sample"></a>UI 範例

下拉式元素有不同的選項，可決定其在入口網站中的外觀。

當只允許選取單一專案時，控制項會顯示為：

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Microsoft. 常用的單一選取專案":::

當包含描述時，控制項會顯示為：

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Microsoft. 常用的單一選取專案":::

啟用複選時，控制項會新增 [ **選取全部** ] 選項和核取方塊，以選取一個以上的專案：

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Microsoft. 常用的單一選取專案":::

描述可以包含在啟用多重選取的情況下。

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Microsoft. 常用的單一選取專案":::

啟用篩選時，控制項就會包含用來加入篩選值的文字方塊。

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Microsoft. 常用的單一選取專案":::

## <a name="schema"></a>結構描述

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "placeholder": "",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
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

- 使用 `multiselect` 指定使用者是否可以選取一個以上的專案。
- 依預設， `selectAll` 會 `true` 在啟用多重選取時使用。
- `filter`屬性可讓使用者在很長的選項清單中進行搜尋。
- `constraints.allowedValues` 的標籤是項目的顯示文字，其值為選取時的元素輸出值。
- 如果指定，預設值必須是 `constraints.allowedValues` 中存在的標籤。 如果未指定，則會選取 `constraints.allowedValues` 中的第一個項目。 預設值為 **null**。
- `constraints.allowedValues` 必須有至少一個項目。
- 若要模擬不需要的值，將包含標籤和 `""` (空字串) 值的項目新增至 `constraints.allowedValues`。
- `defaultDescription`屬性用於沒有描述的專案。
- `placeholder`當使用者開始編輯時，屬性是解說文字會消失。 如果 `placeholder` 和 `defaultValue` 都已定義， `defaultValue` 就會優先使用並顯示。

## <a name="next-steps"></a>後續步驟

* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
