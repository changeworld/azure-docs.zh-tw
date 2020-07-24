---
title: CheckBox UI 元素
description: 描述 Azure 入口網站的 [一般] 核取方塊 UI 元素。 可讓使用者選取以檢查或取消核取選項。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096341"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>[一般] 核取方塊 UI 元素

CheckBox 控制項可讓使用者檢查或取消核取選項。 當控制項已核取時，控制項會傳回**true** ，如果未核取則傳回**false** 。

## <a name="ui-sample"></a>UI 範例

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="[一般] 核取方塊":::

## <a name="schema"></a>結構描述

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>範例輸出

```json
true
```

## <a name="remarks"></a>備註

當您將 [**必要**] 設定為 [ **true**] 時，使用者必須選取此核取方塊。 如果使用者未選取此核取方塊，則會顯示驗證訊息。

## <a name="next-steps"></a>後續步驟

* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
