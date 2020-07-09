---
title: 設定高可用性-超大規模資料庫（Citus）-適用於 PostgreSQL 的 Azure 資料庫
description: 如何啟用或停用高可用性
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: 0c7702c8832e22d889a5d785dad845430bfb7d17
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116874"
---
# <a name="configure-hyperscale-citus-high-availability"></a>設定超大規模資料庫（Citus）高可用性

適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）提供高可用性（HA），以避免資料庫停機。 啟用 HA 之後，伺服器群組中的每個節點都會取得待命。 如果原始節點變得狀況不良，則會升級其待命來取代它。

> [!IMPORTANT]
> 因為 HA 會使群組中的伺服器數目加倍，所以成本也會加倍。

在伺服器群組建立期間，或在 Azure 入口網站中伺服器群組的 [**設定**] 索引標籤中，可以啟用 HA。 在任一情況下，使用者介面看起來都很類似。 將 [**高可用性**] 滑杆拖曳至 [是]：

![ha 滑杆](./media/howto-hyperscale-high-availability/01-ha-slider.png)

按一下 [**儲存**] 按鈕以套用您的選取專案。 啟用 HA 可能需要一些時間，因為伺服器群組會布建待命，並將資料串流給它們。

伺服器群組的 [總覽] 索引標籤會列出所有節點及其待命，以及一個 **[** **高可用性**] 資料行，指出是否已針對每個節點成功啟用 HA。

![伺服器群組中的 ha 資料行總覽](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>後續步驟

深入瞭解[高可用性](concepts-hyperscale-high-availability.md)。
