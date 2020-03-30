---
title: 按雲簡單刪除 VMware 解決方案的節點 - Azure
description: 瞭解如何使用 CloudSimple 部署從 VMWare 中刪除節點
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024733"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>按雲簡單從 Azure VMware 解決方案中刪除節點

創建雲簡單節點後，將按流量計費。  必須刪除節點以停止節點的計量。  刪除未從 Azure 門戶使用的節點。

## <a name="before-you-begin"></a>開始之前

只有在以下情況下才能刪除節點：

* 刪除使用節點創建的私有雲。  要刪除私有雲，請參閱[通過雲簡單私有雲刪除 Azure VMware 解決方案](delete-private-cloud.md)。
* 通過縮小私有雲，該節點已從私有雲中刪除。  要收縮私有雲，請參閱[按雲簡單私有雲收縮 Azure VMware 解決方案](shrink-private-cloud.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。

## <a name="delete-cloudsimple-node"></a>刪除雲簡單節點

1. 選擇**所有服務**。

2. 搜索**雲簡單節點**。

   ![搜索雲簡單節點](media/create-cloudsimple-node-search.png)

3. 選擇**雲簡單節點**。

4. 選擇要刪除不屬於私有雲的節點。  **私有雲名稱**列顯示節點所屬的私有雲名稱。  如果私有雲不使用節點，則該值將為空。 

    ![選擇雲簡單節點](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> 只能刪除不屬於私有雲的節點。

## <a name="next-steps"></a>後續步驟

* 瞭解[私有雲](cloudsimple-private-cloud.md)
