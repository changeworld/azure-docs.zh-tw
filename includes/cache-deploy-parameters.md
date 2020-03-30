---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67173788"
---
### <a name="cacheskuname"></a>cacheSKUName

新的 Azure Cache for Redis 的定價層。

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

範本定義此參數允許的值（基本值、標準值或高級值），如果未指定值，則分配預設值（基本值）。 「基本」提供單一節點，有多種大小可用，最大為 53 GB。 「標準」提供雙節點「主要/複本」，有多種大小可用，最大為 53 GB，還有高達 99.9% 的 SLA。

### <a name="cacheskufamily"></a>cacheSKUFamily

Sku 系列。

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

新 Azure Cache for Redis 執行個體的大小。

對於基本和標準系列：

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

高級值緩存容量的定義相同，但允許的值從 1 到 5 而不是從 0 到 6 運行。

該範本定義此參數允許的整數值（基本和標準族為 0 到 6;高級族為 1 到 5）。 如果未指定值，範本將為基本值和標準值分配預設值 0，1 表示高級值。

這些值對應于以下緩存大小：

| 值 | 基本和標準<br>緩存大小 | Premium<br>緩存大小 |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB（預設）                 | n/a                   |
| 1     | 1 GB                             | 6 GB（預設）        |
| 2     | 2.5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | n/a                   |
