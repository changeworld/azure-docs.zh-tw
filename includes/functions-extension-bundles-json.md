---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689540"
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