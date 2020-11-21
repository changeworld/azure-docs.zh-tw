---
title: 初始伺服器群組大小-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 為您的使用案例挑選適合的初始大小
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026398"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>挑選超大規模 (Citus) server 群組的初始大小

伺服器群組的大小（節點數目和硬體容量）很 [容易變更](howto-hyperscale-scale-grow.md)) 。 不過，您仍然需要為新的伺服器群組選擇初始大小。 以下是合理選擇的一些秘訣。

## <a name="multi-tenant-saas-use-case"></a>多租使用者 SaaS 使用案例

當您從現有的單一節點于 postgresql 資料庫實例遷移至超大規模 (Citus) 時，請選擇一個叢集，其中的背景工作虛擬核心和 RAM 的數量總計等於原始實例的數目。 在這類案例中，我們看到了2倍的效能改進，因為分區化能改善資源使用率，並允許較小的索引等。

VCore 計數實際上是唯一的決策。 RAM 配置目前取決於 vCore 計數，如 [超大規模 (Citus) 設定選項](concepts-hyperscale-configuration-options.md) ] 頁面中所述。
協調器節點不需要像工作者一樣多的 RAM，但沒有任何方法可單獨選擇 RAM 和虛擬核心。

## <a name="real-time-analytics-use-case"></a>即時分析使用案例

總虛擬核心：當工作資料適用于 RAM 時，您可預期超大規模 () Citus 的線性效能改進，與背景工作角色的數目成正比。 若要為您的需求判斷正確的虛擬核心數目，請考慮您的單一節點資料庫中的查詢目前延遲，以及超大規模 (Citus) 所需的延遲。 將目前的延遲除以所需的延遲，並將結果四捨五入。

背景工作角色 RAM：最好的情況是提供足夠的記憶體，讓大部分的工作中集合能夠容納到記憶體。 您的應用程式所使用的查詢類型會影響記憶體需求。 您可以執行說明在查詢上進行分析，以判斷所需的記憶體數量。 請記住，虛擬核心和 RAM 會依 [超大規模 (Citus) 設定選項](concepts-hyperscale-configuration-options.md) 一文中的說明進行調整。

## <a name="next-steps"></a>下一步

- [擴縮伺服器群組](howto-hyperscale-scale-grow.md)
- 深入瞭解伺服器群組 [效能選項](concepts-hyperscale-configuration-options.md)。
