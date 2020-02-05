---
title: 刪除 VMware 解決方案（AVS）的節點-Azure
description: 瞭解如何從您的 VMWare 使用 AVS 部署刪除節點
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024733"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>從 Azure VMware Solution by AVS 刪除節點

AVS 節點一旦建立，就會進行計量。 必須刪除節點，才能停止計量節點。 您會刪除不是從 Azure 入口網站使用的節點。

## <a name="before-you-begin"></a>開始之前

只有在下列情況下，才可以刪除節點：

* 系統會刪除以節點建立的 AVS 私用雲端。 若要刪除 AVS 私人雲端，請參閱透過[AVS 私人雲端刪除 Azure VMware 解決方案](delete-private-cloud.md)。
* 已藉由壓縮 AVS 私人雲端，從 AVS 私人雲端移除該節點。 若要壓縮 AVS 私用雲端，請參閱透過[AVS 私用雲端壓縮 Azure VMware 解決方案](shrink-private-cloud.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="delete-avs-node"></a>刪除 AVS 節點

1. 選取 [所有服務]。

2. 搜尋**AVS 節點**。

   ![搜尋 AVS 節點](media/create-cloudsimple-node-search.png)

3. 選取 [ **AVS 節點**]。

4. 選取不屬於要刪除之 AVS 私人雲端的節點。 [ **AVS 私人雲端名稱**] 欄顯示節點所屬的 AVS 私用雲端名稱。 如果 AVS 私人雲端未使用節點，此值將會是空的。 

    ![選取 AVS 節點](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> 只能刪除不屬於 AVS 私人雲端的節點。

## <a name="next-steps"></a>後續步驟

* 瞭解[AVS 私用雲端](cloudsimple-private-cloud.md)
