---
title: 通過雲簡單私有雲刪除 Azure VMware 解決方案
description: 描述如何刪除雲簡單私有雲。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024750"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>刪除雲簡單私有雲

雲簡單提供了刪除私有雲的靈活性。  私有雲由一個或多個 vSphere 群集組成。 每個群集可以有 3 到 16 個節點。 刪除私有雲時，將刪除所有群集。

## <a name="before-you-begin"></a>開始之前

刪除私有雲將刪除整個私有雲。  私有雲的所有元件都將被刪除。  如果要保留任何資料，請確保將資料備份到本機存放區或 Azure 存儲。

私有雲的元件包括：

* 雲簡單節點
* 虛擬機器
* VLAN/子網路
* 存儲在私有雲上的所有使用者資料
* VLAN/子網的所有防火牆規則附件

## <a name="sign-in-to-azure"></a>登入 Azure

登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。

## <a name="delete-a-private-cloud"></a>刪除私人雲端

1. [訪問雲簡單門戶](access-cloudsimple-portal.md)。

2. 打開 **"資源"** 頁。

3. 按一下要刪除的私有雲

4. 在摘要頁上，按一下"**刪除**"。

    ![刪除私有雲](media/delete-private-cloud.png)

5. 在確認頁面上，輸入私有雲的名稱，然後按一下"**刪除**"。 

    ![刪除私有雲 - 確認](media/delete-private-cloud-confirm.png)

私有雲標記為刪除。  刪除過程在三小時後開始，並刪除私有雲。

> [!CAUTION]
> 刪除私有雲後必須刪除節點。  節點的計數將繼續，直到從訂閱中刪除節點。

## <a name="next-steps"></a>後續步驟

* [刪除節點](delete-nodes.md)
