---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d93de4ed758afb5e951bb5e19f4f7adb290e461c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "67173982"
---
下表列出 Linux 一般化和特殊化作業系統映像可能的上傳和擷取組合。 將正確處理沒有任何錯誤的組合以 Y 表示，而將擲回錯誤的組合會以 N 表示。下表說明遇到不同錯誤的原因和解決辦法。

| OS | 上傳特殊化 | 上傳一般化 | 擷取特殊化 | 擷取一般化 |
| --- | --- | --- | --- | --- |
| Linux 一般化 |N<sup>1</sup> |Y |N<sup>3</sup> |Y |
| Linux 特殊化 |Y |N<sup>2</sup> |Y |N<sup>4</sup> |

