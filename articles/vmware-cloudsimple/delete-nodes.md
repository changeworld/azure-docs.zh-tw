---
title: 依 CloudSimple 刪除 VMware 解決方案的節點-Azure
description: 瞭解如何使用 CloudSimple 部署從 VMWare 刪除節點。 CloudSimple 節點會進行計量。 刪除 Azure 入口網站不使用的節點。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 569bc6350b1bfa01228d49d28a1d12e2ab62f6f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142259"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>從 Azure VMware Solution by CloudSimple 刪除節點

CloudSimple 節點會在建立之後進行計量。  必須刪除節點，才能停止計量節點。  您可以從 Azure 入口網站中刪除不使用的節點。

## <a name="before-you-begin"></a>開始之前

只有在下列情況下，才可以刪除節點：

* 已刪除使用節點建立的私人雲端。  若要刪除私人雲端，請參閱 [CloudSimple 私人雲端刪除 Azure VMware 解決方案](delete-private-cloud.md)。
* 已藉由壓縮私用雲端將節點從私人雲端中移除。  若要壓縮私用雲端，請參閱 [CloudSimple 私人雲端來縮減 Azure VMware Solution](shrink-private-cloud.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="delete-cloudsimple-node"></a>刪除 CloudSimple 節點

1. 選取 [所有服務]。

2. 搜尋 **CloudSimple 節點**。

   ![搜尋 CloudSimple 節點](media/create-cloudsimple-node-search.png)

3. 選取 [ **CloudSimple 節點**]。

4. 選取不屬於私人雲端的節點來刪除。  [**私用雲端名稱**] 欄顯示節點所屬的私用雲端名稱。  如果私人雲端未使用節點，此值將會是空的。 

    ![選取 CloudSimple 節點](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> 您只能刪除不屬於私用雲端的節點。

## <a name="next-steps"></a>後續步驟

* 瞭解 [私用雲端](cloudsimple-private-cloud.md)
