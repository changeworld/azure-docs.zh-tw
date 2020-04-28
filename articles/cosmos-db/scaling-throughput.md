---
title: 調整 Azure Cosmos DB 的輸送量
description: 本文說明 Azure Cosmos DB 如何在布建 Azure Cosmos 帳戶的不同區域之間調整輸送量。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873500"
---
# <a name="globally-scale-provisioned-throughput"></a>全域調整佈建的輸送量 

在 Azure Cosmos DB 中，布建的輸送量會以每秒要求單位數（RU/秒或複數形式 RUs）來表示。 RU 會根據您的 Cosmos 容器來衡量讀取與寫入作業的成本，如下圖所示：

![要求單位](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

您可以在 Cosmos 容器或 Cosmos 資料庫上佈建 RU。 在容器上布建的 ru 僅適用于在該容器上執行的作業。 佈建於資料庫上的 RU 會在該資料庫內的所有容器之間共用 (但任何具有以獨佔方式指派之 RU 的容器除外)。

針對彈性調整布建的輸送量，您可以隨時增加或減少布建的 RU/秒。 如需詳細資訊，請參閱[如何布建輸送量](set-throughput.md)和彈性 scale Cosmos 容器和資料庫。 針對全域調整輸送量，您可以隨時在 Cosmos 帳戶中新增或移除區域。 如需詳細資訊，請參閱[在資料庫帳戶中新增/移除區域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)。 在許多情況下，將多個區域與 Cosmos 帳戶建立關聯非常重要，以達成世界各地的低延遲和[高可用性](high-availability.md)。

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>如何在區域中散發佈建輸送量

如果您在 Cosmos 容器（或資料庫）上布建 *' r '* 個 ru，Cosmos DB 確保與您的 Cosmos 帳戶相關聯的*每個*區域中都有 *' r '* 個 ru 可供使用。 每次您將新區域新增至您的帳戶時，Cosmos DB 會在新增的區域中自動布建 *' R '* 個 ru。 針對您的 Cosmos 容器執行的作業保證會在每個區域中取得 *' R '* 個 ru。 您無法選擇性地將 RU 指派給特定區域。 在 Cosmos 容器（或資料庫）上布建的 ru 會布建在與您 Cosmos 帳戶相關聯的所有區域中。

假設 Cosmos 容器是使用 *' R '* 個 ru 設定的，而且有 *' N '* 個區域與 Cosmos 帳戶相關聯，則：

- 如果 Cosmos 帳戶是使用單一寫入區域設定的，容器上全域可用的 ru 總數 = *R* x *N*。

- 如果 Cosmos 帳戶是使用多個寫入區域設定的，容器上全域可用的 ru 總數 = *R* x （*N*+ 1）。 系統會自動布建額外的*R* ru，以處理跨區域的更新衝突和防熵流量。

您選擇的[一致性模型](consistency-levels.md)也會影響輸送量。 相較于更強的一致性層級（例如，*限定過期*或*強*式一致性），您可以針對更寬鬆的一致性層級（例如，*會話*、*一致前置*詞和*最終*一致性）取得大約2倍的讀取輸送量。

## <a name="next-steps"></a>後續步驟

接下來，您可以瞭解如何在容器或資料庫上設定輸送量：

* [取得並設定適用於容器和資料庫的輸送量](set-throughput.md) 

