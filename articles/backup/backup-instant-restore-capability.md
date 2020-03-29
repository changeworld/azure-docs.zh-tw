---
title: Azure 立即還原功能
description: Azure 立即還原功能和 VM 備份堆疊、Azure Resource Manager 部署模型的常見問題集
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 7cf437c6b20ea6b688e8e93e401cf71ef0260888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705423"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>透過 Azure 備份的立即還原功能取得改良的備份和還原效能

> [!NOTE]
> 我們根據使用者的意見反應，正在將 **VM 備份堆疊 V2** 重新命名為**立即還原**，以減少與 Azure Stack 功能的混淆。
> 所有 Azure 備份使用者現已升級到**即時還原**。

「立即還原」的新模型提供下列增強功能：

* 能夠使用在備份作業中所建立的快照集 (可用於復原)，而不需等到資料轉送到保存庫完成。 如此可減少觸發還原之前，將快照集複製到保存庫的等候時間。
* 藉由本機預設保留 2 天的快照集，可減少備份和還原時間。 此預設快照保留值可配置為 1 到 5 天之間的任何值。
* 支援高達 32 TB 的磁片大小。 Azure 備份不建議調整磁片大小。
* 支援標準 SSD 磁片以及標準硬碟磁片和高級 SSD 磁片。
* 能夠在還原時使用非託管 VM 原始存儲帳戶（每個磁片）。 即使 VM 的磁碟分散於多個儲存體帳戶，仍然具有此功能。 它能夠針對各種不同的 VM 設定，加快還原作業的速度。
* 對於使用高級存儲的 VM 的備份，我們建議分配總分配的存儲空間的*50%* 可用空間，**而僅**第一次備份才需要這樣做。 完成第一個備份後，50% 的可用空間不是備份的要求。

## <a name="whats-new-in-this-feature"></a>這項功能有什麼新增功能？

目前的備份作業包含兩個階段：

1. 建立 VM 快照集。
2. 將 VM 快照集傳輸到 Azure 復原服務保存庫。

復原點只會在第 1 和第 2 階段完成之後才建立。 在此升級中，完成快照集時就會建立復原點，而且快照集類型的此復原點可用來執行還原 (使用相同的還原流程)。 可以使用"快照"作為復原點類型在 Azure 門戶中標識此復原點，在快照傳輸到保存庫後，復原點類型將更改為"快照和保存庫"。

![VM 備份堆疊 Resource Manager 部署模型的備份作業 -- 儲存體與保存庫](./media/backup-azure-vms/instant-rp-flow.png)

預設情況下，快照將保留兩天。 此功能允許通過縮短還原時間來從這些快照中還原操作。 它減少了從保存庫轉換和複製資料所需的時間。

## <a name="feature-considerations"></a>功能考量

* 快照集會儲存在磁碟上，以便建立復原點及加快還原作業的速度。 因此，您將看見儲存體成本會隨著在這段期間內取得的快照集而增減。
* 增量快照會儲存為分頁 Blob。 使用非受控磁碟的所有客戶，需對儲存在其本機儲存體帳戶中的快照集付費。 因為由受控 VM 備份所使用的還原點集合會於基礎儲存體層級使用 Blob 快照集，因此針對受控磁碟，您將會看見對應到 Blob 快照集定價的成本，而且它們會持續累加。
* 對於進階儲存體帳戶，針對立即復原點取得的快照集會計入配置空間的 10 TB 限制。
* 您可以根據還原需求來設定快照集保留期。 您可以根據需求，在備份原則刀鋒視窗中，將快照集保留期最少設為一天，如下所述。 如果不經常執行還原，這將有助於節省快照保留成本。
* 它是單向升級，一旦升級到即時恢復，您就無法返回。

>[!NOTE]
>如果升級此立即還原，所有客戶的快照集保留持續時間 (**同時包含新的和現有的**) 會設定為預設值兩天。 不過，您可以根據每個需求，將持續時間設定為介於 1 至 5 天的任何值。

## <a name="cost-impact"></a>成本影響

