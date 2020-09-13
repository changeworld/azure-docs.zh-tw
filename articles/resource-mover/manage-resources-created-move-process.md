---
title: 管理在 Azure 資源移動器中的 VM 移動程式期間建立的資源
description: 瞭解如何在 Azure 資源移動器中管理 VM 移動程式期間建立的資源
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 4ff1905eb164d004af69ce5b0df3278bf3a46884
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670321"
---
# <a name="manage-resources-created-for-the-vm-move"></a>管理為 VM 移動建立的資源

本文說明如何管理 [Azure 資源移動器](overview.md) 明確建立的資源，以協助 VM 移動程式。 

跨區域移動 Vm 之後，資源移動器所建立的一些資源應該手動清除。

## <a name="delete-resources-created-for-vm-move"></a>刪除為 VM 移動建立的資源

手動刪除移動集合，並 Site Recovery 為 VM 移動建立的資源。

1. 在您移動 Vm 的來源區域中，查看資源群組中的資源 ```RegionMoveRG-<sourceregion>-<target-region>``` 。
2. 檢查 VM 和移動集合中的所有其他來源資源是否已移動/刪除。 這可確保沒有任何擱置中的資源正在使用它們。
2. 刪除這些資源。

    - 移動集合名稱為 ```movecollection-<sourceregion>-<target-region>``` 。
    - 快取儲存體帳戶名稱為 ```resmovecache<guid>```
    - 保存庫名稱為 ```ResourceMove-<sourceregion>-<target-region>-GUID``` 。

## <a name="next-steps"></a>接下來的步驟

請嘗試使用資源移動器將 [VM 移](tutorial-move-region-virtual-machines.md) 至另一個區域。