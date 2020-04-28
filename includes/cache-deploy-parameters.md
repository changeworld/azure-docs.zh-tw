---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
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

此範本會定義此參數允許的值（基本、標準或高階），如果未指定任何值，則會指派預設值（基本）。 「基本」提供單一節點，有多種大小可用，最大為 53 GB。 「標準」提供雙節點「主要/複本」，有多種大小可用，最大為 53 GB，還有高達 99.9% 的 SLA。

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

針對基本和標準系列：

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

Premium 值快取容量的定義相同，不同之處在于允許的值是從1到5，而不是從0到6。

此範本會定義此參數允許的整數值（基本和標準系列為0到6，Premium 系列為1到5）。 如果未指定任何值，範本會為 Basic 和 Standard 指派0的預設值，1代表 Premium。

這些值會對應至下列快取大小：

| 值 | 基本和標準<br>快取大小 | Premium<br>快取大小 |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB （預設值）                 | n/a                   |
| 1     | 1 GB                             | 6 GB （預設值）        |
| 2     | 2.5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | n/a                   |
