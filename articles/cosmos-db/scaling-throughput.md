---
title: 調整 Azure Cosmos DB 的輸送量
description: 本文說明 Azure Cosmos DB 如何在布建 Azure Cosmos 帳戶的不同區域之間調整輸送量。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 94b5b3d2ab1f576f87ead23b389252ec96a20f11
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397346"
---
# <a name="how-does-azure-cosmos-db-globally-scale-the-provisioned-throughput"></a>Azure Cosmos DB 如何全域調整布建的輸送量？

在 Azure Cosmos DB 中，布建的輸送量會以要求單位/秒 (RU/秒或複數形式 ru) 來表示。 RU 會根據您的 Cosmos 容器來衡量讀取與寫入作業的成本，如下圖所示：

:::image type="content" source="./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png" alt-text="要求單位" border="false":::

您可以在 Cosmos 容器或 Cosmos 資料庫上佈建 RU。 在容器上布建的 ru 僅適用于在該容器上執行的作業。 佈建於資料庫上的 RU 會在該資料庫內的所有容器之間共用 (但任何具有以獨佔方式指派之 RU 的容器除外)。

針對彈性調整布建的輸送量，您可以隨時增加或減少布建的 RU/秒。 如需詳細資訊，請參閱 [如何布建輸送量](set-throughput.md) 和彈性 scale Cosmos 容器和資料庫。 針對全域調整的輸送量，您可以隨時在您的 Cosmos 帳戶中新增或移除區域。 如需詳細資訊，請參閱[在資料庫帳戶中新增/移除區域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)。 在許多案例中，將多個區域與 Cosmos 帳戶建立關聯很重要，以達成世界各地的低延遲和 [高可用性](high-availability.md) 。

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>如何在區域中散發佈建輸送量

如果您在 Cosmos 容器 (或資料庫) 上布建 *' r '* 個 ru，Cosmos DB 確保與 Cosmos 帳戶相關聯的*每個*區域中都有 *' r '* 個 ru。 每次您將新區域新增至您的帳戶時，Cosmos DB 會在新加入的區域中自動布建 *' R '* 個 ru。 針對 Cosmos 容器執行的作業保證會在每個區域中取得 *' R '* 個 ru。 您無法選擇性地將 RU 指派給特定區域。 在 Cosmos 容器 (或資料庫) 上布建的 ru 會在與您 Cosmos 帳戶相關聯的所有區域中布建。

假設 Cosmos 容器設定有 *' R '* 個 ru，而且有 *' N '* 個區域與 Cosmos 帳戶相關聯，則：

- 如果 Cosmos 帳戶設定為單一寫入區域，則在容器上全域可用的 ru 總數 = *R* x *N*。

- 如果 Cosmos 帳戶設定了多個寫入區域，則在容器上全域可用的 ru 總數 = *R* x (*N*+ 1) 。 系統會自動布建額外的 *R* ru 來處理跨區域的更新衝突和反熵流量。

您所選擇的 [一致性模型](consistency-levels.md) 也會影響輸送量。 相較于較強的一致性層級，您可以取得大約2x 的讀取輸送量， (例如， *會話*、 *一致前置* 詞和 *最終* 一致性) 相較于較強的一致性層級 (例如， *限定過期* 或 *強* 式一致性) 。

## <a name="next-steps"></a>後續步驟

接下來，您可以瞭解如何設定容器或資料庫的輸送量：

* [取得並設定適用於容器和資料庫的輸送量](set-throughput.md) 

