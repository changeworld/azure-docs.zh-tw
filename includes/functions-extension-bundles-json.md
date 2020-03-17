---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79381562"
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

`extensionBundle` 中有下列屬性可供使用：

| 屬性 | 描述 |
| -------- | ----------- |
| id | Microsoft Azure 函式擴充功能套件組合的命名空間。 |
| version | 要安裝的套件組合版本。 函式執行階段一律會挑選版本範圍或間隔所定義的最大允許版本。 上述版本值允許所有 1.0.0 版本以上的所有套件組合版本，但不包括 2.0.0。 如需詳細資訊，請參閱[指定版本範圍的間隔標記法](/nuget/reference/package-versioning#version-ranges)。 |

套件組合版本會因為組合中的套件發生變更而增加。 當套件組合中的套件以主要版本遞增時，表示發生主要版本變更。 套件組合中的主要版本變更通常會與函式執行階段的主要版本變更一致。  

預設套件組合目前所安裝的擴充功能集合會在此 [extensions.json 檔案](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)中列舉。
