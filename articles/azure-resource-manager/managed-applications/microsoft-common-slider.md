---
title: 滑杆 UI 元素
description: 描述 Azure 入口網站的 Microsoft 一般. 滑杆 UI 元素。 可讓使用者從某個範圍的選項設定值。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: tomfitz
ms.openlocfilehash: bb168a4bff6d2570f539307530232b5063bb535c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096337"
---
# <a name="microsoftcommonslider-ui-element"></a>Microsoft. 一般. 滑杆 UI 元素

滑杆控制項可讓使用者從允許的值範圍選取。

## <a name="ui-sample"></a>UI 範例

:::image type="content" source="./media/managed-application-elements/microsoft-common-slider.png" alt-text="[常用] 滑杆":::

## <a name="schema"></a>結構描述

```json
{
    "name": "memorySize",
    "type": "Microsoft.Common.Slider",
    "min": 1,
    "max": 64,
    "label": "Memory",
    "subLabel": "MB",
    "defaultValue": 24,
    "showStepMarkers": false,
    "toolTip": "Pick the size in MB",
    "constraints": {
        "required": false
    },
    "visible": true
}
```

## <a name="sample-output"></a>範例輸出

```json
26
```

## <a name="remarks"></a>備註

- `min`和 `max` 值是必要的。 他們會設定滑杆的起點和終點。
- `showStepMarkers`屬性預設為 true。 只有當從最小到最大的範圍為100或更低時，才會顯示步驟標記。


## <a name="next-steps"></a>接下來的步驟

* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
