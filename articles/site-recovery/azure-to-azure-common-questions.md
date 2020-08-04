---
title: 使用 Azure Site Recovery 進行 Azure VM 災害復原的常見問題
description: 本文回答在您使用 Azure Site Recovery 時，有關 Azure VM 災害復原的常見問題。
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 7bc8427a51a9931ca82155232569767f12a8e266
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534017"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>常見問題：Azure 至 Azure 災害復原

本文回答使用 [Azure Site Recovery](site-recovery-overview.md) 時，對另一個 Azure 區域進行 Azure VM 災害復原的常見問題。

## <a name="general"></a>一般

### <a name="how-is-site-recovery-priced"></a>Site Recovery 是如何定價的？

檢閱[適用於 VM 的 Azure Site Recovery 定價](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)。

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Azure Site Recovery 的免費層如何運作？

每個受 Azure Site Recovery 保護的執行個體，在受保護的前 31 天皆為免費。 在免費期間之後，每個執行個體的保護都會以 [Azure 虛擬機器的 Azure Site Recovery 定價](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)費率計費。

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>在這前 31 天，我是否須負擔任何其他 Azure 費用？

是。 即使受保護的執行個體前 31 天的 Azure Site Recovery 免費，您仍有可能需要負擔 Azure 儲存體、儲存體交易與資料傳輸的費用。 復原的虛擬機器也可能會產生 Azure 計算費用。 在 [Azure Site Recovery 定價](https://azure.microsoft.com/pricing/details/site-recovery)取得定價的完整詳細資料。

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Azure 虛擬機器災害復原的最佳做法為何？

1. [了解 Azure 至 Azure 架構](azure-to-azure-architecture.md)
1. [檢閱支援和不支援的組態](azure-to-azure-support-matrix.md)
1. [設定適用於 Azure VM 的災害復原](azure-to-azure-how-to-enable-replication.md)
1. [執行測試容錯移轉](azure-to-azure-tutorial-dr-drill.md)
1. [容錯移轉及容錯回復至主要區域](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>如何確保目標區域中的容量？

Site Recovery 小組和 Azure 容量管理小組會規劃足夠的基礎結構容量。 當您開始容錯移轉時，這些小組也可協助確保受 Site Recovery 保護的 VM 執行個體部署到目標區域。

## <a name="replication"></a>複寫

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>我可以複寫透過 Azure 磁碟加密啟用的 VM 嗎？

是。 Site Recovery 支援對已啟用 Azure 磁碟加密的 VM 進行災害復原。 當您啟用複寫時，Azure 會將所有必要的磁碟加密金鑰和秘密從來源區域複製到使用者內容中的目標區域。 如果您沒有適當的權限，您的安全性系統管理員可以使用指令碼來複製金鑰和密碼。

- Site Recovery 支援對執行 Windows 的 Azure VM 進行 Azure 磁碟加密。
- Site Recovery 支援 Azure 磁碟加密 0.1 版，其具有需要 Azure Active Directory (Azure AD) 的結構描述。 Site Recovery 也支援 1.1 版，此版本不需要 Azure AD。 [深入了解 Azure 磁碟加密的延伸結構描述](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema)。
  - 若為 Azure 磁碟加密 1.1 版，您必須使用 Windows VM 搭配受控磁碟。
  - [深入了解](azure-to-azure-how-to-enable-replication-ade-vms.md)如何啟用已加密 VM 的複寫。

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>我可以從不同的資源群組中選取自動化帳戶嗎？

目前不支援透過入口網站，但您可以透過 Powershell 從不同的資源群組中選擇自動化帳戶。

### <a name="after-specifying-an-automation-account-that-is-in-a-different-resource-group-than-the-vault-am-i-permitted-to-delete-the-runbook-if-there-is-no-other-vault-to-specify"></a>在指定的自動化帳戶與保存庫位於不同的資源群組之後，如果沒有其他保存庫可供指定，我是否允許刪除 runbook？

建立的自訂 runbook 是一種工具，如果不需要相同的時間較長，則可以放心刪除。

### <a name="can-i-replicate-vms-to-another-subscription"></a>我可以將 VM 複寫至另一個訂用帳戶嗎？

是的，您可以將 Azure VM 複寫到相同 Azure AD 租用戶內的不同訂用帳戶。

藉由在複寫時選取另一個訂用帳戶，設定[跨訂用帳戶](https://azure.microsoft.com/blog/cross-subscription-dr)的災害復原。

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>我是否可以將區域固定的 Azure VM 複寫到另一個區域？

是的，您可以[將區域固定的 VM 複寫到另一個區域](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region)。

### <a name="can-i-replicate-vms-in-a-region-that-has-zones-from-non-zone-to-zonal-configuration"></a>我是否可以將區域中的 Vm 從非區域複寫到區域性設定？

否，目前不支援這種情況。 因應措施是，您可以使用 ASR 將 VM 複寫至另一個區域中的區域性設定，然後停用複寫。 接下來，重新啟用從該區域到原始區域的複寫，然後選擇要進行容錯移轉的區域性設定。

### <a name="can-i-exclude-disks"></a>是否可排除磁碟嗎？

是的，您可以使用 Power Shell 在設定防護時排除磁碟。 如需詳細資訊，請參閱[如何從複寫排除磁碟](azure-to-azure-exclude-disks.md)。

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>我是否可以將新磁碟加入至複寫的 VM，並為其啟用複寫？

是，搭配受控磁碟的 Azure Vm 支援將新磁碟加入至複寫的 VM，並為其啟用複寫。 當您將新磁碟加入至已啟用複寫的 Azure VM 時，VM 的複寫健康情況會顯示警告。 該警告指出可為 VM 上的一或多個磁碟提供保護。 您可以為新增的磁碟啟用複寫。

- 如果您為新增的磁碟啟用保護，則在初始複寫之後，警告將會消失。
- 如果您未對磁碟啟用複寫，則可以關閉警告。
- 如果您容錯移轉已新增磁碟並啟用複寫的 VM，則會有複寫點。 複寫點會顯示可供復原之用的磁碟。

例如，假設 VM 具有單一磁碟，而且您新增一個磁碟。 可能會有一個在您新增磁碟之前建立的複寫點。 此複寫點會顯示其包含「2 個磁碟之 1」。

Site Recovery 不支援從複寫的 VM 對磁碟進行「熱移除」。 如果您移除 VM 磁碟，則必須停用 VM 的複寫，然後再重新啟用。

### <a name="how-often-can-i-replicate-to-azure"></a>複寫到 Azure 的頻率為何？

當您從 Azure VM 複寫到另一個 Azure 區域時，會以持續方式執行複寫。 如需詳細資訊，請參閱 [Azure 至 Azure 複寫架構](./azure-to-azure-architecture.md#replication-process)。

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>我是否可以在一個區域內複寫虛擬機器？ 我需要這種功能來遷移 VM。

您無法使用 Azure 至 Azure 磁碟復原解決方案，在一個區域內複寫 VM。

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>我可以將 VM 複寫至任何 Azure 區域嗎？

藉由使用 Site Recovery，您可以在相同地理叢集內任何兩個區域之間複寫和復原 VM。 定義地理叢集時皆已考量資料延遲和主權範圍。 如需詳細資訊，請參閱 Site Recovery [區域支援對照表](./azure-to-azure-support-matrix.md#region-support)。

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery 是否需要網際網路連線？

否，Site Recovery 不需要網際網路連線。 但其確實需要能夠存取 Site Recovery URL 和 IP 範圍，如 [Azure VM 災害復原中的網路](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls)所提到的。

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>是否可以針對個別層複寫具有個別資源群組的應用程式？

是，您也可以複寫應用程式，並保留個別資源群組中的災害復原組態。

例如，如果您的應用程式在個別資源群組中具有每層的應用程式、資料庫 和 Web，則您必須選取[複寫精靈](./azure-to-azure-how-to-enable-replication.md#enable-replication)三次來保護所有層。 Site Recovery 會將這三層複寫至三個不同的資源群組中。

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>我是否可以跨資源群組移動儲存體帳戶？

否，這是不支援的案例。 不過，如果您意外地將儲存體帳戶移至不同的資源群組，並刪除原始的資源群組，則可以使用與舊資源群組相同的名稱來建立新的資源群組，然後將儲存體帳戶移至此資源群組。

## <a name="replication-policy"></a>複寫原則

### <a name="what-is-a-replication-policy"></a>什麼是複寫原則？

複寫原則會定義復原點保留歷程記錄的設定。 此原則也會應用程式一致快照集的頻率。 Azure Site Recovery 預設會建立具有下列預設設定的新複寫原則：

- 復原點的保留歷程記錄為 24 小時。
- 應用程式一致快照的頻率為4小時。

[深入了解複寫設定](./azure-to-azure-tutorial-enable-replication.md#configure-replication-settings)。

### <a name="what-is-a-crash-consistent-recovery-point"></a>什麼是當機時保持一致復原點？

當機時保持一致復原點具有磁碟上的資料，就像您在擷取快照集期間從伺服器拔掉電源線一樣。 當機時保持一致復原點不包含擷取快照集時記憶體內的任何資料。

現今，大多數應用程式都可以妥善地從當機時保持一致快照集復原。 對無資料庫作業系統及檔案伺服器、DHCP 伺服器和列印伺服器之類的應用程式而言，通常使用當機時保持一致復原點就已足夠。

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>當機時保持一致復原點產生的頻率為何？

Site Recovery 會每 5 分鐘建立一次當機時保持一致復原點。

### <a name="what-is-an-application-consistent-recovery-point"></a>什麼是應用程式一致復原點？

應用程式一致復原點是從應用程式一致快照集建立的。 應用程式一致快照集所擷取的資料與當機時保持一致復原點相同，同時也會擷取記憶體內的所有資料，以及所有進行中的交易。

由於有這些額外的內容，因此應用程式一致快照集包含最多內容，且需要最長的時間。 建議您針對資料庫作業系統和 SQL Server 之類的應用程式，使用應用程式一致復原點。

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>應用程式一致復原點對應用程式效能有何影響？

應用程式一致復原點會擷取記憶體內的所有資料和進行中的所有資料。 因為復原點會擷取該資料，所以需要類似 Windows 上磁碟區陰影複製服務的架構，來停止應用程式。 如果擷取程序頻繁執行，則當工作負載忙碌時，可能會影響效能。 我們不建議針對非資料庫工作負載的應用程式一致復原點使用低頻率。 即使是針對資料庫工作負載，1 小時就夠了。

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>應用程式一致復原點產生的最小頻率為何？

Site Recovery 可以建立最小頻率為 1 小時的應用程式一致復原點。

### <a name="how-are-recovery-points-generated-and-saved"></a>復原點要如何產生和儲存？

若要了解 Site Recovery 如何產生復原點，讓我們看看複寫原則的範例。 此複寫原則具有保留時間範圍為 24 小時，且應用程式一致頻率快照集為 1 小時的復原點。

Site Recovery 會每 5 分鐘建立一次當機時保持一致復原點。 您無法變更此頻率。 在過去一小時內，您可以從 12 個當機時保持一致點和 1 個應用程式一致點中進行選擇。 隨著時間過去，Site Recovery 會將過去 1 小時以外的所有復原點剪除，只儲存每小時 1 個復原點。

以下螢幕擷取畫面說明此範例。 在螢幕擷取畫面中：

- 在過去 1 小時內，每 5 分鐘就有一個復原點。
- 在過去 1 小時以外的時間，Site Recovery 則只會保留 1 個復原點。

   ![產生的復原點清單](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>最多可復原至多久之前？

您可以使用的最舊復原點為 72 小時。

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>我有 24 小時的複寫原則。 如果某個問題導致 Site Recovery 超過 24 小時無法產生復原點，會發生什麼情況？ 我先前的復原點是否會遺失？

不會，Site Recovery 會保留您先前所有的復原點。 根據復原點的保留時間範圍，Site Recovery 只會在產生新的復原點時，才會取代最舊的復原點。 由於發生問題，Site Recovery 無法產生任何新的復原點。 直到新的復原點出現之前，所有舊的復原點在您達到保留時間範圍之後仍會保留。

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>在 VM 上啟用複寫之後，我要如何變更複寫原則？

移至 [Site Recovery 保存庫] > [Site Recovery 基礎結構] > [複寫原則]。 選取您想要編輯的原則，然後儲存變更。 此外，任何變更都會套用到現有的所有複寫。

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>所有復原點都是 VM 的完整複本？還是會有差異？

所產生的第一個復原點會具有完整複本。 所有後續復原點則具有差異變更。

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>增長復原點的保留週期是否會增加儲存體成本？

是，如果您將保留週期從 24 小時增加到 72 小時，Site Recovery 將把復原點再多儲存 48 小時。 增加的時間將產生儲存體費用。 例如，單一復原點可能會有 10 GB 的差異變更，每個月的每 GB 成本為 $0.16。 額外費用將是每月 $1.60 × 48。

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>我可以在 Linux 伺服器中使用應用程式一致性來啟用複寫嗎？

是。 適用于 Linux 作業系統的 Azure Site Recovery 支援應用程式的自訂腳本，以進行應用程式一致性。 具有前置和後置選項的自訂腳本，會在應用程式一致性期間由 Azure Site Recovery 行動代理程式使用。 [深入了解](./site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

## <a name="multi-vm-consistency"></a>多 VM 一致性

### <a name="what-is-multi-vm-consistency"></a>什麼是多 VM 一致性？

多 VM 一致性確保復原點在所有複寫的虛擬機器之間都保持一致。

Site Recovery 提供 [多 VM 一致性] 選項，其會建立所有機器的複寫群組。

在您容錯移轉所有虛擬機器時，其會有共用的當機時保持一致和應用程式一致復原點。

請瀏覽教學課程來[啟用多 VM 一致性](./azure-to-azure-tutorial-enable-replication.md#enable-replication-for-a-vm)。

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>我是否可以容錯移轉位於多 VM 一致性複寫群組內的單一虛擬機器？

當您選取 [多 VM 一致性] 選項時，即表示應用程式與群組內的所有虛擬機器相依。 不允許進行單一虛擬機器容錯移轉。

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>我可以透過一個多 VM 一致性複寫群組複寫多少部虛擬機器？

您可以透過一個複寫群組一起複寫 16 部虛擬機器。

### <a name="when-should-i-enable-multi-vm-consistency"></a>何時應該啟用多 VM 一致性？

由於多 VM 一致性會耗用大量 CPU，因此啟用此功能可能會影響工作負載效能。 只在機器執行相同工作負載，且多部機器之間需要一致性時，才使用多 VM 一致性。 例如，如果您的應用程式中有兩個 SQL Server 執行個體和兩個 Web 伺服器，則應該只針對 SQL Server 執行個體使用多 VM 一致性。

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>是否可以將已複寫 VM 新增至複寫群組？
啟用複寫時，您可以將 VM 加入至新的複寫群組。 啟用複寫時，您也可以將 VM 加入至現有的複寫群組。 不過，您無法將已複寫 VM 加入至新的複寫群組或現有的複寫群組。
 
## <a name="failover"></a>容錯移轉


### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>如何確保 Azure VM 目標區域中的容量？

Site Recovery 小組和 Azure 容量管理小組會規劃足夠的基礎結構容量。 當您開始容錯移轉時，這些小組也可協助確保受 Site Recovery 保護的 VM 執行個體部署到目標區域。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？

容錯移轉並非自動發生。 您可以在入口網站中按一下適當按鈕來開始容錯移轉，也可以使用 [PowerShell](azure-to-azure-powershell.md) 來觸發容錯移轉。

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>我可以在容錯移轉之後保留公用 IP 位址嗎？

您無法在容錯移轉之後保留生產環境應用程式的公用 IP 位址。

當您在容錯移轉過程中顯示工作負載時，必須將 Azure 公用 IP 資源指派給工作負載。 Azure 公用 IP 資源必須在目標區域中提供。 您可以手動指派 Azure 公用 IP 資源，也可以使用復原方案將其自動化。 了解如何[在容錯移轉之後設定公用 IP 位址](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)。

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>我可以在容錯移轉期間保留私人 IP 位址嗎？

是，您可以保留私人 IP 位址。 根據預設，當您為 Azure VM 啟用災害復原時，Site Recovery 會根據來源資源設定建立目標資源。 針對使用靜態 IP 位址設定的 Azure 虛擬機器，Site Recovery 會嘗試為目標 VM 佈建相同的 IP 位址 (如果其未在使用中)。
了解[如何在容錯移轉期間保留 IP 位址](site-recovery-retain-ip-azure-vm-failover.md)。

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>容錯移轉之後，為什麼會將新 IP 位址指派給伺服器？

Site Recovery 會在容錯移轉時嘗試提供 IP 位址。 如果另一部虛擬機器正在使用該位址，Site Recovery 就會將下一個可用的 IP 位址設定為目標。

深入了解[如何設定虛擬網路的網路對應和 IP 位址](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)。

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>什麼是**最新 (最低 RPO)** 復原點？

[最新 (最低 RPO)] 選項會先處理已傳送到 Site Recovery 的所有資料。 在服務處理資料之後，其會為每個 VM 建立復原點，然後再容錯移轉至 VM。 此選項提供最低的復原點目標 (RPO)。 在容錯移轉之後建立的 VM，會在觸發容錯移轉時，將所有資料複寫到 Site Recovery。

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>**最新 (最低 RPO)** 復原點對容錯移轉 RTO 是否有影響？

是。 Site Recovery 會在容錯移轉前處理所有待處理的資料，因此與其他選項相比，此選項具有較高的復原時間目標 (RTO)。

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>復原點中的**最新處理**選項是什麼意思？

[最近處理] 選項會將方案中的所有 VM 容錯移轉至 Site Recovery 所處理的最新復原點。 若要查看特定 VM 的最新復原點，請檢查 VM 設定中的 [最新復原點]。 此選項提供低 RTO，因為不會花費任何時間來處理未處理的資料。

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>如果我的主要區域遭遇非預期的中斷，會發生什麼情況？

您可以在中斷之後觸發容錯移轉。 Site Recovery 不需要來自主要區域的連線即可執行容錯移轉。

### <a name="what-is-an-rto-of-a-vm-failover"></a>什麼是 VM 容錯移轉的 RTO？

Site Recovery 有 [2 小時的 RTO SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)。 不過在大部分情況下，Site Recovery 會在幾分鐘內容錯移轉虛擬機器。 若要計算 RTO，您可以移至容錯移轉作業查看其啟動 VM 所花的時間。 如需復原方案 RTO，請參閱下節。

## <a name="recovery-plans"></a>復原方案

### <a name="what-is-a-recovery-plan"></a>什麼是復原方案？

Site Recovery 中的復原方案會協調 VM 的容錯移轉復原。 這有助於讓復原「保持一致精確」、「可重複執行」及「自動化」。 復原方案可滿足下列需求：

- 定義一組會一起容錯移轉的虛擬機器
- 定義虛擬機器間的相依性，讓應用程式能夠正確地啟動
- 搭配自訂的手動動作自動執行復原，以完成虛擬機器容錯移轉以外的工作

深入了解[如何建立復原方案](site-recovery-create-recovery-plans.md)。

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>復原方案中是如何進行排序的？

在復原方案中，您可以建立多個群組來進行排序。 每個群組會各在一個時間容錯移轉。 屬於相同群組的虛擬機器會一起容錯移轉，後面再接著另一個群組。 若要了解如何使用復原方案來建立應用程式模型，請參閱[關於復原方案](recovery-plan-overview.md#model-apps)。

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>如何找到復原方案的 RTO？

若要檢查復原方案的 RTO，請為復原方案進行測試容錯移轉，然後移至 [Site Recovery 作業]。
在下列範例中，請參閱 **SAPTestRecoveryPlan** 作業。 此作業需要 8 分 59 秒來容錯移轉所有虛擬機器，並執行指定的動作。

![Site Recovery 作業清單](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>我是否可以將自動化 Runbook 新增至復原方案？

是的，您可以將「Azure 自動化」Runbook 整合至您的復原方案。 深入了解[如何新增 Azure 自動化 Runbook](site-recovery-runbook-automation.md)。

## <a name="reprotection-and-failback"></a>重新保護和容錯回復

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>我已從主要區域容錯移轉到災害復原區域。 DR 區域中的 VM 是否會自動受到保護？

否。 當您將 Azure VM 從一個區域[容錯移轉](./azure-to-azure-tutorial-failover-failback.md)至另一個區域時，VM 會在 DR 區域中以未受保護的狀態啟動。 若要將 VM 容錯回復到到主要區域，您必須[重新保護](./azure-to-azure-how-to-reprotect.md)次要區域中的 VM。

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>進行重新保護時，Site Recovery 是否會從次要區域將完整的資料複寫到主要區域？

這需視情況而定。 如果來源區域 VM 存在，則只會同步來源磁碟與目標磁碟之間的變更。 Site Recovery 會藉由比較磁碟來計算出差異，然後才傳輸資料。 此程序通常需要幾小時的時間。 如需有關在重新保護期間會發生之情況的詳細資訊，請參閱[重新保護已容錯移轉到主要區域的 Azure VM 執行個體](./azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)。

### <a name="how-much-time-does-it-take-to-fail-back"></a>容錯回復需要花費多久時間？

在重新保護之後，容錯回復所需的時間數與從主要區域容錯移轉到次要區域所需的時間相同。

## <a name="capacity"></a><a name="capacity"></a>容量

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>如何確保 Azure VM 目標區域中的容量？

Site Recovery 小組和 Azure 容量管理小組會規劃足夠的基礎結構容量。 當您開始容錯移轉時，這些小組也可協助確保受 Site Recovery 保護的 VM 執行個體部署到目標區域。

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery 是否會使用保留執行個體？

是，您可以在災害復原區域中購買[保留的 Azure VM](https://azure.microsoft.com/pricing/reserved-vm-instances/)，而且 Site Recovery 容錯移轉作業將會使用這些 VM。 不需要任何其他設定。

## <a name="security"></a>安全性

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>複寫資料會傳送到 Site Recovery 服務嗎？

否，Site Recovery 不會攔截複寫的資料，也不會擁有任何關於您 VM 上執行哪些項目的資訊。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。

Site Recovery 是經過認證的 ISO 27001:2013、27018、HIPAA 和 DPA。 服務正在進行 SOC2 和 FedRAMP JAB 評量。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery 會將複寫加密嗎？

是，傳輸中加密和 [Azure 中的待用加密](../storage/common/storage-service-encryption.md)均受支援。

## <a name="next-steps"></a>後續步驟

- [檢閱 Azure 對 Azure 支援需求](azure-to-azure-support-matrix.md)。
- [設定 Azure 對 Azure 複寫](azure-to-azure-tutorial-enable-replication.md)。
- 如果您在閱讀本文後有問題，請將問題張貼在 [Microsoft 的 Azure 復原服務問與答頁面](/answers/topics/azure-site-recovery.html)上。
