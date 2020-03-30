---
title: 通過 Azure 網站恢復從災害復原排除 VMware VM 磁片到 Azure
description: 如何通過 Azure 網站恢復將 VMware VM 磁片從複製到 Azure 中排除。
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: cd54da5ee01206e576157435135065189bfb8035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495363"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>從 VMware VM 複製到 Azure 的磁片

本文介紹如何在將 VMware VM 複製到 Azure 以進行災害復原時排除磁片。 您可能希望出於多種原因從複製中排除磁片：

- 確保在排除的磁片上改動的不重要資料不會複製。
- 通過排除不需要複製的磁片，優化消耗的複製頻寬或目標端資源。
- 通過不復制不需要的資料來節省存儲和網路資源。

在從複製中排除磁片之前：

- [深入了解](exclude-disks-replication.md)排除磁碟。
- 查看[典型的排除方案和](exclude-disks-replication.md#typical-scenarios)示例，這些方案和[示例](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)顯示排除磁片如何影響複製、容錯移轉和故障恢復。

## <a name="before-you-start"></a>開始之前

 開始之前，請注意下列事項：

- **複製**：預設情況下，將複製電腦上的所有磁片。
- **磁片類型**：只能從複製中排除基本磁碟。 您無法排除作業系統或動態磁碟。
- **移動服務**：要從複製中排除磁片，必須先在啟用複製之前在電腦上手動安裝移動服務。 不能使用推送安裝，因為此方法僅在啟用複製後在 VM 上安裝移動服務。  
- **添加/刪除/排除磁片**：啟用複製後，無法添加/刪除/排除磁片進行複製。 如果要添加/刪除或排除磁片，則需要禁用對電腦的保護，然後再次啟用它。
- **容錯移轉**：容錯移轉後，如果容錯移轉應用需要排除磁片才能正常工作，則需要手動創建這些磁片。 或者，您可以將 Azure 自動化集成到恢復計畫中，以在電腦容錯移轉期間創建磁片。
- **故障倒回 Windows**：容錯移轉後故障回本地網站時，在 Azure 中手動創建的 Windows 磁片不會失敗。 例如，如果故障超過三個磁片，直接在 Azure VM 上創建兩個磁片，則只有容錯移轉的三個磁片將失敗。
- **故障倒退-Linux：** 對於 Linux 電腦的故障倒回，您在 Azure 中手動創建的磁片將失敗。 例如，如果故障超過三個磁片，並直接在 Azure VM 上創建兩個磁片，則所有五個磁片都將失敗。 不能排除在故障恢復或重新保護 VM 中手動創建的磁片。



## <a name="exclude-disks-from-replication"></a>從複寫排除磁碟

1. 當您為 VMware VM[啟用複製](site-recovery-hyper-v-site-to-azure.md)時，在選擇要複製的 VM 後，在 **"啟用複製** > **屬性** > **配置屬性屬性"** 頁中，查看 **"磁片到複製"** 列。 預設情況下，所有磁片都選擇用於複製。
2. 如果不想複製特定磁片，則在**磁片中複製**清除要排除的任何磁片的選擇。 

    ![從複寫排除磁碟](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>後續步驟
在您的部署設定完成並開始執行之後，請 [深入了解](failover-failback-overview.md) 不同類型的容錯移轉。
