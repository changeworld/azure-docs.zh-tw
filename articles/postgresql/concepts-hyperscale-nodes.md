---
title: 節點 = 超大規模（Citus） - 用於後格雷SQL的 Azure 資料庫
description: 在 PostgreSQL Azure 資料庫中的伺服器組中瞭解兩種類型的節點（協調器和輔助角色）。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973997"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure 資料庫中的節點，用於後格雷SQL和超大規模（Citus）

Hyperscale （Citus） 託管類型允許用於 PostgreSQL 伺服器（稱為節點）的 Azure 資料庫在"共用無"體系結構中相互協調。 伺服器組中的節點共同保存的資料更多，並且使用的 CPU 內核比在單個伺服器上可能擁有的資料更多。 該體系結構還允許資料庫通過向伺服器組添加更多節點來擴展。

## <a name="coordinator-and-workers"></a>協調員和工人

每個伺服器組都有一個協調器節點和多個輔助工作群體。 應用程式將其查詢發送到協調器節點，該節點將其中繼到相關工作人員並累積其結果。 應用程式無法直接連接到工作人員。

對於每個查詢，協調器要麼將其路由到單個輔助節點，要麼將其並行化到多個節點，具體取決於所需的資料是駐在單個節點還是多個節點上。 協調員通過諮詢中繼資料表來決定該怎麼做。 這些表跟蹤輔助節點的 DNS 名稱和運行狀況，以及資料在節點之間的分佈。

## <a name="next-steps"></a>後續步驟
- 瞭解節點如何存儲[分散式資料](concepts-hyperscale-distributed-data.md)
