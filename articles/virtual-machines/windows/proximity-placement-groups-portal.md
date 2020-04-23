---
title: 使用入口網站建立鄰近放置群組
description: 瞭解如何使用 Azure 入口網站建立鄰近放置群組。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: aaecfbd14289840e795c6323737877e267586e16
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098640"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>使用入口網站建立鄰近放置群組

若要盡可能接近 Vm，達到最低可能的延遲，您應該將它們部署在[鄰近放置群組](co-location.md#proximity-placement-groups)內。

鄰近放置群組是一種邏輯群組，用來確保 Azure 計算資源實際位於彼此接近的位置。 鄰近放置群組適用于需要低延遲的工作負載。


## <a name="create-the-proximity-placement-group"></a>建立鄰近位置群組

1. 在搜尋中輸入**鄰近位置群組**。
1. 在搜尋結果中的 [**服務**] 底下，選取 [**鄰近放置群組**]。
1. 在 [**鄰近放置群組**] 頁面中，選取 [**新增**]。
1. 在 [基本]**** 索引標籤中的 [專案詳細資料]**** 底下，確認已選取正確的訂用帳戶。
1. 在 [**資源群組**] 中，選取 **[新建] 以建立**新的群組，或從下拉式選單選取現有的資源群組。
1. 在 [**區域**] 中，選取您想要建立鄰近放置群組的位置。
1. 在 [**鄰近位置組名**] 中輸入名稱，然後選取 [**審核 + 建立**]。
1. 通過驗證之後，請選取 [**建立**] 以建立鄰近位置群組。

    ![建立近接位置群組的螢幕擷取畫面](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>建立 VM

1. 在入口網站中建立 VM 時，請移至 [ **Advanced** ] 索引標籤。 
1. 在**鄰近放置群組**選取專案中，選取正確的放置群組。 

    ![在入口網站中建立新的 VM 時的鄰近放置群組區段的螢幕擷取畫面](./media/ppg/vm-ppg.png)

1. 當您完成所有其他必要的選取專案時，請選取 [**審查 + 建立**]。
1. 通過驗證之後，請選取 [**建立**] 以在放置群組中部署 VM。




## <a name="next-steps"></a>後續步驟

您也可以使用[Azure PowerShell](proximity-placement-groups.md)來建立鄰近放置群組。

