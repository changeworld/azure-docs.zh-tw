---
title: InfoBox UI 元素
description: 描述 Azure 入口網站的 InfoBox UI 元素。 用來在部署受控應用程式時加入文字或警告。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 7580ac0650706d6aee49bbf0e8235e8c5dab33f4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87033340"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft.Common.InfoBox UI 元素

可新增資訊方塊的控制項。 此方塊包含重要的文字或警告，可協助使用者了解他們所要提供的值。 它也可以連結至 URI 來提供更多資訊。

## <a name="ui-sample"></a>UI 範例

![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft-common-infobox.png)


## <a name="schema"></a>結構描述

```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="sample-output"></a>範例輸出

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>備註

* 對於 `icon`，請使用 **無**、**資訊**、**警告**或**錯誤**。
* `uri` 是選用屬性。

## <a name="next-steps"></a>後續步驟

* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
