---
title: TextBlock UI 元素
description: 描述 Azure 入口網站的 Microsoft.Common.TextBlock UI 元素。 用來將文字新增至介面。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: ba2c4d410891910c29ee1fda1065f8230ab171bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87063856"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft.Common.TextBlock UI 元素

可用來將文字新增至入口網站介面的控制項。

## <a name="ui-sample"></a>UI 範例

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textblock.png)

## <a name="schema"></a>結構描述

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>範例輸出

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>後續步驟

* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
