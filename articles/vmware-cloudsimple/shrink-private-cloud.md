---
title: 按雲簡單私有雲收縮 Azure VMware 解決方案
description: 描述如何收縮雲簡單私有雲。
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 602dca105e91c55c591388a833a36e71f951da8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014261"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>縮小雲簡單私有雲

CloudSimple 提供了動態收縮私有雲的靈活性。  私有雲由一個或多個 vSphere 群集組成。 每個群集可以有 3 到 16 個節點。 收縮私有雲時，將從現有群集中刪除節點或刪除整個群集。 

## <a name="before-you-begin"></a>開始之前

縮小私有雲必須滿足以下條件。  無法刪除創建私有雲時創建的管理群集（第一個群集）。

* vSphere 群集必須具有三個節點。  只能具有三個節點的群集無法收縮。
* 群集收縮後，消耗的總存儲容量不應超過總容量。
* 檢查任何分散式資源計劃程式 （DRS） 規則是否阻止虛擬機器的 vMotion。  如果存在規則，請禁用或刪除這些規則。  DRS 規則包括用於承載關聯規則的虛擬機器。

## <a name="sign-in-to-azure"></a>登入 Azure

登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。

## <a name="shrink-a-private-cloud"></a>壓縮私人雲端

1. [訪問雲簡單門戶](access-cloudsimple-portal.md)。

2. 打開 **"資源"** 頁。

3. 按一下要縮小的私有雲

4. 在摘要頁上，按一下 **"收縮**"。

    ![收縮私有雲](media/shrink-private-cloud.png)

5. 選擇要收縮或刪除的群集。 

    ![收縮私有雲 - 選擇群集](media/shrink-private-cloud-select-cluster.png)

6. 選擇**刪除一個節點**或刪除**整個群集**。 

7. 驗證群集容量

8. 按一下"**提交**"以縮小私有雲。

私有雲的收縮開始。  您可以監視任務的進度。  收縮過程可能需要幾個小時，具體取決於資料，需要在 vSAN 上重新同步。

> [!NOTE]
> 1. 如果通過刪除資料中心中的最後一個或唯一的群集來縮小私有雲，則不會刪除資料中心。
> 2. 如果發生任何 DRS 規則衝突，將不會從群集中刪除節點，並且任務說明顯示刪除節點將違反群集上的 DRS 規則。    


## <a name="next-steps"></a>後續步驟

* [在 Azure 上取用 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 瞭解有關[私有雲](cloudsimple-private-cloud.md)的更多
