---
title: 高可用性 = 超大規模 （Citus） - 用於後格雷SQL的 Azure 資料庫
description: 高可用性和災害復原概念
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975528"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure 資料庫中高可用性，適用于 PostgreSQL 和超大規模（Citus）

高可用性 （HA） 通過維護伺服器組中每個節點的備用副本來避免資料庫停機時間。 如果節點出現故障，超大規模將傳入連接從故障節點切換到其備用節點。 容錯移轉在幾分鐘內發生，升級的節點始終通過 PostgreSQL 同步流複製獲得新資料。

為了利用協調節點上的 HA，資料庫應用程式需要檢測和重試丟棄的連接和失敗的交易。 新升級的協調員將使用相同的連接字串訪問。

恢復可以分為三個階段：檢測、容錯移轉和完全恢復。  Hyperscale 在每個節點上運行定期運行狀況檢查，在四次檢查失敗後，它確定節點已關閉。 然後，Hyperscale 將備用節點提升為主節點狀態（容錯移轉），並設置新的備用資料庫。
流式複製開始，使新節點成為最新的節點。  複製所有資料後，節點已達到完全恢復。

### <a name="next-steps"></a>後續步驟

- 瞭解如何在超大規模伺服器組中[啟用高可用性](howto-hyperscale-high-availability.md)。
