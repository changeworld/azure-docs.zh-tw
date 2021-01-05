---
title: 藉由 CloudSimple 私人雲端來刪除 Azure VMware 解決方案
description: 瞭解如何刪除 CloudSimple 私人雲端。 當您刪除私人雲端時，將會刪除所有叢集。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7db967955dc86db39db4dcb2b3a2baf8906efb20
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97896255"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>刪除 CloudSimple 私人雲端

CloudSimple 可讓您彈性地刪除私人雲端。  私人雲端包含一或多個 vSphere 叢集。 每個叢集可以有3至16個節點。 當您刪除私人雲端時，將會刪除所有叢集。

## <a name="before-you-begin"></a>開始之前

刪除私用雲端會刪除整個私用雲端。  將會刪除私人雲端的所有元件。  如果您想要保留任何資料，請確定您已將資料備份至內部部署儲存體或 Azure 儲存體。

私用雲端的元件包括：

* CloudSimple 節點
* 虛擬機器
* VLAN/子網路
* 私人雲端上儲存的所有使用者資料
* VLAN/子網的所有防火牆規則附件

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="delete-a-private-cloud"></a>刪除私人雲端

1. [存取 CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 開啟 [ **資源** ] 頁面。

3. 按一下您想要刪除的私人雲端

4. 在 [摘要] 頁面上，按一下 [ **刪除**]。

    ![刪除私人雲端](media/delete-private-cloud.png)

5. 在 [確認] 頁面上，輸入私人雲端的名稱，然後按一下 [ **刪除**]。 

    ![刪除私用雲端-確認](media/delete-private-cloud-confirm.png)

私人雲端已標示為要刪除。  刪除進程會在三個小時後啟動，並刪除私人雲端。

> [!CAUTION]
> 刪除私人雲端之後，必須刪除節點。  從您的訂用帳戶中刪除節點之前，節點的計量將會繼續。

## <a name="next-steps"></a>後續步驟

* [刪除節點](delete-nodes.md)
