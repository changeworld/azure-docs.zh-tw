---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878235"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

以下屬性在`extensionBundle`中可用。

| 屬性 | 描述 |
| -------- | ----------- |
| id | Microsoft Azure 函數擴展包的命名空間。 |
| version | 要安裝的捆綁包的版本。 函數運行時始終選擇由版本範圍或間隔定義的最大允許版本。 上述版本值允許從 1.0.0 到 但不包括 2.0.0 的所有捆綁版本。 有關詳細資訊,請參閱[用於指定版本範圍的間隔表示法](/nuget/reference/package-versioning#version-ranges)。 |

捆綁包版本隨著捆綁包中的包更改而遞增。 當包中的包由主要版本增加時,將發生重大版本更改。 捆綁包中的主要版本更改通常與函數運行時的主要版本更改一致。  

默認捆綁包安裝的當前擴展集在此[擴展.json 檔中](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)枚舉。
