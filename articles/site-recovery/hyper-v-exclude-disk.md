---
title: 使用 Azure Site Recovery 將 Hyper-v VM 磁片從嚴重損壞修復排除至 Azure
description: 如何使用 Azure Site Recovery 排除 Hyper-v VM 磁片，使其不會複寫至 Azure。
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 50fb6da2905b2ae27547f25cce3d7a76ca7976b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75498128"
---
# <a name="exclude-disks-from-replication"></a>從複寫排除磁碟

本文說明如何在將 Hyper-v Vm 複寫至 Azure 時排除磁片。 基於下列原因，您可能會想要從複寫中排除磁片：

- 確保在排除的磁片上變換不重要的資料不會被覆寫。
- 藉由排除不需要複寫的磁片，將耗用的複寫頻寬或目標端資源優化。
- 藉由不要複寫不需要的資料來儲存儲存體和網路資源。

從複寫排除磁片之前：

- [深入了解](exclude-disks-replication.md)排除磁碟。
- 回顧[一般排除案例](exclude-disks-replication.md#typical-scenarios)和[範例](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)，其中顯示如何排除磁片，如何影響複寫、容錯移轉和容錯回復。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

開始之前，請注意下列事項：

- 複寫 **：根據**預設，機器上的所有磁片都已複寫。
- **磁片類型**：
    - 您可從複寫排除基本磁碟。
    - 您無法排除作業系統磁碟。
    - 我們建議不要排除動態磁碟。 Site Recovery 無法識別來賓 VM 中的哪個 VHD 是基本或動態的。  如果您不排除所有的相依動態磁碟區磁片，受保護的動態磁碟會在故障的 VM 上變成失敗的磁片，而且該磁片上的資料無法存取。
- **新增/移除/排除磁片**：啟用複寫之後，您將無法新增/移除/排除磁片以進行複寫。 如果您想要新增/移除或排除磁片，您需要停用 VM 的保護，然後再次啟用它。
- **故障**轉移：容錯移轉之後，如果容錯移轉的應用程式需要排除磁片才能正常執行，您必須手動建立這些磁片。 或者，您可以將 Azure 自動化整合至復原方案，以在電腦容錯移轉期間建立磁片。
- 容錯**回復**：當您容錯移轉之後，您在 Azure 中手動建立的磁片不會容錯回復至內部部署網站。 例如，如果您故障切換三個磁片，並直接在 Azure VM 上建立兩個磁片，則只有三個已故障轉的磁片會容錯回復。 您不能包含在容錯回復或反向複寫 Vm 中手動建立的磁片。

## <a name="exclude-disks"></a>排除磁碟

1. 若要在為 hyper-v VM[啟用](site-recovery-hyper-v-site-to-azure.md)複寫時排除磁片，請在選取您想要複寫的 vm 之後，在 [**啟用**複寫內容] [  >  **Properties**  >  **設定屬性**] 頁面中，檢查 [**要**複寫的磁片] 資料行。 預設會選取所有磁片進行複寫。
2. 如果您不想要複寫特定的磁片，請在 [要複寫的**磁片**] 中清除您要排除之任何磁片的選項。 

    ![從複寫排除磁碟](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>後續步驟
在您的部署設定完成並開始執行之後，請 [深入了解](failover-failback-overview.md) 不同類型的容錯移轉。
