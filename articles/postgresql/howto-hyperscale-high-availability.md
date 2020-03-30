---
title: 配置高可用性 - 超大規模（Citus） - 用於後格雷SQL的 Azure 資料庫
description: 如何啟用或禁用高可用性
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a8d4b5949b34d16191e9ec10a1dd39faff3660dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977642"
---
# <a name="configure-hyperscale-citus-high-availability"></a>配置超大規模 （Citus） 高可用性

用於 PostgreSQL - 超大規模 （Citus） 的 Azure 資料庫提供高可用性 （HA）以避免資料庫停機。 啟用 HA 後，伺服器組中的每個節點都將獲得備用。 如果原始節點不正常，將升級其備用節點以替換它。

> [!IMPORTANT]
> 由於 HA 使組中的伺服器數翻倍，因此成本也會加倍。

在伺服器組創建期間，或在 Azure 門戶中的伺服器組的 **"配置"** 選項卡中啟用 HA 是可能的。 在這兩種情況下，使用者介面看起來都相似。 將**高可用性**的滑塊拖動為"是"：

![哈滑塊](./media/howto-hyperscale-high-availability/01-ha-slider.png)

按一下"**保存**"按鈕以應用您的選擇。 啟用 HA 可能需要一些時間，因為伺服器組會提供備用資料庫並將資料流程式傳輸給他們。

伺服器組的 **"概述"** 選項卡將列出所有節點及其備用節點，以及指示是否為每個節點成功啟用 HA**的高可用性**列。

![伺服器組概述中的 ha 列](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>後續步驟

瞭解有關[高可用性的更多資訊](concepts-hyperscale-high-availability.md)。
