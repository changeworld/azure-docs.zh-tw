---
title: 使用門戶創建接近放置組
description: 瞭解如何使用 Azure 門戶創建鄰近放置組。
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73180247"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>使用門戶創建接近放置組

要盡可能接近 VM，實現盡可能低的延遲，應將它們部署在[接近放置組中](co-location.md#proximity-placement-groups)。

鄰近放置組是一種邏輯分組，用於確保 Azure 計算資源物理上彼此靠近。 接近放置組對於需要低延遲的工作負載非常有用。


## <a name="create-the-proximity-placement-group"></a>創建鄰近放置組

1. 在搜索中鍵入**接近放置組**。
1. 在搜尋結果中的 **"服務**"下，選擇 **"鄰近"放置組**。
1. 在 **"鄰近放置組"** 頁中，選擇 **"添加**"。
1. 在 [基本]**** 索引標籤中的 [專案詳細資料]**** 底下，確認已選取正確的訂用帳戶。
1. 在 **"資源"組中**，選擇 **"新建**"以創建新組，或從下拉清單中選擇現有資源組。
1. 在 **"區域"** 中選擇要創建鄰近放置組的位置。
1. 在 **"鄰近放置"組名稱**中鍵入名稱，然後選擇 **"審閱 + 創建**"。
1. 驗證通過後，選擇 **"創建**"以創建鄰近放置組。

    ![創建鄰近放置組的螢幕截圖](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>建立 VM

1. 在門戶中創建 VM 時，轉到 **"高級"** 選項卡。 
1. 在 **"鄰近放置組**"選擇中，選擇正確的放置組。 

    ![在門戶中創建新 VM 時的臨近放置組部分的螢幕截圖](./media/ppg/vm-ppg.png)

1. 完成所有其他必需選擇後，選擇 **"審閱 + 創建**"。
1. 通過驗證後，選擇 **"創建"** 以在放置組中部署 VM。




## <a name="next-steps"></a>後續步驟

您還可以使用[Azure PowerShell](proximity-placement-groups.md)創建鄰近放置組。

