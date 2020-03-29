---
title: Azure 宇宙 DB 多主優勢
description: 瞭解 Azure Cosmos DB 中的多主機的優點，比較單個和多個寫入位置的延遲和 SLA 要求。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872004"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>瞭解 Azure Cosmos DB 中的多主機優勢

Cosmos DB 帳戶操作員應選擇適當的全域分發配置，以確保其應用程式的延遲、可用性和 RTO 要求。 與具有單寫入位置的帳戶（包括 99.999% 寫入可用性 SLA、<10 ms 寫入延遲 SLA（第 99 個百分位數）和 RTO = 0 區域災難的帳戶，配置了多個寫入位置的 Azure Cosmos 帳戶，具有顯著的優勢。

## <a name="comparison-of-features"></a>功能比較

|應用程式需求|多個寫入位置|單寫入位置|附註|
|---|---|---|---|
|在 P99 時寫入 <10ms 的延遲 SLA|**是**|否|具有單寫入位置的帳戶會為每個寫入產生額外的跨區域網路延遲。|
|在 P99 讀取 <10ms 的延遲 SLA|**是**|是| |
|寫入 SLA 的 99.999%|**是**|否|單寫位置保證 SLA 的帳戶為 99.99%|
|RTO = 0|**是**|否|在發生區域災難時，寫入次數為零。 具有單寫位置的帳戶的 RTO 為 15 分鐘。|

## <a name="next-steps"></a>後續步驟

如果仍希望禁用 Azure Cosmos 帳戶中的啟用多個Write位置，請[打開一個支援票證](https://azure.microsoft.com/support/create-ticket/)。
