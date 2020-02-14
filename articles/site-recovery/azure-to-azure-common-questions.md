---
title: 有關使用 Azure Site Recovery 的 Azure VM 嚴重損壞修復的常見問題
description: 當您使用 Azure Site Recovery 時，本文會回答有關 Azure VM 嚴重損壞修復的常見問題。
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 99fed1d2b1246e4c099f275708f694e5d7ea2f22
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190829"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>常見問題： Azure 到 Azure 的嚴重損壞修復

本文將說明當您使用[Azure Site Recovery](site-recovery-overview.md)時，將 azure vm 容錯回復至另一個 Azure 區域的常見問題。

## <a name="general"></a>一般

### <a name="how-is-site-recovery-priced"></a>Site Recovery 是如何定價的？

請參閱[vm Azure Site Recovery 定價](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)。

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Azure Site Recovery 的免費層如何運作？

每個受 Azure Site Recovery 保護的執行個體，在受保護的前 31 天皆為免費。 在這段時間之後，每個實例的保護都會以[Azure 虛擬機器的 Azure Site Recovery 定價](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)費率計費。

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>在這前 31 天，我是否須負擔任何其他 Azure 費用？

是的。 雖然在受保護實例的前31天內 Azure Site Recovery 免費，但您可能會產生 Azure 儲存體、儲存體交易和資料傳輸的費用。 復原的虛擬機器也可能會產生 Azure 計算費用。 取得定價[Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery)定價的完整詳細資料。

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Azure 虛擬機器嚴重損壞修復的最佳做法為何？

