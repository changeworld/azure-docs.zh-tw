---
title: 有關 Azure 網站恢復的 Azure VM 災難恢復的常見問題
description: 本文回答了使用 Azure 網站恢復時有關 Azure VM 災難恢復的常見問題。
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 7d3bcc32dc8f1412a5adbc175a5f8618628bce83
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547880"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>常見問題:Azure 到 Azure 災難恢復

本文回答了有關 Azure VM 災難恢復到另一個 Azure 區域的常見問題,供您使用[Azure 網站恢復](site-recovery-overview.md)時。

## <a name="general"></a>一般

### <a name="how-is-site-recovery-priced"></a>Site Recovery 是如何定價的？

檢視[VM 的 Azure 網站恢復定價](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)。

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Azure Site Recovery 的免費層如何運作？

每個受 Azure Site Recovery 保護的執行個體，在受保護的前 31 天皆為免費。 在此期間之後,每個實例的保護以[Azure 虛擬機的 Azure 網站恢復定價中的](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)速率進行。

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>在這前 31 天，我是否須負擔任何其他 Azure 費用？

是。 即使 Azure 網站恢復在受保護實例的前 31 天是免費的,也可能對 Azure 存儲、存儲事務和數據傳輸產生費用。 恢復的虛擬機也可能產生 Azure 計算費用。 在[Azure 網站恢復定價](https://azure.microsoft.com/pricing/details/site-recovery)中獲取有關定價的完整詳細資訊。

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Azure 虛擬機災難恢復的最佳做法是什麼?

1. [了解 Azure 至 Azure 架構](azure-to-azure-architecture.md)
1. [檢閱支援和不支援的組態](azure-to-azure-support-matrix.md)
1. [設定適用於 Azure VM 的災害復原](azure-to-azure-how-to-enable-replication.md)
1. [執行測試容錯移轉](azure-to-azure-tutorial-dr-drill.md)
1. [容錯移轉及容錯回復至主要區域](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>如何確保目標區域的容量?

網站恢復團隊和 Azure 容量管理團隊計畫有足夠的基礎結構容量。 啟動故障轉移時,團隊還有助於確保受網站恢復保護的 VM 實例將部署到目標區域。

## <a name="replication"></a>複寫

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>我可以複寫透過 Azure 磁碟加密啟用的 VM 嗎？

是。 網站恢復支援啟用 Azure 磁碟加密的 VM 的災難恢復。 啟用複製時,Azure 會將所有必需的磁碟加密密鑰和機密從源區域複製到使用者上下文中的目標區域。 如果您沒有適當的許可權,您的安全管理員可以使用腳本複製密鑰和機密。

- 網站恢復支援為運行 Windows 的 Azure VM 進行 Azure 磁碟加密。
- 網站恢復支援 Azure 磁碟加密版本 0.1,該版本具有需要 Azure 活動目錄 (Azure AD) 的架構。 網站恢復還支援版本 1.1,它不需要 Azure AD。 [瞭解有關 Azure 磁碟加密擴充架構的詳細資訊](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema)。
  - 對於 Azure 磁碟加密版本 1.1,必須使用具有託管磁碟的 Windows VM。
  - [詳細瞭解](azure-to-azure-how-to-enable-replication-ade-vms.md)如何為加密 VM 啟用複製。

### <a name="can-i-replicate-vms-to-another-subscription"></a>我可以將 VM 複寫至另一個訂用帳戶嗎？

是的，您可以將 Azure VM 複寫到相同 Azure AD 租用戶內的不同訂用帳戶。

通過在複製時選擇另一個訂閱來配置[跨訂閱](https://azure.microsoft.com/blog/cross-subscription-dr)的災難恢復。

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>我是否可以將區域固定的 Azure VM 複寫到另一個區域？

是的，您可以[將區域固定的 VM 複寫到另一個區域](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region)。

### <a name="can-i-exclude-disks"></a>是否可排除磁碟嗎？

是的，您可以使用 Power Shell 在設定防護時排除磁碟。 有關詳細資訊,請參閱[如何從複製中排除磁碟](azure-to-azure-exclude-disks.md)。

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>是否可以向複製 VM 添加新磁碟並為它們啟用複製?

可以,對於具有託管磁碟的 Azure VM,支援向複製的 VM 添加新磁碟並為它們啟用複製。 將新磁碟添加到已啟用複製的 Azure VM 時,VM 的複製運行狀況將顯示警告。 該警告指出 VM 上的一個或多個磁碟可供保護。 您可以為新增的磁碟啟用複製。

- 如果為添加的磁碟啟用保護,則警告將在初始複製後消失。
- 如果不為磁片啟用複製,則可以關閉該警告。
- 如果對已啟用了添加的磁碟和複製的 VM 進行故障轉移,則存在複製點。 複製點將顯示可用於恢復的磁碟。

例如,假設 VM 具有單個磁碟,並且添加新磁碟。 在添加磁碟之前,可能創建了複製點。 此複製點將顯示它由"2個磁碟中的1個"組成。

網站恢復不支援從複製的 VM 中"熱刪除"磁碟。 如果刪除 VM 磁碟,則需要禁用,然後重新啟用 VM 的複製。

### <a name="how-often-can-i-replicate-to-azure"></a>複寫到 Azure 的頻率為何？

當您從 Azure VM 複寫到另一個 Azure 區域時，會以持續方式執行複寫。 如需詳細資訊，請參閱 [Azure 至 Azure 複寫架構](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process)。

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>我是否可以在一個區域內複寫虛擬機器？ 我需要此功能來遷移 VM。

不能使用 Azure 到 Azure 磁碟恢復解決方案在區域內複製 VM。

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>是否可以將 VM 實體複製到任何 Azure 區域?

通過使用網站恢復,可以在同一地理群集中的任何兩個區域之間複製和恢復 VM。 定義地理叢集時皆已考量資料延遲和主權範圍。 如需詳細資訊，請參閱 Site Recovery [區域支援對照表](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)。

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery 是否需要網際網路連線？

否,網站恢復不需要互聯網連接。 但它確實需要訪問網站恢復 URL 和 IP 範圍,如[Azure VM 災難恢復中的網路中](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls)所述。

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>是否可以複製具有獨立層的單獨資源組的應用程式?

可以,您可以複製應用程式,並將災難恢復配置保存在單獨的資源組中。

例如,如果應用程式在單獨的資源組中具有每個層的應用程序、資料庫和 Web,則必須選擇[複製嚮導](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication)三次以保護所有層。 網站恢復將複製這三個層到三個不同的資源組。

## <a name="replication-policy"></a>複寫原則

### <a name="what-is-a-replication-policy"></a>什麼是複寫原則？

複製策略定義恢復點的保留歷史記錄的設置。 該策略還定義了應用一致的快照的頻率。 Azure Site Recovery 預設會建立具有下列預設設定的新複寫原則：

- 復原點的保留歷程記錄為 24 小時。
- 應用程式一致快照集的頻率為 60 分鐘。

[瞭解此專案設定的更多內容](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings)。

### <a name="what-is-a-crash-consistent-recovery-point"></a>什麼是當機時保持一致復原點？

崩潰一致的恢復點具有磁碟上的數據,就像您在快照期間從伺服器拔出電源線一樣。 崩潰一致的恢復點不包括拍攝快照時記憶體中的任何內容。

現今，大多數應用程式都可以妥善地從當機時保持一致快照集復原。 對無資料庫作業系統及檔案伺服器、DHCP 伺服器和列印伺服器之類的應用程式而言，通常使用當機時保持一致復原點就已足夠。

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>當機時保持一致復原點產生的頻率為何？

Site Recovery 會每 5 分鐘建立一次當機時保持一致復原點。

### <a name="what-is-an-application-consistent-recovery-point"></a>什麼是應用程式一致復原點？

應用程式一致復原點是從應用程式一致快照集建立的。 應用程式一致性恢復點捕獲與崩潰一致性快照相同的數據,同時捕獲記憶體中的數據和正在處理的所有事務。

由於其額外的內容,應用程式一致的快照參與最多,使用時間最長。 建議您針對資料庫作業系統和 SQL Server 之類的應用程式，使用應用程式一致復原點。

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>應用程式一致復原點對應用程式效能有何影響？

應用程式一致的恢復點捕獲記憶體中和進程中的所有數據。 由於恢復點捕獲該數據,因此它們需要 Windows 上的卷卷影複製服務等框架來靜默應用程式。 如果捕獲過程頻繁,則當工作負載已繁忙時,可能會影響性能。 我們不建議對非資料庫工作負載的應用一致性恢復點使用低頻。 即使對於資料庫工作負載,1 小時也就足夠了。

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>應用程式一致復原點產生的最小頻率為何？

網站恢復可以創建一個應用程式一致的恢復點,最小頻率為 1 小時。

### <a name="how-are-recovery-points-generated-and-saved"></a>復原點要如何產生和儲存？

要瞭解網站恢復如何生成恢復點,讓我們看一個複製策略的示例。 此複製策略具有具有 24 小時保留視窗和 1 小時應用一致頻率快照的恢復點。

Site Recovery 會每 5 分鐘建立一次當機時保持一致復原點。 您無法更改此頻率。 對於最後一小時,您可以從 12 個崩潰一致性點和 1 個應用一致性點中進行選擇。 隨著時間的推移,網站恢復會修剪過去一小時之後的所有恢復點,每小時僅保存 1 個恢復點。

以下螢幕擷取畫面說明此範例。 在螢幕擷取畫面中：

- 在過去一小時內,有頻率為 5 分鐘的恢復點。
- 超過過去一小時,網站恢復僅保留 1 個恢復點。

   ![產生的復原點清單](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>最多可復原至多久之前？

您可以使用的最舊復原點為 72 小時。

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>我有 24 小時的複製策略。 如果問題阻止網站恢復生成恢復點超過 24 小時,會發生什麼情況? 我先前的復原點是否會遺失？

不會，Site Recovery 會保留您先前所有的復原點。 根據恢復點的保留視窗,網站恢復僅在生成新點時才替換最舊的點。 由於此問題,網站恢復無法生成任何新的恢復點。 在有新的恢復點之前,所有舊點都將在到達保留視窗后保留。

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>在 VM 上啟用複寫之後，我要如何變更複寫原則？

跳到**站台復原管庫** > **站台恢復基礎結構** > **複製原則**。 選擇要編輯的策略,並保存更改。 此外，任何變更都會套用到現有的所有複寫。

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>所有復原點都是 VM 的完整複本？還是會有差異？

所產生的第一個復原點會具有完整複本。 所有後續復原點則具有差異變更。

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>增長復原點的保留週期是否會增加儲存體成本？

是的,如果將保留期從 24 小時增加到 72 小時,網站恢復將額外保存恢復點 48 小時。 增加的時間將產生儲存體費用。 例如,單個恢復點可能具有 10 GB 的增量更改,每 GB 成本為每月 0.16 美元。 額外費用為每月1.60美元~48美元。

## <a name="multi-vm-consistency"></a>多 VM 一致性

### <a name="what-is-multi-vm-consistency"></a>什麼是多 VM 一致性?

多 VM 一致性可確保恢復點在所有複製的虛擬機之間保持一致。

網站恢復提供**多 VM 一致性**選項,該選項創建所有計算機的複製組。

當您故障轉移虛擬機器時,虛擬機器將具有共用崩潰一致性和應用一致的恢復點。

完成本教學[以開啟多 VM 一致性](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm)。

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>是否可以在多 VM 一致性複製組中對單個虛擬機進行故障轉移?

選擇 **「多 VM 一致性**」選項時,您指出應用程式依賴於組中的所有虛擬機器。 不允許單個虛擬機器故障轉移。

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>我可以作為多 VM 一致性複製組的一部分複製多少虛擬機?

您可以透過一個複寫群組一起複寫 16 部虛擬機器。

### <a name="when-should-i-enable-multi-vm-consistency"></a>何時應啟用多 VM 一致性?

由於多 VM 一致性佔用 CPU,因此啟用它可能會影響工作負載性能。 僅當電腦運行相同的工作負載並且需要跨多台電腦的一致性時,才使用多 VM 一致性。 例如,如果應用程式中有兩個 SQL Server 實例和兩個 Web 伺服器,則僅應具有 SQL Server 實例的多 VM 一致性。

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>是否可以將已複製的 VM 添加到複製群組?

在啟用複製時,可以將 VM 添加到新的複製組。 您還可以在啟用複製的同時將 VM 添加到現有複製組。 但是,您不能將已複製的 VM 添加到新的複製組或現有複製組。

## <a name="failover"></a>容錯移轉

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>如何確保 Azure VM 的目標區域中的容量?

網站恢復團隊和 Azure 容量管理團隊計畫有足夠的基礎結構容量。 啟動故障轉移時,團隊還有助於確保受網站恢復保護的 VM 實例將部署到目標區域。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？

容錯移轉並非自動發生。 您可以在門戶中按一下啟動故障轉移,也可以使用[PowerShell](azure-to-azure-powershell.md)觸發故障轉移。

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>故障轉移後,我是否可以保留公共 IP 位址?

故障轉移後,無法保留生產應用程式的公共 IP 位址。

在故障轉移過程中啟動工作負荷時,需要為工作負荷分配 Azure 公共 IP 資源。 Azure 公共 IP 資源必須在目標區域中可用。 您可以手動分配 Azure 公共 IP 資源,也可以使用恢復計畫自動分配它。 瞭解如何[在故障移送後設定公共 IP 位址](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)。

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>我可以在故障轉移期間保留專用 IP 位址嗎?

可以,您可以保留專用 IP 位址。 根據預設，當您為 Azure VM 啟用災害復原時，Site Recovery 會根據來源資源設定建立目標資源。 對於配置了靜態 IP 位址的 Azure 虛擬機器,網站恢復嘗試為目標 VM 預配相同的 IP 位址(如果該位址未使用)。
瞭解如何[在故障移轉到 IP 位址](site-recovery-retain-ip-azure-vm-failover.md)。

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>故障轉移後,為什麼伺服器分配了新的 IP 位址?

Site Recovery 會在容錯移轉時嘗試提供 IP 位址。 如果另一部虛擬機器正在使用該位址，Site Recovery 就會將下一個可用的 IP 位址設定為目標。

瞭解有關[為虛擬網路設定網路映射和 IP 定址](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)的更多資訊。

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>什麼是**最新 (最低 RPO)** 復原點？

**"最新(最低 RPO)"** 選項首先處理已發送到網站恢復的所有資料。 服務處理數據後,它會為每個 VM 創建一個恢復點,然後再故障轉移到 VM。 此選項提供最低恢復點目標 (RPO)。 故障轉移後創建的 VM 將從觸發故障轉移時將所有資料複製到網站恢復。

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>**最新 (最低 RPO)** 復原點對容錯移轉 RTO 是否有影響？

是。 網站恢復在故障通過之前處理所有掛起的數據,因此此選項比其他選項具有更高的恢復時間目標 (RTO)。

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>復原點中的**最新處理**選項是什麼意思？

**"最新處理**"選項在計劃中的所有 VM 上失敗,到網站恢復處理的最新恢復點。 要檢視特定 VM 的最新復原點,請在 VM 設定中檢視**最新復原點**。 此選項提供低 RTO，因為不會花費任何時間來處理未處理的資料。

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>如果我的主區域遇到意外中斷,會發生什麼情況?

您可以在中斷之後觸發容錯移轉。 網站恢復不需要來自主區域的連接即可執行故障轉移。

### <a name="what-is-an-rto-of-a-vm-failover"></a>什麼是 VM 故障轉移的 RTO?

網站恢復具有[2 小時的 RTO SLA。](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) 但是,大多數時候,網站恢復在幾分鐘內通過虛擬機失敗。 您可以通過存取故障轉移作業來計算 RTO,該作業顯示啟動 VM 所花的時間。 有關恢復計劃 RTO,請參閱下一節。

## <a name="recovery-plans"></a>復原方案

### <a name="what-is-a-recovery-plan"></a>什麼是復原方案？

Site Recovery 中的復原方案會協調 VM 的容錯移轉復原。 這有助於讓復原「保持一致精確」、「可重複執行」及「自動化」。 回復計劃滿足以下需求:

- 定義一組會一起容錯移轉的虛擬機器
- 定義虛擬機器間的相依性，讓應用程式能夠正確地啟動
- 搭配自訂的手動動作自動執行復原，以完成虛擬機器容錯移轉以外的工作

[瞭解此建立回復排程的詳細資訊](site-recovery-create-recovery-plans.md)。

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>復原方案中是如何進行排序的？

在復原方案中，您可以建立多個群組來進行排序。 每個群組會各在一個時間容錯移轉。 屬於同一組的虛擬機一起故障轉移,後跟另一組。 若要了解如何使用復原方案來建立應用程式模型，請參閱[關於復原方案](recovery-plan-overview.md#model-apps)。

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>如何找到復原方案的 RTO？

若要檢查復原方案的 RTO，請為復原方案進行測試容錯移轉，然後移至 [Site Recovery 作業]****。
在下面的範例中,請參閱作業**SAPTest 恢復計畫**。 作業需要 8 分 59 秒才能故障接管所有虛擬機器並執行指定的操作。

![Site Recovery 作業清單](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>我是否可以將自動化 Runbook 新增至復原方案？

是的，您可以將「Azure 自動化」Runbook 整合至您的復原方案。 瞭解有關添加[Azure 自動化運行簿](site-recovery-runbook-automation.md)的更多詳細資訊。

## <a name="reprotection-and-failback"></a>重新保護和容錯回復

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>我從主區域故障轉移到災難恢復區域。 DR 區域中的 VM 是自動保護的嗎?

否。 當您將 Azure VM 從一個區域[容錯移轉](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)至另一個區域時，VM 會在 DR 區域中以未受保護的狀態啟動。 若要將 VM 容錯回復到到主要區域，您必須[重新保護](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect)次要區域中的 VM。

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>進行重新保護時，Site Recovery 是否會從次要區域將完整的資料複寫到主要區域？

這需視情況而定。 如果源區域 VM 存在,則僅同步源磁碟和目標磁碟之間的更改。 Site Recovery 會藉由比較磁碟來計算出差異，然後才傳輸資料。 此程序通常需要幾小時的時間。 有關重新保護期間發生的情況的詳細資訊,請參閱[將 Azure VM 實體故障重新保護到主區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)。

### <a name="how-much-time-does-it-take-to-fail-back"></a>容錯回復需要花費多久時間？

重新保護後,故障恢復所需的時間大約與從主區域故障轉移到輔助區域所需的時間大致相同。

## <a name="capacity"></a><a name="capacity"></a>Capacity

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>如何確保 Azure VM 的目標區域中的容量?

網站恢復團隊和 Azure 容量管理團隊計畫有足夠的基礎結構容量。 啟動故障轉移時,團隊還有助於確保受網站恢復保護的 VM 實例將部署到目標區域。

### <a name="does-site-recovery-work-with-reserved-instances"></a>網站恢復是否與保留實例一起工作?

可以,您可以在災難恢復區域購買保留的[Azure VM,](https://azure.microsoft.com/pricing/reserved-vm-instances/)網站恢復故障轉移操作將使用它們。 不需要任何其他設定。

## <a name="security"></a>安全性

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>複寫資料會傳送到 Site Recovery 服務嗎？

否,網站恢復不會截獲複製的數據,並且沒有關於 VM 上運行的內容的任何資訊。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。

網站恢復是 ISO 27001:2013、27018、HIPAA 和 DPA 認證。 該服務正在進行SOC2和FedRAMP JAB評估。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery 會將複寫加密嗎？

是的,支援在 Azure[中進行傳送中的加密與靜態加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)。

## <a name="next-steps"></a>後續步驟

- [檢視 Azure 到 Azure 支援要求](azure-to-azure-support-matrix.md)。
- [設定 Azure 到 Azure 複製](azure-to-azure-tutorial-enable-replication.md)。
- 如果在閱讀本文後有疑問,則將其發佈到[Azure 恢復服務論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)。
