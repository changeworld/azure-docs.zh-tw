---
title: 壓縮 Azure VMware 解決方案（AVS）私人雲端
description: 說明如何壓縮 AVS 私用雲端。
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0ea764081cd0b4d5c6d44cd7364d1e9a89a3cec3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014261"
---
# <a name="shrink-an-avs-private-cloud"></a>壓縮 AVS 私用雲端

AVS 提供動態壓縮 AVS 私用雲端的彈性。 AVS 私用雲端是由一或多個 vSphere 叢集所組成。 每個叢集可以有3到16個節點。 壓縮 AVS 私用雲端時，您可以從現有的叢集中移除節點，或刪除整個叢集。 

## <a name="before-you-begin"></a>開始之前

在壓縮 AVS 私人雲端之前，必須符合下列條件。 建立 AVS 私用雲端時，會建立管理叢集（第一個叢集）。 無法刪除。

* VSphere 叢集必須有三個節點。 只有三個節點的叢集無法壓縮。
* 在壓縮叢集之後，使用的總儲存體不應超過總容量。
* 檢查是否有任何分散式資源排程器（DRS）規則會阻止虛擬機器的 vMotion。 如果有規則存在，請停用或刪除規則。 DRS 規則包含虛擬機器以裝載相似性規則。


## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="shrinking-an-avs-private-cloud"></a>壓縮 AVS 私用雲端

1. [存取 AVS 入口網站](access-cloudsimple-portal.md)。

2. 開啟 [**資源**] 頁面。

3. 按一下您要壓縮的 AVS 私人雲端

4. 在 [摘要] 頁面上，按一下 [**壓縮**]。

    ![壓縮 AVS 私用雲端](media/shrink-private-cloud.png)

5. 選取您想要壓縮或刪除的叢集。 

    ![壓縮 AVS 私用雲端-選取叢集](media/shrink-private-cloud-select-cluster.png)

6. 選取 [**移除一個節點**] 或 **[刪除整個**叢集]。 

7. 驗證叢集容量

8. 按一下 [**提交**] 以壓縮 AVS 私用雲端。

已開始壓縮 AVS 私用雲端。 您可以在 [工作] 中監視進度。 壓縮程式可能需要幾個小時的時間，視資料而定，這需要在 vSAN 上 resynced。

> [!NOTE]
> 1. 如果您藉由刪除資料中心內的最後一個或唯一的叢集來縮小私人雲端，資料中心將不會被刪除。
> 2. 如果發生任何 DRS 規則違規，節點將不會從叢集移除，而且工作描述會顯示移除節點會違反叢集上的 DRS 規則。    


## <a name="next-steps"></a>後續步驟

* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 深入瞭解[AVS 私人](cloudsimple-private-cloud.md)雲端
