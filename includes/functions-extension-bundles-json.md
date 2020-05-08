---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
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

下列屬性可用於`extensionBundle`：

| 屬性 | 描述 |
| -------- | ----------- |
| id | Microsoft Azure 函式擴充功能配套的命名空間。 |
| version | 要安裝的配套版本。 函數執行時間一律會挑選版本範圍或間隔所定義的最大允許版本。 上述版本值允許所有來自1.0.0 的套件組合版本，但不包括2.0.0。 如需詳細資訊，請參閱[指定版本範圍的間隔標記法](/nuget/reference/package-versioning#version-ranges)。 |

配套版本隨著套件在配套中的增加而變更。 當配套中的套件以主要版本遞增時，就會發生主要版本變更。 套件組合中的主要版本變更通常會與函式執行時間的主要版本變更一致。  

預設配套所安裝的目前延伸模組集合會在此[副檔名 json](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)檔案中列舉。
