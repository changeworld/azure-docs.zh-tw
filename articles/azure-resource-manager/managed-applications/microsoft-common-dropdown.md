---
title: DropDown UI 元素
description: 描述 Azure 入口網站的 Microsoft.Common.DropDown UI 元素。 當部署受控應用程式時，使用從可用的選項中選取。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: f5eac1d331bd439ad4066d1dea1b9aa950fcce60
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004426"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI 元素

下拉式清單中的選取控制項。 您可以只選取單一專案或多個專案。 您也可以選擇性地包含專案的描述。

## <a name="ui-sample"></a>UI 範例

下拉式元素具有不同的選項，可決定其在入口網站中的外觀。

只有在允許選取單一專案時，控制項才會顯示為：

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="[一般] 下拉式清單單選":::

當包含說明時，控制項會顯示為：

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="包含描述的 [Microsoft 一般] 下拉式清單選取專案":::

啟用多重選取時，控制項會新增 [**全選**] 選項和核取方塊，以選取一個以上的專案：

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="[一般] 下拉式清單複選":::

可以在啟用多重選取的情況下包含說明。

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="[Microsoft 一般] 下拉式清單複選包含描述":::

啟用篩選時，控制項會包含一個文字方塊，用以加入篩選值。

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="[Microsoft 一般] 下拉式清單複選包含描述":::

## <a name="schema"></a>結構描述

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
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

- 用 `multiselect` 來指定使用者是否可以選取一個以上的專案。
- 根據預設， `selectAll` `true` 當啟用多重選取時，會是。
- `filter`屬性可讓使用者在較長的選項清單中進行搜尋。
- `constraints.allowedValues` 的標籤是項目的顯示文字，其值為選取時的元素輸出值。
- 如果指定，預設值必須是 `constraints.allowedValues` 中存在的標籤。 如果未指定，則會選取 `constraints.allowedValues` 中的第一個項目。 預設值為 **null**。
- `constraints.allowedValues` 必須有至少一個項目。
- 若要模擬不需要的值，將包含標籤和 `""` (空字串) 值的項目新增至 `constraints.allowedValues`。
- `defaultDescription`屬性用於沒有描述的專案。

## <a name="next-steps"></a>接下來的步驟

* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
