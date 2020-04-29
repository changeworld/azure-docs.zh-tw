---
title: CloudSimple 的 VMware 解決方案刪除節點-Azure
description: 瞭解如何使用 CloudSimple 部署刪除 VMWare 中的節點
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024733"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>從 Azure VMware Solution by CloudSimple 刪除節點

CloudSimple 節點一旦建立，就會計量。  必須刪除節點，才能停止計量節點。  您會刪除不是從 Azure 入口網站使用的節點。

## <a name="before-you-begin"></a>開始之前

只有在下列情況下，才可以刪除節點：

* 已刪除使用節點所建立的私用雲端。  若要刪除私人雲端，請參閱[CloudSimple 私用雲端來刪除 Azure VMware 解決方案](delete-private-cloud.md)。
* 已藉由壓縮私人雲端，從私人雲端移除節點。  若要壓縮私人雲端，請參閱透過[CloudSimple 私用雲端縮小 Azure VMware 解決方案](shrink-private-cloud.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="delete-cloudsimple-node"></a>刪除 CloudSimple 節點

1. 選取 [所有服務]  。

2. 搜尋**CloudSimple 節點**。

   ![搜尋 CloudSimple 節點](media/create-cloudsimple-node-search.png)

3. 選取 [ **CloudSimple 節點**]。

4. 選取不屬於私人雲端以刪除的節點。  [**私人雲端名稱**] 資料行顯示節點所屬的私人雲端名稱。  如果私人雲端未使用節點，此值將會是空的。 

    ![選取 CloudSimple 節點](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> 只能刪除不屬於私人雲端的節點。

## <a name="next-steps"></a>後續步驟

* 瞭解[私用雲端](cloudsimple-private-cloud.md)
