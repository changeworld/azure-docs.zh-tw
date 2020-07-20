---
title: 透過 CloudSimple 私用雲端來刪除 Azure VMware 解決方案
description: 說明如何刪除 CloudSimple 私用雲端。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024750"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>刪除 CloudSimple 私用雲端

CloudSimple 提供刪除私人雲端的彈性。  私人雲端是由一或多個 vSphere 叢集所組成。 每個叢集可以有3到16個節點。 當您刪除私人雲端時，將會刪除所有的叢集。

## <a name="before-you-begin"></a>開始之前

刪除私人雲端會刪除整個私人雲端。  將會刪除私人雲端的所有元件。  如果您想要保留任何資料，請確定您已將資料備份至內部部署儲存體或 Azure 儲存體。

私用雲端的元件包括：

* CloudSimple 節點
* 虛擬機器
* VLAN/子網路
* 所有儲存在私人雲端上的使用者資料
* VLAN/子網的所有防火牆規則附件

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="delete-a-private-cloud"></a>刪除私人雲端

1. [存取 CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 開啟 [**資源**] 頁面。

3. 按一下您要刪除的私人雲端

4. 在 [摘要] 頁面上，按一下 [**刪除**]。

    ![刪除私人雲端](media/delete-private-cloud.png)

5. 在 [確認] 頁面上，輸入私人雲端的名稱，然後按一下 [**刪除**]。 

    ![刪除私人雲端-確認](media/delete-private-cloud-confirm.png)

私人雲端已標示為要刪除。  刪除程式會在三個小時後啟動，並刪除私人雲端。

> [!CAUTION]
> 刪除私人雲端之後，必須刪除節點。  節點的計量會繼續，直到從您的訂用帳戶刪除節點為止。

## <a name="next-steps"></a>後續步驟

* [刪除節點](delete-nodes.md)