1. [了解 Azure 至 Azure 架構](azure-to-azure-architecture.md)
1. [檢閱支援和不支援的組態](azure-to-azure-support-matrix.md)
1. [設定適用於 Azure VM 的災害復原](azure-to-azure-how-to-enable-replication.md)
1. [執行測試容錯移轉](azure-to-azure-tutorial-dr-drill.md)
1. [容錯移轉及容錯回復至主要區域](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>如何確保目的地區域中的容量？

Site Recovery 小組和 Azure 容量管理小組會規劃足夠的基礎結構容量。 當您開始進行容錯移轉時，小組也有助於確保受 Site Recovery 保護的 VM 實例會部署到目的地區域。

## <a name="replication"></a>複寫

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>我可以複寫透過 Azure 磁碟加密啟用的 VM 嗎？

是的。 Site Recovery 支援已啟用 Azure 磁碟加密之 Vm 的嚴重損壞修復。 當您啟用複寫時，Azure 會將所有必要的磁片加密金鑰和秘密從來源區域複製到使用者內容中的目的地區域。 如果您沒有適當的許可權，您的安全性系統管理員可以使用腳本來複製金鑰和密碼。

- Site Recovery 支援執行 Windows 之 Azure Vm 的 Azure 磁碟加密。
- Site Recovery 支援 Azure 磁碟加密版本0.1，其具有需要 Azure Active Directory （Azure AD）的架構。 Site Recovery 也支援1.1 版，這不需要 Azure AD。 [深入瞭解 Azure 磁片加密的延伸模組架構](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schemata)。
  - 針對 Azure 磁碟加密版本1.1，您必須使用具有受控磁片的 Windows Vm。
  - [深入瞭解](azure-to-azure-how-to-enable-replication-ade-vms.md)如何針對加密的 vm 啟用複寫。

### <a name="can-i-replicate-vms-to-another-subscription"></a>我可以將 VM 複寫至另一個訂用帳戶嗎？

是的，您可以將 Azure VM 複寫到相同 Azure AD 租用戶內的不同訂用帳戶。

在複寫時選取另一個訂用帳戶，以設定[跨訂閱](https://azure.microsoft.com/blog/cross-subscription-dr)的損毀修復。

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>我是否可以將區域固定的 Azure VM 複寫到另一個區域？

是的，您可以[將區域固定的 VM 複寫到另一個區域](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region)。

### <a name="can-i-exclude-disks"></a>是否可排除磁碟嗎？

是的，您可以使用 Power Shell 在設定防護時排除磁碟。 如需詳細資訊，請參閱[如何將磁片從複寫中排除](azure-to-azure-exclude-disks.md)。

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>我可以將新磁片新增至複寫的 Vm，並為它們啟用複寫嗎？

是，具有受控磁片的 Azure Vm 支援將磁片新增至複寫的 Vm，並為其啟用複寫。 當您將新的磁片新增至已啟用複寫的 Azure VM 時，VM 的複寫健康情況會顯示警告。 該警告指出 VM 上的一或多個磁片可供保護。 您可以為新增的磁片啟用複寫。

- 如果您為新增的磁片啟用保護，在初始複寫之後，警告將會消失。
- 如果您未啟用磁片的複寫，您可以關閉警告。
- 如果您損毀修復已啟用新增磁片和複寫的 VM，則會有複寫點。 複寫點會顯示可供復原的磁片。 

例如，假設 VM 具有單一磁片，而您新增一個。 在您新增磁片之前，可能會有一個複寫點已建立。 此複寫點會顯示其包含「2個磁片的1個」。

Site Recovery 不支援從複寫的 VM 對磁片進行「熱移除」。 如果您移除 VM 磁片，則必須停用 VM 的複寫，然後再重新啟用。

### <a name="how-often-can-i-replicate-to-azure"></a>複寫到 Azure 的頻率為何？

當您從 Azure VM 複寫到另一個 Azure 區域時，會以持續方式執行複寫。 如需詳細資訊，請參閱 [Azure 至 Azure 複寫架構](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process)。

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>我是否可以在一個區域內複寫虛擬機器？ 我需要這種功能來遷移 Vm。

您無法使用 Azure 至 Azure 磁片修復解決方案來複寫區域內的 Vm。

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>我可以將 VM 實例複寫到任何 Azure 區域嗎？

藉由使用 Site Recovery，您可以在相同的地理叢集內的任何兩個區域之間複寫和復原 Vm。 定義地理叢集時皆已考量資料延遲和主權範圍。 如需詳細資訊，請參閱 Site Recovery [區域支援對照表](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)。

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery 是否需要網際網路連線？

否，Site Recovery 不需要網際網路連線。 但它確實需要 Site Recovery Url 和 IP 範圍的存取權，如[AZURE VM 嚴重損壞修復的網路中](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)所述。

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>我可以針對個別層級的個別資源群組來複寫應用程式嗎？

是，您可以複寫應用程式，並將嚴重損壞修復設定保留在另一個資源群組中。

例如，如果您的應用程式在不同的資源群組中有每一層的應用程式、資料庫和 web，則您必須選取 [[複製嚮導]](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication)三次來保護所有層級。 Site Recovery 會將這三個層級複寫到三個不同的資源群組。

## <a name="replication-policy"></a>複寫原則

### <a name="what-is-a-replication-policy"></a>什麼是複寫原則？

複寫原則會定義復原點保留歷程記錄的設定。 此原則也會定義應用程式一致快照的頻率。 Azure Site Recovery 預設會建立具有下列預設設定的新複寫原則：

- 復原點的保留歷程記錄為 24 小時。
- 應用程式一致快照集的頻率為 60 分鐘。

[深入瞭解複寫設定](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings)。

### <a name="what-is-a-crash-consistent-recovery-point"></a>什麼是當機時保持一致復原點？

當機時保持一致復原點具有磁片上的資料，就像您在快照集期間從伺服器提取電源線一樣。 當建立快照集時，損毀一致復原點不會包含記憶體中的任何專案。

現今，大多數應用程式都可以妥善地從當機時保持一致快照集復原。 對無資料庫作業系統及檔案伺服器、DHCP 伺服器和列印伺服器之類的應用程式而言，通常使用當機時保持一致復原點就已足夠。

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>當機時保持一致復原點產生的頻率為何？

Site Recovery 會每 5 分鐘建立一次當機時保持一致復原點。

### <a name="what-is-an-application-consistent-recovery-point"></a>什麼是應用程式一致復原點？

應用程式一致復原點是從應用程式一致快照集建立的。 應用程式一致復原點會捕捉與當機時保持一致快照集相同的資料，同時也會捕捉記憶體中的資料和處理中的所有交易。

由於有額外的內容，因此應用程式一致的快照集最為相關，而且會佔用最長的時間。 建議您針對資料庫作業系統和 SQL Server 之類的應用程式，使用應用程式一致復原點。

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>應用程式一致復原點對應用程式效能有何影響？

應用程式一致復原點會在記憶體中和處理中捕捉所有資料。 因為復原點會捕捉該資料，所以需要在 Windows 上磁碟區陰影複製服務類似的架構，以停止應用程式。 如果捕捉程式很頻繁，當工作負載已忙碌時，可能會影響效能。 對於非資料庫工作負載，我們不建議使用低頻率的應用程式一致復原點。 即使是針對資料庫工作負載，1小時就夠了。

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>應用程式一致復原點產生的最小頻率為何？

Site Recovery 可以建立應用程式一致復原點，最小頻率為1小時。

### <a name="how-are-recovery-points-generated-and-saved"></a>復原點要如何產生和儲存？

若要瞭解 Site Recovery 如何產生復原點，讓我們看一個複寫原則的範例。 此複寫原則的復原點具有24小時的保留時間，以及1小時的應用程式一致頻率快照集。

Site Recovery 會每 5 分鐘建立一次當機時保持一致復原點。 您無法變更此頻率。 在過去一小時內，您可以選擇12個當機時保持一致的點和1個應用程式一致點。 隨著時間進展，Site Recovery 會剪除過去一小時後的所有復原點，而且每小時只會儲存1個復原點。

以下螢幕擷取畫面說明此範例。 在螢幕擷取畫面中：

- 在過去一小時內，有個復原點的頻率為5分鐘。
- 超過過去一小時，Site Recovery 只會保留1個復原點。

   ![產生的復原點清單](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>最多可復原至多久之前？

您可以使用的最舊復原點為 72 小時。

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>我有24小時的複寫原則。 如果問題導致 Site Recovery 無法產生復原點超過24小時，會發生什麼事？ 我先前的復原點是否會遺失？

不會，Site Recovery 會保留您先前所有的復原點。 根據復原點的保留時間範圍，Site Recovery 只會在產生新的點時，才會取代最舊的點。 由於問題的原因，Site Recovery 無法產生任何新的復原點。 在有新的復原點之前，所有舊的點都會在您到達保留視窗之後保留。

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>在 VM 上啟用複寫之後，我要如何變更複寫原則？

移至 [Site Recovery 保存庫] > [Site Recovery 基礎結構] > [複寫原則]。 選取您想要編輯的原則，然後儲存變更。 此外，任何變更都會套用到現有的所有複寫。

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>所有復原點都是 VM 的完整複本？還是會有差異？

所產生的第一個復原點會具有完整複本。 所有後續復原點則具有差異變更。

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>增長復原點的保留週期是否會增加儲存體成本？

是，如果您將保留期限從24小時增加到72小時，Site Recovery 會將復原點儲存為額外的48小時。 增加的時間將產生儲存體費用。 例如，單一復原點可能會有 10 GB 的差異變更，每個月的每 GB 成本為 $0.16。 額外費用會是每月 $1.60 ×48。

## <a name="multi-vm-consistency"></a>多 VM 一致性

### <a name="what-is-multi-vm-consistency"></a>什麼是多部 VM 一致性？

多部 VM 一致性可確保所有複寫的虛擬機器上的復原點都是一致的。

Site Recovery 提供**多 VM 一致性**選項，它會建立所有機器的複寫群組。

當您故障復原虛擬機器時，它們會有共用的損毀一致和應用程式一致復原點。

請完成本教學課程，以[啟用多部 VM 一致性](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm)。

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>我可以故障處理多部 VM 一致性複寫群組內的單一虛擬機器嗎？

當您選取 [**多部 VM 一致性**] 選項時，您會看到應用程式相依于群組內的所有虛擬機器。 不允許單一虛擬機器容錯移轉。

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>我可以將多少個虛擬機器複寫為多部 VM 一致性複寫群組的一部分？

您可以透過一個複寫群組一起複寫 16 部虛擬機器。

### <a name="when-should-i-enable-multi-vm-consistency"></a>何時應該啟用多部 VM 一致性？

由於多部 VM 一致性會耗用大量 CPU，因此可能會影響工作負載效能。 只有當機器執行相同的工作負載，而且您需要跨多部電腦的一致性時，才使用多部 VM 一致性。 例如，如果您在應用程式中有兩個 SQL Server 實例和兩個 web 伺服器，則只有 SQL Server 實例的多部 VM 一致性。

## <a name="failover"></a>容錯移轉

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>如何在 Azure Vm 的目的地區域中確保容量？

Site Recovery 小組和 Azure 容量管理小組會規劃足夠的基礎結構容量。 當您開始進行容錯移轉時，小組也有助於確保受 Site Recovery 保護的 VM 實例會部署到目的地區域。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？

容錯移轉並非自動發生。 您可以在入口網站中按一下一次來啟動容錯移轉，也可以使用[PowerShell](azure-to-azure-powershell.md)來觸發容錯移轉。

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>我可以在容錯移轉之後保留公用 IP 位址嗎？

您無法在容錯移轉之後保留生產應用程式的公用 IP 位址。

當您在容錯移轉過程中顯示工作負載時，您必須將 Azure 公用 IP 資源指派給工作負載。 Azure 公用 IP 資源必須在目的地區域中提供。 您可以手動指派 Azure 公用 IP 資源，也可以使用復原方案將它自動化。 瞭解如何[在容錯移轉之後設定公用 IP 位址](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)。  

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>我可以在容錯移轉期間保留私人 IP 位址嗎？

是，您可以保留私人 IP 位址。 根據預設，當您為 Azure VM 啟用災害復原時，Site Recovery 會根據來源資源設定建立目標資源。 若為使用靜態 IP 位址設定的 Azure 虛擬機器，Site Recovery 會嘗試針對目標 VM 布建相同的 IP 位址（如果未使用的話）。
瞭解如何[在容錯移轉期間保留 IP 位址](site-recovery-retain-ip-azure-vm-failover.md)。

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>在容錯移轉之後，為什麼伺服器會指派新的 IP 位址？

Site Recovery 會在容錯移轉時嘗試提供 IP 位址。 如果另一部虛擬機器正在使用該位址，Site Recovery 就會將下一個可用的 IP 位址設定為目標。

深入瞭解如何[設定虛擬網路的網路對應和 IP 位址](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)。

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>什麼是**最新 (最低 RPO)** 復原點？

[**最新（最低 RPO）** ] 選項會先處理已傳送至 Site Recovery 的所有資料。 在服務處理資料之後，它會為每個 VM 建立復原點，然後再容錯移轉至 VM。 此選項提供最低的復原點目標（RPO）。 在容錯移轉之後建立的 VM，會在觸發容錯移轉時，將所有資料複寫到 Site Recovery。

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>**最新 (最低 RPO)** 復原點對容錯移轉 RTO 是否有影響？

是的。 Site Recovery 會在容錯移轉之前處理所有擱置中的資料，因此相較于其他選項，此選項的復原時間目標（RTO）更高。

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>復原點中的**最新處理**選項是什麼意思？

[**最新處理**的] 選項會將方案中的所有 vm 故障處理到 Site Recovery 處理的最新復原點。 若要查看特定 VM 的最新復原點，請檢查 VM 設定中的 [最新復原點]。 此選項提供低 RTO，因為不會花費任何時間來處理未處理的資料。

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>如果我的主要區域遇到非預期的中斷，會發生什麼事？

您可以在中斷之後觸發容錯移轉。 Site Recovery 不需要來自主要區域的連線來進行容錯移轉。

### <a name="what-is-an-rto-of-a-vm-failover"></a>VM 容錯移轉的 RTO 是什麼？

Site Recovery 的[RTO SLA 為2小時](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)。 不過，大部分的情況下，Site Recovery 會在幾分鐘內故障處理虛擬機器。 您可以移至容錯移轉工作來計算 RTO，這會顯示啟動 VM 所需的時間。 如需復原方案的 RTO，請參閱下一節。

## <a name="recovery-plans"></a>復原方案

### <a name="what-is-a-recovery-plan"></a>什麼是復原方案？

Site Recovery 中的復原方案會協調 VM 的容錯移轉復原。 這有助於讓復原「保持一致精確」、「可重複執行」及「自動化」。 復原方案滿足下列需求：

- 定義一組會一起容錯移轉的虛擬機器
- 定義虛擬機器間的相依性，讓應用程式能夠正確地啟動
- 搭配自訂的手動動作自動執行復原，以完成虛擬機器容錯移轉以外的工作

深入瞭解[如何建立復原計畫](site-recovery-create-recovery-plans.md)。

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>復原方案中是如何進行排序的？

在復原方案中，您可以建立多個群組來進行排序。 每個群組會各在一個時間容錯移轉。 屬於相同群組的虛擬機器會一起故障，然後再進行另一個群組。 若要了解如何使用復原方案來建立應用程式模型，請參閱[關於復原方案](recovery-plan-overview.md#model-apps)。

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>如何找到復原方案的 RTO？

若要檢查復原方案的 RTO，請為復原方案進行測試容錯移轉，然後移至 [Site Recovery 作業]。
在下列範例中，請參閱作業**SAPTestRecoveryPlan**。 此作業需要8分鐘和59秒的時間來故障切換所有虛擬機器，並執行指定的動作。

![Site Recovery 作業清單](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>我是否可以將自動化 Runbook 新增至復原方案？

是的，您可以將「Azure 自動化」Runbook 整合至您的復原方案。 深入瞭解如何[新增 Azure 自動化 runbook](site-recovery-runbook-automation.md)。

## <a name="reprotection-and-failback"></a>重新保護和容錯回復

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>我已從主要區域故障復原到嚴重損壞修復區域。 DR 區域中的 Vm 是否會自動受到保護？

No。 當您將 Azure VM 從一個區域[容錯移轉](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)至另一個區域時，VM 會在 DR 區域中以未受保護的狀態啟動。 若要將 VM 容錯回復到到主要區域，您必須[重新保護](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect)次要區域中的 VM。

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>進行重新保護時，Site Recovery 是否會從次要區域將完整的資料複寫到主要區域？

這需視情況而定。 如果來源區域 VM 存在，則只會同步處理來源磁片與目標磁片之間的變更。 Site Recovery 會藉由比較磁碟來計算出差異，然後才傳輸資料。 此程序通常需要幾小時的時間。 如需重新保護期間所發生狀況的詳細資訊，請參閱將已[故障的 AZURE VM 實例重新保護到主要區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)。

### <a name="how-much-time-does-it-take-to-fail-back"></a>容錯回復需要花費多久時間？

重新保護之後，容錯回復所花的時間，會與從主要區域容錯移轉到次要區域所需的時間相同。

## <a name="capacity"></a>存儲

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>如何在 Azure Vm 的目的地區域中確保容量？

Site Recovery 小組和 Azure 容量管理小組會規劃足夠的基礎結構容量。 當您開始進行容錯移轉時，小組也有助於確保受 Site Recovery 保護的 VM 實例會部署到目的地區域。

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery 與保留實例搭配使用嗎？

是，您可以在嚴重損壞修復區域中購買[保留的 Azure vm](https://azure.microsoft.com/pricing/reserved-vm-instances/) ，Site Recovery 容錯移轉作業將會使用它們。 不需要任何其他設定。

## <a name="security"></a>安全性

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>複寫資料會傳送到 Site Recovery 服務嗎？

否，Site Recovery 不會攔截複寫的資料，也不會有任何有關您 Vm 上執行之內容的資訊。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。  

Site Recovery 是 ISO 27001:2013、27018、HIPAA 和 DPA 認證。 服務正在進行 SOC2 和 FedRAMP JAB 評量。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery 會將複寫加密嗎？

是，同時支援傳輸中的加密和[Azure 中](https://docs.microsoft.com/azure/storage/storage-service-encryption)待用的加密。

## <a name="next-steps"></a>後續步驟

- [審查 Azure 對 Azure 支援的需求](azure-to-azure-support-matrix.md)。
- [設定 azure 到 azure](azure-to-azure-tutorial-enable-replication.md)的複寫。
- 如果您在閱讀本文後有問題，請將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)。
