---
title: 通過 Azure 網站恢復從災害復原排除 Hyper-V VM 磁片到 Azure
description: 如何通過 Azure 網站恢復從複製中排除 Hyper-V VM 磁片。
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 50fb6da2905b2ae27547f25cce3d7a76ca7976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498128"
---
# <a name="exclude-disks-from-replication"></a>從複寫排除磁碟

本文介紹如何在將超 V VM 複製到 Azure 時排除磁片。 您可能希望出於多種原因從複製中排除磁片：

- 確保在排除的磁片上改動的不重要資料不會複製。
- 通過排除不需要複製的磁片，優化消耗的複製頻寬或目標端資源。
- 通過不復制不需要的資料來節省存儲和網路資源。

在從複製中排除磁片之前：

- [深入了解](exclude-disks-replication.md)排除磁碟。
- 查看[典型的排除方案和](exclude-disks-replication.md#typical-scenarios)示例，這些方案和[示例](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)顯示排除磁片如何影響複製、容錯移轉和故障恢復。

## <a name="before-you-start"></a>開始之前

開始之前，請注意下列事項：

- **複製**：預設情況下，將複製電腦上的所有磁片。
- **磁片類型**：
    - 可以從複製中排除基本磁碟。
    - 您無法排除作業系統磁碟。
    - 我們建議不要排除動態磁碟。 網站恢復無法識別來賓 VM 中哪些 VHD 是基本 VHD 或動態的。  如果不排除所有從屬動態磁碟區磁片，受保護的動態磁碟將成為 VM 故障上的故障磁片，並且無法訪問該磁片上的資料。
- **添加/刪除/排除磁片**：啟用複製後，無法添加/刪除/排除磁片進行複製。 如果要添加/刪除或排除磁片，則需要禁用 VM 的保護，然後再次啟用它。
- **容錯移轉**：容錯移轉後，如果容錯移轉應用需要排除磁片才能正常工作，則需要手動創建這些磁片。 或者，您可以將 Azure 自動化集成到恢復計畫中，以在電腦容錯移轉期間創建磁片。
- **容錯移轉**：容錯移轉後故障回本地網站時，在 Azure 中手動創建的磁片不會失敗。 例如，如果故障超過三個磁片，直接在 Azure VM 上創建兩個磁片，則只有三個失敗磁片會失敗回來。 不能包括在故障恢復中手動創建的磁片，也不能包括在 VM 的反向複製中創建的磁片。

## <a name="exclude-disks"></a>排除磁碟

1. 要在為 Hyper-V VM[啟用複製](site-recovery-hyper-v-site-to-azure.md)時排除磁片，在選擇要複製的 VM 後，在 **"啟用複製** > **屬性** > **配置屬性"** 頁中，請查看 **"磁片到複製"** 列。 預設情況下，所有磁片都選擇用於複製。
2. 如果不想複製特定磁片，則在**磁片中複製**清除要排除的任何磁片的選擇。 

    ![從複寫排除磁碟](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>後續步驟
在您的部署設定完成並開始執行之後，請 [深入了解](failover-failback-overview.md) 不同類型的容錯移轉。
