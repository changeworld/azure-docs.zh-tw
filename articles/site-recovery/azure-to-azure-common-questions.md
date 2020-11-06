---
title: 使用 Azure Site Recovery 進行 Azure VM 災害復原的常見問題
description: 本文回答在您使用 Azure Site Recovery 時，有關 Azure VM 災害復原的常見問題。
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: d4fa7348df647cf699ebd55dd6415a79454ab5f1
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397942"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>常見問題：Azure 至 Azure 災害復原

本文將使用 [Azure Site Recovery](site-recovery-overview.md) 服務，回答 azure vm 到另一個 azure 區域的嚴重損壞修復常見問題。

## <a name="general"></a>一般

### <a name="how-is-site-recovery-priced"></a>Site Recovery 是如何定價的？

瞭解 Azure VM 嚴重損壞修復的 [成本](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) 。

### <a name="how-does-the-free-tier-work"></a>免費層的運作方式為何？

每個受 Site Recovery 保護的實例在前31天的保護中是免費的。 在該期間之後，每個實例的保護都會以 [定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)中摘要說明的費率計算。 您可以使用 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=site-recovery)來預估成本。

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>前31天是否會產生其他 Azure 費用？

是。 即使受保護的執行個體前 31 天的 Azure Site Recovery 免費，您仍有可能需要負擔 Azure 儲存體、儲存體交易與資料傳輸的費用。 復原的 VM 也可能會產生 Azure 計算費用。 G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>如何? 開始使用 Azure VM 嚴重損壞修復？

1. [瞭解](azure-to-azure-architecture.md) Azure VM 嚴重損壞修復架構。
2. [檢閱](azure-to-azure-support-matrix.md)支援需求。
3. [設定](azure-to-azure-how-to-enable-replication.md) Azure vm 的嚴重損壞修復。
4. 使用測試容錯移轉執行嚴重損壞[修復演練](azure-to-azure-tutorial-dr-drill.md)。
5. [執行完整容錯移轉](azure-to-azure-tutorial-failover-failback.md) 至次要 Azure 區域。
6. 從次要區域[容錯回復](azure-to-azure-tutorial-failback.md)至主要區域。

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>如何確保目的地區域中的容量？

Site Recovery 小組和 Azure 容量管理小組，規劃足夠的基礎結構容量。 當您開始進行容錯移轉時，小組也有助於確保受 Site Recovery 保護的 VM 實例會部署至目的地區域。

## <a name="replication"></a>Replication

### <a name="can-i-replicate-vms-with-disk-encryption"></a>我可以使用磁片加密來複寫 Vm 嗎？

可以。 Site Recovery 針對已啟用 Azure 磁碟加密 (ADE) 的 Vm 支援災難復原。 當您啟用複寫時，Azure 會在使用者內容中，將所有必要的磁片加密金鑰和秘密從源區域複製到目的地區域。 如果您沒有必要的許可權，您的安全性系統管理員可以使用腳本來複製金鑰和秘密。

- Site Recovery 針對執行 Windows 的 Azure Vm 支援 ADE。
- Site Recovery 支援：
    - ADE 版本0.1，其架構需要 Azure Active Directory (Azure AD) 。
    - ADE 版本1.1，不需要 Azure AD。 針對1.1 版，Windows Azure Vm 必須有受控磁片。
    - [深入了解](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema)。 關於延伸模組架構。

[深入了解](azure-to-azure-how-to-enable-replication-ade-vms.md)如何啟用已加密 VM 的複寫。

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>我可以從不同的資源群組選取自動化帳戶嗎？

