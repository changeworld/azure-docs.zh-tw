---
title: 使用 Azure Site Recovery 將 VMware VM 磁片從嚴重損壞修復排除到 Azure
description: 如何使用 Azure Site Recovery 排除 VMware VM 磁片，使其不會複寫到 Azure。
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: c4842172ff181b5cdbe7f6fecf69da8755ae43fa
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86129878"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>將磁片從 VMware VM 複寫排除到 Azure

本文說明如何在將 VMware Vm 複寫至 Azure 以進行嚴重損壞修復時，排除磁片。 基於下列原因，您可能會想要從複寫中排除磁片：

- 確保在排除的磁片上變換不重要的資料不會被覆寫。
- 藉由排除不需要複寫的磁片，將耗用的複寫頻寬或目標端資源優化。
- 藉由不要複寫不需要的資料來儲存儲存體和網路資源。

從複寫排除磁片之前：

- [深入了解](exclude-disks-replication.md)排除磁碟。
- 回顧[一般排除案例](exclude-disks-replication.md#typical-scenarios)和[範例](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)，其中顯示如何排除磁片，如何影響複寫、容錯移轉和容錯回復。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

 開始之前，請注意下列事項：

- 複寫 **：根據**預設，機器上的所有磁片都已複寫。
- **磁片類型**：只有基本磁碟可以從複寫中排除。 您無法排除作業系統或動態磁碟。
- **行動服務**：若要排除磁片不進行複寫，您必須先在電腦上手動安裝行動服務，再啟用複寫。 您無法使用推送安裝，因為只有在啟用複寫之後，此方法才會在 VM 上安裝行動服務。  
- **新增/移除/排除磁片**：啟用複寫之後，您將無法新增/移除/排除磁片以進行複寫。 如果您想要新增/移除或排除磁片，您需要停用機器的保護，然後再次啟用它。
- **故障**轉移：容錯移轉之後，如果容錯移轉的應用程式需要排除的磁片才能正常執行，您必須手動建立這些磁片。 或者，您可以將 Azure 自動化整合至復原方案，以在電腦容錯移轉期間建立磁片。
- **容錯回復-Windows**：當您在容錯移轉之後容錯回復至內部部署網站時，您在 Azure 中手動建立的 Windows 磁片不會容錯回復。 例如，如果您故障切換三個磁片，並直接在 Azure Vm 上建立兩個磁片，則只有三個已容錯回復的磁片會容錯回復。
- **容錯回復-linux**機器的容錯回復，您在 Azure 中手動建立的磁片會容錯回復。 例如，如果您故障切換三個磁片，並直接在 Azure Vm 上建立兩個磁片，則所有五個都會容錯回復。 您無法排除在容錯回復或 Vm 重新保護中手動建立的磁片。



## <a name="exclude-disks-from-replication"></a>從複寫排除磁碟

1. 當您[啟用](./hyper-v-azure-tutorial.md)VMware VM 的複寫時，在選取您想要複寫的 vm 之後，請在 [**啟用**複寫內容] [  >  **Properties**  >  **設定屬性**] 頁面中，檢查 [**要**複寫的磁片] 資料行。 預設會選取所有磁片進行複寫。
2. 如果您不想要複寫特定的磁片，請在 [要複寫的**磁片**] 中清除您要排除之任何磁片的選項。 

    ![從複寫排除磁碟](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>後續步驟
在您的部署設定完成並開始執行之後，請 [深入了解](failover-failback-overview.md) 不同類型的容錯移轉。
