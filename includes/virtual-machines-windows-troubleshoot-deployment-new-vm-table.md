---
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14abae6f6f72d724fffb1ccaa12f56fb6976f7a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67173979"
---
下表列出了 Windows 通用（gen.）和專用（規範）作業系統映射的可能上載和捕獲組合。 將正確處理沒有任何錯誤的組合以 Y 表示，而將擲回錯誤的組合會以 N 表示。下表說明遇到不同錯誤的原因和解決辦法。

| OS | 上傳特殊化 | 上傳一般化 | 擷取特殊化 | 擷取一般化 |
| --- | --- | --- | --- | --- |
| Windows 一般化 |N<sup>1</sup> |Y |N<sup>3</sup> |Y |
| Windows 特殊化 |Y |N<sup>2</sup> |Y |N<sup>4</sup> |

