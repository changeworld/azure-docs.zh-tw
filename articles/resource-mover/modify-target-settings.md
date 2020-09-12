---
title: 使用 Azure 資源移動器在區域之間移動 Azure Vm 時修改目標設定
description: 瞭解如何在使用 Azure 資源移動器的區域之間移動 Azure Vm 時修改目標設定。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: c58fdd38c4221c03778b2c769620bf7911aaded8
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670323"
---
# <a name="modify-target-settings"></a>修改目標設定

本文說明如何在 Azure 區域之間使用 [Azure 資源移動器](overview.md)移動資源時，修改目標設定。


## <a name="modify-vm-settings"></a>修改 VM 設定

移動 Azure Vm 和相關聯的資源時，您可以修改目標設定。 

- 建議您只在驗證移動集合之後變更目標設定。
- 建議您在準備資源之前先修改設定，因為在完成準備之後，某些目標屬性可能無法進行編輯。

但是：
- 如果您要移動來源資源，您通常可以修改目標設定，直到您啟動起始移動進程為止。
- 如果您在來源區域中指派現有的資源，您可以修改目標設定，直到移動認可完成為止。

### <a name="settings-you-can-modify"></a>您可以修改的設定

資料表中摘要說明您可以修改的設定。

**資源** | **選項** 
--- | --- | --- 
**VM 名稱** | 選項：<br/><br/> -在目的地區域中建立具有相同名稱的新 VM。<br/><br/> -使用不同的名稱在目的地區域中建立新的 VM。<br/><br/> -使用目的地區域中的現有 VM。<br/><br/> 如果您建立新的 VM，但您所修改的設定除外，則會將新的目標 VM 指派為與來源相同的設定。
**VM 可用性區域** | 將放置目標 VM 的可用性區域。 如果您不想要變更來源設定，或不想將 VM 放在可用性區域中，則可以將此標示為 **NA** 。
**VM SKU** | [VM 類型](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (可用於目標 vm) 的目的地區域中。<br/><br/> 選取的目標 VM 不應小於來源 VM。
**網路資源** | 虛擬網路 (Vnet) /network 安全性群組/網路介面的選項：<br/><br/> -在目的地區域中建立具有相同名稱的新資源。<br/><br/> -在目的地區域中建立具有不同名稱的新資源。<br/><br/> -使用目的地區域中的現有網路資源。<br/><br/> 如果您建立新的目標資源，但您所修改的設定除外，則會將相同的設定指派為來源資源。
**公用 IP 位址名稱** | 指定名稱。
**公用 IP 位址 SKU** | 指定 [SKU](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku)。
**公用 IP 位址區域** | 指定標準公用 IP 位址的 [區域](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#standard) 。<br/><br/> 如果您想要讓它成為區域冗余，請輸入為 **區域**。
**負載平衡器名稱** | 指定名稱。
**負載平衡器 SKU** | 基本或標準。 我們建議使用 Standard。
**負載平衡器區域** | 指定負載平衡器的區域。 <br/><br/> 如果您想要讓它成為區域冗余，請輸入為 **區域**。
**資源相依性** | 每個相依性的選項：<br/><br/>-資源會使用將移至目的地區域的來源相依資源。<br/><br/> -資源使用位於目的地區域的不同相依資源。 在此情況下，您可以選擇目的地區域中的任何類似資源。

### <a name="edit-vm-target-settings"></a>編輯 VM 目標設定

如果您不想要將來源區域的資源相依于目標，您有幾個其他選項：

- 在目的地區域中建立新的資源。 除非您指定不同的設定，否則新的資源將會具有與來源資源相同的設定。
- 使用目的地區域中的現有資源。

確切行為取決於資源類型。 [深入瞭解](modify-target-settings.md) 如何修改目標設定。

您可以使用資源移動集合中的 **目標** 設定專案來修改資源的目標設定。 

若要修改設定： 

1. 在 [ **跨區域** ] 頁面 > **目標設定** 資料行] 中，按一下資源專案的連結。
2. 在 [ **設定] 設定**中，您可以在目的地區域中建立新的 VM。
3. 將新的可用性區域、可用性設定組或 SKU 指派給目標 VM。 **可用性區域** 和 **SKU**。

只會針對您正在編輯的資源進行變更。 您必須個別更新任何相依的資源。


## <a name="modify-sql-settings"></a>修改 SQL 設定

移動 Azure SQL Database 資源時，您可以修改移動的目標設定。 

- 針對 SQL database：
    - 建議您先修改目標設定設定，再準備它們進行移動。
    - 您可以修改目標資料庫的設定，以及資料庫的區域冗余。
- 針對彈性集區：
    -  您可以在起始移動之前隨時修改目標設定。
    - 您可以針對集區修改目標彈性集區和區域冗余。 

### <a name="sql-settings-you-can-modify"></a>您可以修改的 SQL 設定

**設定** | **SQL database** | **彈性集區**
--- | --- | ---
**名稱** | 在目的地區域中建立具有相同名稱的新資料庫。<br/><br/> 使用不同的名稱在目的地區域中建立新的資料庫。<br/><br/> 使用目的地區域中的現有資料庫。 | 在目的地區域中建立具有相同名稱的新彈性集區。<br/><br/> 使用不同的名稱在目的地區域中建立新的彈性集區。<br/><br/> 使用目的地區域中的現有彈性集區。
**區域備援** | 若要從支援區域冗余的區域移至沒有的區域，請在 [區域] 設定中輸入 **Disable** 。<br/><br/> 若要從不支援區域冗余的區域移至該區域，請在 [區域] 設定中輸入 [ **啟用** ]。 | 若要從支援區域冗余的區域移至沒有的區域，請在 [區域] 設定中輸入 **Disable** 。<br/><br/> 若要從不支援區域冗余的區域移至該區域，請在 [區域] 設定中輸入 [ **啟用** ]。

### <a name="edit-sql-target-settings"></a>編輯 SQL 目標設定

您可以修改 Azure SQL Database 資源的目標設定，如下所示： 

1. 在 **跨區域**，針對您想要修改的資源，按一下 **目標設定** 專案。
2. 在 [ **設定設定**] 中，指定上表中摘要說明的目標設定。

## <a name="next-steps"></a>接下來的步驟

[將 AZURE VM 移](tutorial-move-region-virtual-machines.md) 至另一個區域。
