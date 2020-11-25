---
title: Azure 立即還原功能
description: Azure 立即還原功能和 VM 備份堆疊、Azure Resource Manager 部署模型的常見問題集
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 147fadc92429157ed2f9ba3eb68297a3e1d08d24
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "96014443"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>透過 Azure 備份的立即還原功能取得改良的備份和還原效能

> [!NOTE]
> 根據使用者的意見反應，我們已將 **VM 備份堆疊 V2** 重新命名為「 **立即還原** 」，以減少與 Azure stack 功能的混淆。
> 所有 Azure 備份使用者現在已升級為立即 **還原**。

「立即還原」的新模型提供下列增強功能：

* 能夠使用在備份作業中所建立的快照集，以供復原，而不需要等待將資料傳輸到保存庫完成。 如此可減少觸發還原之前，將快照集複製到保存庫的等候時間。
* 藉由本機預設保留 2 天的快照集，可減少備份和還原時間。 此預設快照集保留值可設定為介於1到5天之間的任何值。
* 最多可支援 32 TB 的磁片大小。 Azure 備份不建議將磁片調整大小。
* 支援標準 SSD 磁片以及標準 HDD 磁片和進階 SSD 磁片。
* 在還原時，能夠使用每個磁片)  (非受控 Vm 的原始儲存體帳戶。 即使 VM 的磁碟分散於多個儲存體帳戶，仍然具有此功能。 它能夠針對各種不同的 VM 設定，加快還原作業的速度。
* 針對在儲存體帳戶中使用非受控 premium 磁片的 Vm 備份，透過立即還原，建議您配置配置的總儲存空間（**僅限** 第一次備份所需的 *50%* 可用空間）。 50% 可用空間不是第一次備份完成後的備份需求。

## <a name="whats-new-in-this-feature"></a>這項功能有什麼新增功能？

目前的備份作業包含兩個階段：

1. 建立 VM 快照集。
2. 將 VM 快照集傳輸到 Azure 復原服務保存庫。

復原點只會在第 1 和第 2 階段完成之後才建立。 在此升級中，完成快照集時就會建立復原點，而且快照集類型的此復原點可用來執行還原 (使用相同的還原流程)。 您可以使用「快照集」作為復原點類型，並在快照集傳輸至保存庫之後，在 Azure 入口網站中識別此復原點，復原點類型會變更為「快照集與保存庫」。

![VM 備份堆疊 Resource Manager 部署模型的備份作業 -- 儲存體與保存庫](./media/backup-azure-vms/instant-rp-flow.png)

根據預設，快照集會保留兩天。 這項功能可讓您減少還原時間，以便從這些快照集進行還原作業。 它可減少轉換並從保存庫複製資料所需的時間。

## <a name="feature-considerations"></a>功能考量

* 快照集會儲存在磁碟上，以便建立復原點及加快還原作業的速度。 因此，您將看見儲存體成本會隨著在這段期間內取得的快照集而增減。
* 增量快照會儲存為分頁 Blob。 使用非受控磁碟的所有客戶，需對儲存在其本機儲存體帳戶中的快照集付費。 由於受控 VM 備份所使用的還原點集合會在基礎儲存體層級使用 blob 快照集，因此針對受控磁片，您會看到對應于 blob 快照集定價的成本，而且它們是累加的。
* 對於進階儲存體帳戶，針對立即復原點取得的快照集會計入配置空間的 10 TB 限制。
* 您可以根據還原需求來設定快照集保留期。 視需求而定，您可以在 [備份原則] 窗格中將快照集保留設定為最少一天，如下所述。 如果您沒有經常執行還原，這將有助於節省快照集保留成本。
* 這是單向升級。 一旦升級為立即還原，您就無法返回。

>[!NOTE]
>如果升級此立即還原，所有客戶的快照集保留持續時間 (**同時包含新的和現有的**) 會設定為預設值兩天。 不過，您可以根據您的需求，將持續時間設定為介於1到5天之間的任何值。

## <a name="cost-impact"></a>成本影響

增量快照集會儲存在 VM 的儲存體帳戶中，以用於立即復原。 增量快照集表示快照集所佔用的空間等於建立快照集後寫入的頁面所佔用的空間。 針對快照集所佔用的每 GB 已使用空間，計費仍會是相同的，而且每 GB 的價格與 [定價頁面](https://azure.microsoft.com/pricing/details/managed-disks/)上所述的價格相同。 針對使用非受控磁片的 Vm，您可以在每個磁片的 VHD 檔案的功能表中看到快照集。 針對受控磁片，快照集會儲存在指定資源群組中的還原點集合資源中，而快照本身則不會直接顯示。

>[!NOTE]
> 每週原則的快照集保留期固定為5天。

## <a name="configure-snapshot-retention"></a>設定快照集保留期

### <a name="using-azure-portal"></a>使用 Azure 入口網站

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

在 Azure 入口網站中，您可以在 [**立即還原**] 區段下的 [ **VM 備份原則**] 窗格中看到新增的欄位。 您可以針對與特定備份原則相關聯的所有 Vm，從 **VM 備份原則** 窗格變更快照集保留持續時間。

![立即還原功能](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>使用 PowerShell

>[!NOTE]
> 從 Az PowerShell version 1.6.0 之後，您可以使用 PowerShell 來更新原則中的立即還原快照集保留期限

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

每個原則的預設快照集保留設定為兩天。 您可以將此值變更為最少1個，最多五天。 針對每週原則，快照集保留期固定為五天。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="what-are-the-cost-implications-of-instant-restore"></a>「立即還原」的成本影響為何？

快照集會儲存在磁碟上，以便加快建立復原點和還原作業的速度。 因此，您將在 VM 備份原則中看見對應至所選快照集保留的儲存體成本。

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>在進階儲存體帳戶中，針對立即復原點取得的快照集是否會佔用 10 TB 的快照集限制？

是，對於高階儲存體帳戶，針對立即復原點取得的快照集會佔用 10 TB 的配置快照空間。

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>快照集保留在五天期間內的運作方式為何？

每天都會取得新的快照集，所以有五個個別的累加快照集。 快照集的大小取決於資料變換，在大部分情況下大約是 2%-7%。

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>立即還原快照集是增量快照集，還是完整的快照集？

立即還原功能所取得的快照集是增量快照集。

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>如何計算立即還原功能所造成的大約成本增加？

這取決於 VM 的變換。 在穩定的狀態下，您可以假設成本增加 = 快照集保留期間，每部 VM 的每日變換，每 GB 的儲存體成本。

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>如果還原點的復原類型是「快照集與保存庫」，而我執行還原作業，則會使用哪個復原類型？

如果復原類型是「快照集與保存庫」，則會從本機快照集自動完成還原，相較於從保存庫進行還原，這麼做更快速。

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>如果我選取的還原點 (第 2 層) 保留期間小於快照集 (第 1 層) 保留期間，會發生什麼事？

除非刪除快照集 (第1層) ，否則新的模型不允許刪除 (第2層) 的還原點。 建議您排程大於快照集保留期間的還原點 (第 2 層) 保留期間。

### <a name="why-does-my-snapshot-still-exist-even-after-the-set-retention-period-in-backup-policy"></a>為什麼我的快照集仍然存在，即使在備份原則中設定的保留期限之後？

如果復原點有快照集，且它是可用的最新復原點，則會保留到下一次成功備份為止。 這是根據指定的「垃圾收集」 (GC) 原則。 它會要求至少有一個最新的復原點存在，以防所有後續的備份因 VM 中的問題而失敗。 在一般情況下，復原點會在到期後的最多24小時內清除。

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>我不需要立即還原功能。 可以停用嗎？

每個人都會啟用「立即還原」功能，且無法停用。 您可以將快照集保留降至最少一天。

### <a name="is-it-safe-to-restart-the-vm-during-the-transfer-process-which-can-take-many-hours-will-restarting-the-vm-interrupt-or-slow-down-the-transfer"></a>您是否可以安全地在傳輸程式期間重新開機 VM (這可能需要數小時的時間) ？ 將會重新開機 VM 中斷或減緩轉移的速度？

沒錯，它是安全的，對資料傳送速率絕對沒有任何影響。

