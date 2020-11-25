---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 48e2d798a3dcf7354e68d07785c2762ba03f39f8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011121"
---
下表顯示 [帳戶層級的度量](../articles/azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)。

| Metric | 描述 |
| ------------------- | ----------------- |
| UsedCapacity | 儲存體帳戶所使用的儲存體數量。 若為標準儲存體帳戶，則為 Blob、資料表、檔案和佇列所使用的容量總和。 若為進階儲存體帳戶和 Blob 儲存體帳戶，此數量和 BlobCapacity 相同。 <br/><br/> 單位：位元組 <br/> 彙總類型：Average <br/> 值範例：1024 |