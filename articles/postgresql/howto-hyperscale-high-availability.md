---
title: 設定高可用性-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 如何啟用或停用高可用性
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 46b842994cbcf7efe66d5992c79246d77626e268
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907393"
---
# <a name="configure-hyperscale-citus-high-availability"></a>設定超大規模 (Citus) 高可用性

適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 提供高可用性 (HA) ，以避免資料庫停機。 啟用 HA 之後，伺服器群組中的每個節點都會取得待命。 如果原始節點變成狀況不良，則會將其待命升級以取代。

> [!IMPORTANT]
> 由於 HA 會將群組中的伺服器數目加倍，因此也會加倍成本。

在伺服器群組建立期間，或之後在 Azure 入口網站的伺服器群組的 [ **計算 + 儲存體** ] 索引標籤中啟用 HA 是可行的。 在上述任一情況下，使用者介面看起來都很類似。 將 [ **高可用性** ] 的滑杆從 [否] 拖曳至 [是]：

:::image type="content" source="./media/howto-hyperscale-high-availability/01-ha-slider.png" alt-text="ha 滑杆":::

按一下 [ **儲存** ] 按鈕以套用您的選取專案。 啟用 HA 可能需要一些時間，因為伺服器群組會布建待命，並將資料串流至這些資料。

伺服器群組的 [ **總覽** ] 索引標籤會列出所有節點及其待命，以及一個 [ **高可用性** ] 資料行，指出是否已成功為每個節點啟用 HA。

:::image type="content" source="./media/howto-hyperscale-high-availability/02-ha-column.png" alt-text="伺服器群組中的 ha 資料行總覽":::

### <a name="next-steps"></a>下一步

深入瞭解 [高可用性](concepts-hyperscale-high-availability.md)。
