---
title: 藉由 CloudSimple 私用雲端來縮減 Azure VMware 解決方案
description: 瞭解如何藉由從現有的 vSphere 叢集中移除節點，或移除整個叢集，以在 CloudSimple 中動態壓縮私用雲端。
author: Ajayan1008
ms.author: v-hborys
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a99b9b56f17b78a98f37d47dcefab26dd9c859de
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899128"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>縮減 CloudSimple 私人雲端

CloudSimple 提供動態壓縮私用雲端的彈性。  私人雲端包含一或多個 vSphere 叢集。 每個叢集可以有3至16個節點。 壓縮私用雲端時，您會從現有的叢集移除節點，或刪除整個叢集。 

## <a name="before-you-begin"></a>開始之前

必須符合下列條件，才能壓縮私用雲端。  管理叢集 (建立私人雲端時所建立的第一個叢集) 無法刪除。

* VSphere 叢集必須有三個節點。  只有三個節點的叢集無法壓縮。
* 已使用的總儲存空間不應超過壓縮之後的總容量。
* 檢查是否有任何分散式資源排程器 (DRS) 規則會防止虛擬機器的 vMotion。  如果有規則，請停用或刪除規則。  DRS 規則包含虛擬機器與主機親和性規則。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="shrink-a-private-cloud"></a>壓縮私人雲端

1. [存取 CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 開啟 [ **資源** ] 頁面。

3. 按一下您想要壓縮的私人雲端

4. 在 [摘要] 頁面上，按一下 [ **壓縮**]。

    ![壓縮私用雲端](media/shrink-private-cloud.png)

5. 選取您要壓縮或刪除的群集。 

    ![壓縮私用雲端-選取叢集](media/shrink-private-cloud-select-cluster.png)

6. 選取 [ **移除一個節點** ] 或 **[刪除整個** 叢集]。 

7. 確認叢集容量

8. 按一下 [ **提交** ] 以縮減私用雲端。

開始壓縮私用雲端。  您可以監視工作中的進度。  壓縮程式可能需要幾小時的時間，視資料而定，需要在 vSAN 上 resynced。

> [!NOTE]
> 1. 如果您藉由刪除資料中心內的最後一個或唯一一個叢集來壓縮私用雲端，將不會刪除資料中心。
> 2. 如果發生任何 DRS 規則違規，節點將不會從叢集中移除，而且工作描述會顯示移除節點將會違反叢集中的 DRS 規則。    


## <a name="next-steps"></a>後續步驟

* [在 Azure 上取用 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 深入了解[私人雲端](cloudsimple-private-cloud.md)
