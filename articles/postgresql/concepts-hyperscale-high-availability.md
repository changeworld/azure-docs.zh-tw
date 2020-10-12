---
title: 高可用性–超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 高可用性和嚴重損壞修復概念
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 406b4f2ada665d65ee0452579e24744d1f7cfc63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314848"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫中的高可用性–超大規模 (Citus) 

高可用性 (HA) 在伺服器群組中維護每個節點的待命複本，以避免資料庫停機。 如果節點停止運作，超大規模 (Citus) 會將失敗節點的連入連線切換到其待命。 容錯移轉會在數分鐘內發生，而且升級的節點一律會透過於 postgresql 同步串流複寫來取得最新資料。

若要在協調器節點上利用 HA，資料庫應用程式必須偵測並重試中斷的連接和失敗的交易。 您可以使用相同的連接字串來存取新升級的協調器。

復原可以分為三個階段：偵測、容錯移轉和完整復原。  超大規模 (Citus) 會在每個節點上執行週期性健康情況檢查，並在四次失敗檢查後判斷節點已關閉。 超大規模 (Citus) 然後將待命升級為主要節點狀態 (容錯移轉) ，並布建新的待命。
串流複寫會開始，讓新的節點保持在最新狀態。  當所有資料都已複寫之後，節點就已達到完整復原。

### <a name="next-steps"></a>後續步驟

- 瞭解如何在超大規模 (Citus) server 群組中 [啟用高可用性](howto-hyperscale-high-availability.md) 。
