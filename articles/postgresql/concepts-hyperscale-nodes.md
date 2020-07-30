---
title: 節點–超大規模資料庫（Citus）-適用於 PostgreSQL 的 Azure 資料庫
description: 在適用於 PostgreSQL 的 Azure 資料庫中，瞭解伺服器群組中兩種類型的節點、協調器和背景工作。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: af743ca56572f507091db01f11d3283294a9e3d5
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382792"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫中的節點–超大規模資料庫（Citus）

超大規模資料庫（Citus）裝載類型可讓適用於 PostgreSQL 的 Azure 資料庫伺服器（稱為節點）在「無共用」架構中彼此協調。 伺服器群組中的節點會共同保存較多的資料，並使用比在單一伺服器上可能更多的 CPU 核心。 此架構也可讓您將更多節點新增至伺服器群組來調整資料庫。

## <a name="coordinator-and-workers"></a>協調員和背景工作

每個伺服器群組都有協調器節點和多個背景工作角色。 應用程式會將其查詢傳送至協調器節點，以將其轉送至相關的背景工作並累計其結果。 應用程式無法直接連接到背景工作角色。

超大規模資料庫（Citus）可讓資料庫管理員*散發*資料表，並將不同的資料列儲存在不同的背景工作節點上。 分散式資料表是超大規模資料庫效能的關鍵。 無法散發資料表會將它們完全保留在協調器節點上，而且無法利用跨電腦的平行處理原則。

針對分散式資料表上的每個查詢，協調器會將其路由傳送至單一背景工作節點，或根據所需的資料是否存在於單一節點上或多個來平行處理。 協調器會透過諮詢中繼資料資料表來決定要執行的動作。 這些資料表會追蹤背景工作節點的 DNS 名稱和健康情況，以及跨節點的資料分佈。

## <a name="next-steps"></a>後續步驟
- 瞭解[分散式資料表](concepts-hyperscale-distributed-data.md)
