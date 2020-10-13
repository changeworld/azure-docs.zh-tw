---
title: ResourceSelector UI 元素
description: 描述 Azure 入口網站的 ResourceSelector UI 元素。 用來取得現有資源的清單。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: d4b21f092733f6ebb3c2bf5aa06c1d2782323730
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096510"
---
# <a name="microsoftsolutionsresourceselector-ui-element"></a>ResourceSelector UI 元素

ResourceSelector 可讓使用者從訂用帳戶中選取現有的資源。

## <a name="ui-sample"></a>UI 範例

![ResourceSelector](./media/managed-application-elements/microsoft-solutions-resourceselector.png)

## <a name="schema"></a>結構描述

```json
{
    "name": "storageSelector",
    "type": "Microsoft.Solutions.ResourceSelector",
    "label": "Select storage accounts",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "options": {
        "filter": {
            "subscription": "onBasics",
            "location": "onBasics"
        }
    }
}
```

## <a name="sample-output"></a>範例輸出

```json
"name": "{resource-name}",
"id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}",
"location": "{deployed-location}"
```

## <a name="remarks"></a>備註

在 `resourceType` 屬性中，為您想要在清單中顯示的資源提供資源提供者命名空間和資源類型名稱。

`filter`屬性會限制資源的可用選項。 您可以依位置或訂用帳戶限制結果。 若只要顯示符合基本概念之選取範圍的資源，請使用 `onBasics` 。 若要顯示所有資源，請使用 `all` 。 預設值為 `all`。

## <a name="next-steps"></a>後續步驟

* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
