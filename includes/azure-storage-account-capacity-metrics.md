---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 2a8f27c0df2224aed5c69c8c38f463a97e3cf294
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711187"
---
下表顯示 [帳戶層級的度量](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccounts)。

| 計量 | 描述 |
| ------------------- | ----------------- |
| UsedCapacity | 儲存體帳戶所使用的儲存體數量。 若為標準儲存體帳戶，則為 Blob、資料表、檔案和佇列所使用的容量總和。 若為進階儲存體帳戶和 Blob 儲存體帳戶，此數量和 BlobCapacity 相同。 <br/><br/> 單位：位元組 <br/> 彙總類型：Average <br/> 值範例：1024 |