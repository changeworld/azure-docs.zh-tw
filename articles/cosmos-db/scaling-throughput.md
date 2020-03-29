---
title: 調整 Azure Cosmos DB 的輸送量
description: 本文介紹 Azure Cosmos DB 如何跨預配 Azure Cosmos 帳戶的不同區域縮放輸送量。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873500"
---
# <a name="globally-scale-provisioned-throughput"></a>全域調整佈建的輸送量 

在 Azure Cosmos DB 中，預配輸送量表示為請求單位/秒（RU/s 或複數形式 RU）。 RU 會根據您的 Cosmos 容器來衡量讀取與寫入作業的成本，如下圖所示：

![要求單位](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

您可以在 Cosmos 容器或 Cosmos 資料庫上佈建 RU。 容器上預配的 R 機僅可用於對該容器執行的操作。 佈建於資料庫上的 RU 會在該資料庫內的所有容器之間共用 (但任何具有以獨佔方式指派之 RU 的容器除外)。

對於彈性擴展預配輸送量，您可以隨時增加或減少預配的 RU/s。 有關詳細資訊，請參閱[如何預配輸送量](set-throughput.md)和彈性擴展 Cosmos 容器和資料庫。 對於全域縮放輸送量，您可以隨時從 Cosmos 帳戶添加或刪除區域。 如需詳細資訊，請參閱[在資料庫帳戶中新增/移除區域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)。 在許多情況下，將多個區域與 Cosmos 帳戶關聯非常重要 - 實現全球的低延遲和[高可用性](high-availability.md)。

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>如何在區域中散發佈建輸送量

如果在 Cosmos 容器（或資料庫）上預配 *"R"R，Cosmos* DB 可確保與 Cosmos 帳戶關聯的*每個*區域都有 *"R"R。* 每次向帳戶添加新區域時，Cosmos DB 都會在新添加的區域中自動提供 *"R"R。* 對 Cosmos 容器執行的操作保證在每個區域獲得 *"R"R。* 您無法選擇性地將 RU 指派給特定區域。 在 Cosmos 容器（或資料庫）上預配的 R 單位預配在與 Cosmos 帳戶關聯的所有區域中。

假設 Cosmos 容器配置了 *"R"R，* 並且存在與 Cosmos 帳戶關聯的 *"N"* 區域，則：

- 如果 Cosmos 帳戶配置了單個寫入區域，則容器上全域可用的總 R 機數 = *R* x *N*。

- 如果 Cosmos 帳戶配置了多個寫入區域，則容器上全域可用的總 R 數將 = *R* x *（N*+1）。 額外的*RR*自動預配，以處理跨區域的更新衝突和反熵流量。

您選擇的[一致性模型](consistency-levels.md)也會影響輸送量。 與更強的一致性級別（例如，*有限過時*或*強*一致性）相比，您可以獲得大約 2 倍的讀取輸送量，以獲得更輕鬆的一致性級別（例如，*會話*、*一致首碼*和*最終*一致性）。

## <a name="next-steps"></a>後續步驟

接下來，您可以瞭解如何配置容器或資料庫上的輸送量：

* [取得並設定適用於容器和資料庫的輸送量](set-throughput.md) 