增量快照存儲在 VM 的存儲帳戶中，該帳戶用於即時恢復。 增量快照集表示快照集所佔用的空間等於建立快照集後寫入的頁面所佔用的空間。 計費仍用於快照佔用的每個 GB 使用空間，並且每 GB 的價格與[定價頁上](https://azure.microsoft.com/pricing/details/managed-disks/)提到的價格相同。 對於使用非託管磁片的 VM，可以在每個磁片的 VHD 檔的功能表中看到快照。 對於託管磁片，快照存儲在指定資源組中的還原點集合資源中，並且快照本身不直接可見。

>[!NOTE]
> 快照保留期固定為每週策略的 5 天。

## <a name="configure-snapshot-retention"></a>配置快照保留

### <a name="using-azure-portal"></a>使用 Azure 入口網站

在 Azure 門戶中，您可以在 **"即時還原**"部分下看到**在 VM 備份策略**邊欄選項卡中添加的欄位。 您可以針對與特定備份原則相關聯的所有 VM，從 **VM 備份原則**刀鋒視窗變更快照集保留持續時間。

![立即還原功能](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>使用 PowerShell

>[!NOTE]
> 從 Az PowerShell 版本 1.6.0 開始，您可以使用 PowerShell 更新策略中的即時還原快照保留期

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

每個策略的預設快照保留設置為兩天。 使用者可以將該值更改為至少 1 天，最多 5 天。 對於每週策略，快照保留期固定為五天。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="what-are-the-cost-implications-of-instant-restore"></a>「立即還原」的成本影響為何？

快照集會儲存在磁碟上，以便加快建立復原點和還原作業的速度。 因此，您將在 VM 備份原則中看見對應至所選快照集保留的儲存體成本。

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>在進階儲存體帳戶中，針對立即復原點取得的快照集是否會佔用 10 TB 的快照集限制？

是，對於高級存儲帳戶，為即時復原點拍攝的快照佔用 10 TB 分配的快照空間。

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>快照集保留在五天期間內的運作方式為何？

每天都會取得新的快照集，所以有五個個別的累加快照集。 快照的大小取決於資料改動，在大多數情況下，資料更改約為 2%-7%。

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>立即還原快照集是增量快照集，還是完整的快照集？

立即還原功能所取得的快照集是增量快照集。

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>如何計算立即還原功能所造成的大約成本增加？

這取決於 VM 的變換。 在穩定的狀態下，您可以假設成本增加 = 快照集保留期間，每部 VM 的每日變換，每 GB 的儲存體成本。

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>如果還原點的復原類型是「快照集與保存庫」，而我執行還原作業，則會使用哪個復原類型？

如果復原類型是「快照集與保存庫」，則會從本機快照集自動完成還原，相較於從保存庫進行還原，這麼做更快速。

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>如果我選取的還原點 (第 2 層) 保留期間小於快照集 (第 1 層) 保留期間，會發生什麼事？

除非刪除快照集 (第 1 層)，否則新模型不允許刪除還原點 (第 2 層)。 建議您排程大於快照集保留期間的還原點 (第 2 層) 保留期間。

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>為何在備份原則中設定的保留期間之後，我的快照集仍然存在？

如果復原點具有快照集而且是最新的可用 RP，則會保留到有下一個成功備份的時候。 這是按照今天設計的"垃圾回收"（GC）策略，該策略要求至少一個最新的 RP 始終存在，以防所有備份因 VM 中的問題而進一步失敗。 在正常情況下，RP 會在到期後的最多 24 小時內清除。

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>我不需要即時還原功能。 可以禁用它嗎？

為每個人啟用即時還原功能，不能禁用。 您可以將快照保留時間減少到至少一天。

>[!NOTE]
> **Azure 備份現在支援使用 Azure 虛擬機器備份解決方案進行選擇性磁片備份和還原。**
>
>如今，Azure 備份支援使用虛擬機器備份解決方案一起備份 VM 中的所有磁片（作業系統和資料）。 使用排除磁片功能，您可以選擇從 VM 中的許多資料磁片備份一個或多個資料磁片。 這為備份和恢復需求提供了高效且經濟高效的解決方案。 每個復原點都包含備份操作中包含的磁片的資料，這進一步允許您在還原操作期間從給定復原點復原磁碟子集。 這適用于從快照和保存庫還原。
>
>**要註冊預覽版，請寫信給我們：AskAzureBackupTeam@microsoft.com**