當您允許 Site Recovery 管理在已複寫的 Azure Vm 上執行的行動服務擴充功能更新時，它會透過 Azure 自動化帳戶，部署 Azure 服務) 所使用的全域 runbook (。 您可以使用 Site Recovery 建立的自動化帳戶，或選取使用現有的自動化帳戶。 

目前，在入口網站中，您只能選取與保存庫位於相同資源群組中的自動化帳戶。 您可以使用 PowerShell 從不同的資源群組中選取自動化帳戶。 [深入了解](azure-to-azure-autoupdate.md#enable-automatic-updates)。

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>如果我使用的客戶自動化帳戶不在保存庫資源群組中，是否可以刪除預設的 runbook？

是的，如果不需要，您可以將它刪除。 

### <a name="can-i-replicate-vms-to-another-subscription"></a>我可以將 VM 複寫至另一個訂用帳戶嗎？

是，您可以將 Azure Vm 複寫到相同 Azure AD 租使用者內的任何訂用帳戶。 當您啟用 Vm 的嚴重損壞修復時，根據預設，所顯示的目標訂用帳戶是來源 VM 的訂用帳戶。 您可以修改目標訂用帳戶，以及從選取的訂用帳戶自動填入其他設定 (例如資源群組和虛擬網路) ）。

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>我可以將可用性區域中的 Vm 複寫到另一個區域嗎？

是，您可以將可用性區域中的 Vm 複寫到另一個 Azure 區域。 您可以將目標 VM 部署為單一實例、可用性設定組或可用性區域（如果目的地區域支援的話）。 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>我可以將非區域 Vm 複寫到相同區域內的區域嗎？ 

入口網站不支援此功能。 您可以使用 REST API/PowerShell 來執行此動作。

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>是否可以將已分區的 Vm 複寫到相同區域中的不同區域？

這項支援僅限於幾個區域。 [深入了解](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)。

### <a name="can-i-exclude-disks-from-replication"></a>我可以從複寫中排除磁片嗎？

是的，您可以在設定複寫時使用 PowerShell 來排除磁片。 [深入了解](azure-to-azure-exclude-disks.md)。

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>是否可以將新的磁片複寫到已複寫的 Vm？

針對具有受控磁片的已複寫 Vm，您可以新增磁片並為其啟用複寫。 當您新增磁片時，複寫的 VM 會顯示一則警告訊息，指出 VM 上有一或多個磁片可供保護。 

- 如果您為新增的磁片啟用複寫，則在初始複寫之後，警告就會消失。
- 如果您不想要為磁片啟用複寫，可以關閉警告。
- 如果您容錯移轉已新增磁片的 VM，複寫點會顯示可用來復原的磁片。 例如，如果您將第二個磁片新增至具有一個磁片的 VM，則在新增之前建立的複寫點會顯示為「1個2個磁片」。

Site Recovery 不支援從已複寫的 VM 「熱移除」磁片。 如果您移除 VM 磁片，您必須停用 VM 的複寫，然後再重新啟用複寫。

### <a name="how-often-can-i-replicate-to-azure"></a>複寫到 Azure 的頻率為何？

從 Azure VM 到另一個 Azure 區域的複寫會持續執行。 [深入瞭解](./azure-to-azure-architecture.md#replication-process) 複寫的運作方式。

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>我是否可以在一個區域內複寫虛擬機器？ 

您無法使用 Site Recovery 來複寫區域內的磁片。

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>我可以將 VM 複寫至任何 Azure 區域嗎？

您可以複製和復原相同的地理叢集內任何兩個區域之間的 VM。 定義地理叢集時皆已考量資料延遲和主權範圍。 [深入瞭解](./azure-to-azure-support-matrix.md#region-support) 區域支援。

### <a name="does-site-recovery-need-internet-connectivity"></a>Site Recovery 是否需要網際網路連線？

否，但 Vm 需要 Site Recovery Url 和 IP 範圍的存取權。 [深入了解](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls)。

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>我可以跨資源群組複寫階層式應用程式嗎？

是的，您可以複寫應用程式，並將嚴重損壞修復設定保留在不同的資源群組中。

例如，如果應用程式在不同的資源群組中有三個階層 (應用程式/資料庫/web) ，您需要啟用複寫三次，以保護所有層級。 Site Recovery 會將三個層級複寫至三個不同的資源群組。

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>我是否可以跨資源群組移動儲存體帳戶？

否，不支援這種情況。 如果您不小心將儲存體帳戶移至不同的資源群組，並刪除原始資源群組，您可以建立與舊資源群組同名的新資源群組，然後將儲存體帳戶移至此資源群組。

## <a name="replication-policy"></a>複寫原則

### <a name="what-is-a-replication-policy"></a>什麼是複寫原則？

複寫原則會定義復原點的保留歷程記錄，以及應用程式一致快照的頻率。  Site Recovery 建立預設的複寫原則，如下所示：

- 保留復原點24小時。
- 每四個小時取得應用程式一致快照集。

[深入瞭解](azure-to-azure-how-to-enable-replication.md#customize-target-resources) 複寫設定。

### <a name="whats-a-crash-consistent-recovery-point"></a>什麼是損毀一致復原點？

當機時保持一致復原點包含磁片上的資料，就像您在快照集期間從伺服器提取電源線一樣。 在建立快照集時，它不會包含在記憶體中的任何事物。

現今，大部分的應用程式都可以從損毀一致的快照集復原。 損毀一致復原點通常就已足夠用於非資料庫作業系統，以及檔案伺服器、DHCP 伺服器和列印伺服器等應用程式。

Site Recovery 每隔五分鐘會自動建立一個損毀一致復原點。

### <a name="whats-an-application-consistent-recovery-point"></a>什麼是應用程式一致復原點？

應用程式一致復原點可從應用程式一致快照集建立。 它們會捕捉與當機時保持一致快照集相同的資料，以及在記憶體中捕捉資料，以及處理所有交易。

由於有額外的內容，因此，應用程式一致的快照集會最相關，並花費最長的時間。 我們建議資料庫作業系統和應用程式（例如 SQL Server）的應用程式一致復原點。 針對 Windows，應用程式一致快照集會使用磁碟區陰影複製服務 (VSS) 。

### <a name="do-app-consistent-recovery-points-impact-performance"></a>應用程式一致復原點是否會影響效能？

 由於應用程式一致復原點會在記憶體和進程中捕捉所有資料，如果它們經常進行，它可能會在工作負載已忙碌時影響效能。 我們不建議您針對非資料庫工作負載進行太頻繁的拍攝。 即使是資料庫工作負載，一小時的時間也應該足夠。

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>產生應用程式一致復原點的最小頻率為何？

Site Recovery 可以建立應用程式一致的復原點，最小頻率為1小時。

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>是否可以啟用 Linux Vm 的應用程式一致複寫？

可以。 適用于 Linux 的行動代理程式支援應用程式一致性的自訂腳本。 代理程式會使用前置和後置選項的自訂腳本。 [深入了解](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>復原點要如何產生和儲存？

若要瞭解 Site Recovery 如何產生復原點，讓我們使用範例。 

- 複寫原則會保留復原點24小時，並每小時取得應用程式一致的頻率快照。
- Site Recovery 每隔五分鐘會建立一個損毀一致復原點。 您無法變更此頻率。
- Site Recovery 在一小時後將復原點剪除，每小時儲存一個點。

因此，在過去一小時內，您可以選擇12個損毀一致的點，以及一個應用程式一致的點（如下圖所示）。

   ![產生的復原點清單](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>最多可復原至多久之前？

您可以使用的最舊復原點為 72 小時。

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>如果 Site Recovery 無法產生超過24小時的復原點，會發生什麼事？ 

如果您有24小時的複寫原則，而且 Site Recovery 無法產生超過24小時的復原點，則舊的復原點會保留下來。 Site Recovery 只有在產生新的點時，才會取代最舊的時間點。 在有新的復原點之前，所有舊的點都會在您到達保留期間之後繼續進行。

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>可以在啟用複寫之後變更複寫原則嗎？

可以。 在保存庫 > **Site Recovery 基礎結構**  >  **複寫原則** ，請選取並編輯原則。 變更也適用于現有的原則。

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>所有復原點都是完整的 VM 複本嗎？

所產生的第一個復原點會具有完整複本。 後續復原點會有差異變更。

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>復原點保留期增加儲存體成本會增加嗎？

可以。 例如，如果您將保留從24小時增加到72，Site Recovery 會將復原點儲存為額外的48小時。 新增的時間會產生儲存體變更。 舉例來說，如果單一復原點的差異變更為 10 GB，每個月每 GB 的成本為 $0.16，則額外費用會是 $1.60 × 48/月。

## <a name="multi-vm-consistency"></a>多 VM 一致性

### <a name="what-is-multi-vm-consistency"></a>什麼是多 VM 一致性？

多重 VM 一致性可確保復原點在複寫的虛擬機器之間保持一致。

- 當您啟用多部 VM 一致性時，Site Recovery 會在啟用選項的情況下，建立所有電腦的複寫群組。 
- 當您容錯移轉複寫群組中的機器時，它們具有共用的損毀一致和應用程式一致復原點。

[瞭解](azure-to-azure-tutorial-enable-replication.md#enable-replication) 如何啟用多 VM 一致性。

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>是否可以容錯移轉複寫群組中的單一 VM？

否。 當您啟用多部 VM 一致性時，它會推斷應用程式相依于複寫群組中的所有 Vm，且不允許單一 VM 容錯移轉。

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>我可以將多少部 VM 一起複寫到群組中？

您可以在複寫群組中同時複寫16個 Vm。

### <a name="when-should-i-enable-multi-vm-consistency"></a>何時應該啟用多 VM 一致性？

多 VM 一致性會耗用大量 CPU，而啟用它可能會影響工作負載效能。 只有在 Vm 執行相同的工作負載，且您需要在多部電腦之間保持一致性時，才會啟用。 例如，如果您的應用程式中有兩個 SQL Server 實例和兩個 web 伺服器，請僅針對 SQL Server 的實例啟用多部 VM 一致性。

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>是否可以將複寫 VM 新增至複寫群組？

當您為 VM 啟用複寫時，可以將它新增至新的複寫群組，或新增至現有的群組。 您無法將已複寫的 VM 新增至群組。 
 
## <a name="failover"></a>容錯移轉

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>如何確保目的地區域中的容量？

Site Recovery 小組和 Azure 容量管理小組規劃足夠的基礎結構容量。 當您開始進行容錯移轉時，小組也有助於確保受 Site Recovery 保護的 VM 實例可部署至目的地區域。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？

容錯移轉並非自動發生。 您可以在入口網站中按一下單鍵來啟動容錯移轉，或使用  [PowerShell](azure-to-azure-powershell.md) 來觸發容錯移轉。

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>我可以在容錯移轉之後保留公用 IP 位址嗎？

您無法在容錯移轉後保留生產應用程式的公用 IP 位址。

當您在容錯移轉程式中顯示工作負載時，您必須將 Azure 公用 IP 位址資源指派給它。 資源必須可在目的地區域中使用。 您可以手動指派 Azure 公用 IP 位址資源，也可以使用復原方案將它自動化。 [瞭解](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan) 如何在容錯移轉之後設定公用 IP 位址。

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>我可以在容錯移轉後保留私人 IP 位址嗎？

可以。 根據預設，當您啟用 Azure Vm 的嚴重損壞修復時，Site Recovery 會根據來源資源設定建立目標資源。 針對使用靜態 IP 位址設定的 Azure VM，Site Recovery 會嘗試為目標 VM 佈建相同的 IP 位址 (如果它未在使用中)。
[深入瞭解如何](site-recovery-retain-ip-azure-vm-failover.md) 在容錯移轉後保留 IP 位址。

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>為何 VM 在容錯移轉之後被指派新的 IP 位址？

Site Recovery 會在容錯移轉時嘗試提供 IP 位址。 如果另一個 VM 使用該位址，Site Recovery 會將下一個可用的 IP 位址設定為目標。

[深入瞭解如何](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms) 設定虛擬網路的網路對應和 IP 位址。

### <a name="whats-the-latest-recovery-point"></a>*最新* 的復原點是什麼？

*最新的 (最低 RPO)* 復原點選項會執行下列動作：

1. 它會先處理已傳送至 Site Recovery 的所有資料。
2. 在服務處理資料之後，它會為每部 VM 建立復原點，然後再容錯移轉至 VM。 此選項提供最低的復原點目標 (RPO)。
3. 在容錯移轉後建立的 VM 會在觸發容錯移轉時，將所有的資料複寫至 Site Recovery。

### <a name="do-latest-recovery-points-impact-failover-rto"></a>*最新* 的復原點是否會影響容錯移轉 RTO？

可以。 Site Recovery 會在容錯移轉之前處理所有擱置中的資料，因此此選項的復原時間目標 (RTO) 比其他選項更高。

### <a name="whats-the-latest-processed-recovery-option"></a>*最新處理* 的復原選項是什麼？

*最新處理* 的選項會執行下列動作：

1. 它會將所有 Vm 容錯移轉到 Site Recovery 所處理的最新復原點。 此選項提供低 RTO，因為不會花費任何時間來處理未處理的資料。

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>如果主要區域發生未預期的中斷，該怎麼辦？

您可以啟動容錯移轉。 Site Recovery 不需要來自主要區域的連線即可執行容錯移轉。

### <a name="what-is-the-rto-of-a-vm-failover"></a>VM 容錯移轉的 RTO 為何？

Site Recovery 有 [兩個小時](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)的 RTO SLA。 大部分的情況下，Site Recovery 會在幾分鐘內容錯移轉 Vm。 若要計算 RTO，請檢查容錯移轉作業，這會顯示啟動 VM 所花的時間。 

## <a name="recovery-plans"></a>復原計畫

### <a name="whats-a-recovery-plan"></a>什麼是復原方案？

Site Recovery 中的復原 [方案](site-recovery-create-recovery-plans.md) 會協調 vm 的容錯移轉和復原。 它有助於以一致、可重複且自動化的方式進行復原。 它會執行下列工作：

- 定義一組一起容錯移轉的 Vm
- 定義 Vm 之間的相依性，讓應用程式能夠正確地出現。
- 自動復原，可選擇針對 VM 容錯移轉以外的工作自訂手動動作。 


### <a name="how-does-sequencing-work"></a>排序的運作方式為何？

在復原方案中，您可以建立多個 VM 群組進行排序。 群組會一次容錯移轉，因此屬於相同群組的 Vm 會一起容錯移轉。 [深入了解](recovery-plan-overview.md#model-apps)。

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>如何找到復原方案的 RTO？

若要檢查復原方案的 RTO，請針對復原方案進行測試容錯移轉。 在 **Site Recovery 作業** 中，檢查測試容錯移轉持續時間。 在範例螢幕擷取畫面中， **SAPTestRecoveryPlan** 測試容錯移轉作業花費了8分鐘和59秒。

![列出作業，顯示 RTO 的測試容錯移轉持續時間](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>是否可以將自動化 runbook 新增至復原方案？

可以。 [深入了解](site-recovery-runbook-automation.md)。

## <a name="reprotection-and-failback"></a>重新保護和容錯回復

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>在容錯移轉之後，次要區域中的 Vm 是否會自動受到保護？ 

否。 當您將 Vm 從一個區域容錯移轉至另一個區域時，Vm 會在目標嚴重損壞修復區域中以未受保護的狀態啟動。 若要重新保護次要區域中 [的 vm，您可以啟用](./azure-to-azure-how-to-reprotect.md) 複寫回主要區域。

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>當我重新保護時，是否將所有資料從次要區域複寫到主要區域？ 

這取決於。如果來源區域 VM 存在，則只會同步處理來源磁片與目標磁片之間的變更。 Site Recovery 比較磁片與不同的磁片，然後傳輸資料。 此程序通常需要幾小時的時間。 [深入了解](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)。

### <a name="how-long-does-it-take-fail-back"></a>容錯回復需要多久的時間？

重新保護之後，容錯回復大約需要從主要區域容錯移轉到次要區域所需的時間量。

## <a name="capacity"></a><a name="capacity"></a>容量

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>如何確保目的地區域中的容量？

Site Recovery 小組和 Azure 容量管理小組會規劃足夠的基礎結構容量。 當您開始進行容錯移轉時，小組也有助於確保受 Site Recovery 保護的 VM 實例可部署至目的地區域。

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery 是否會使用保留執行個體？

是的，您可以在故障復原區域中購買 [保留的 Azure vm](https://azure.microsoft.com/pricing/reserved-vm-instances/) ，Site Recovery 容錯移轉作業使用它們。 不需要任何其他設定。

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
