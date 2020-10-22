---
title: 關於 Azure Site Recovery 中的容錯移轉和容錯回復
description: 瞭解 Azure Site Recovery 中的容錯移轉和 failable。
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 3617683200aa3ffba08061b70993613fd0cc7241
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369874"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>關於內部部署嚴重損壞修復容錯移轉/容錯回復

本文提供在內部部署機器到 Azure 的災難復原期間，使用 [Azure Site Recovery](site-recovery-overview.md)的容錯移轉和容錯回復的總覽。

## <a name="recovery-stages"></a>復原階段

Site Recovery 中的容錯移轉和容錯回復有四個階段：

- **階段1：從內部部署容錯移轉**：為內部部署機器設定複寫至 Azure 之後，當您的內部部署網站停止運作時，您會將這些機器容錯移轉至 azure。 容錯移轉之後，系統會從複寫的資料建立 Azure VM。
- **階段2：重新保護 Azure vm**：在 azure 中，您可以重新保護 azure vm，使其開始複寫回到內部部署網站。 內部部署 VM (（如果可用) 在重新保護期間關閉），以協助確保資料一致性。
- **第3階段：從 Azure 容錯移轉**：當您的內部部署網站再次以正常方式執行時，您會執行另一個容錯移轉，這次將 Azure vm 容錯回復至內部部署網站。 您可以容錯回復至原始位置，也就是您容錯移轉的來源位置或替代位置。
- **階段4：** 重新保護內部部署機器：容錯回復之後，請再次啟用將內部部署機器複寫至 Azure 的複寫。

## <a name="failover"></a>容錯移轉

您會在商務持續性和嚴重損壞修復 (BCDR) 策略中執行容錯移轉。

- 在 BCDR 策略的第一個步驟中，您會持續將內部部署機器複寫至 Azure。 使用者會存取在內部部署來源機器上執行的工作負載和應用程式。
- 如果有需要，例如在內部部署中斷時，您會將複寫機器容錯移轉至 Azure。 使用複寫的資料建立 Azure Vm。
- 針對商務持續性，使用者可以繼續存取 Azure Vm 上的應用程式。

容錯移轉是兩階段的活動：

- **容錯移轉**：使用選取的復原點建立和顯示 Azure VM 的容錯移轉。
- **認可**：在容錯移轉之後，您會在 Azure 中驗證 VM：
    - 然後，您可以將容錯移轉認可至選取的復原點，或為認可選取不同的點。
    - 認可容錯移轉之後，就無法變更復原點。


## <a name="connect-to-azure-after-failover"></a>在容錯移轉之後連接到 Azure

若要連線到使用 RDP/SSH 進行容錯移轉之後建立的 Azure Vm，有一些需求。

**容錯移轉** | **位置** | **動作**
--- | --- | ---
**Azure VM (Windows (** | 在容錯移轉前的內部部署機器上 | 透過**網際網路存取**：啟用 RDP。 請確定已為**公用**新增 TCP 和 UDP 規則，而且允許**Windows 防火牆**  >  **允許的應用程式**中的所有設定檔使用 RDP。<br/><br/> 透過**站對站 VPN 存取**：在電腦上啟用 RDP。 檢查**Windows 防火牆**  ->  **允許的應用程式和功能**（適用于**網域和專用**網）是否允許 RDP。<br/><br/>  確定作業系統的 SAN 原則已設為 **OnlineAll**。 [深入了解](https://support.microsoft.com/kb/3031135)。<br/><br/> 觸發容錯移轉時，請確定 VM 上沒有任何暫止的 Windows 更新。 當您容錯移轉時，Windows Update 可能會啟動，而且您必須等到更新完成後才能登入 VM。
**執行 Windows 的 Azure VM** | 在容錯移轉後的 Azure VM 上 |  [新增 VM 的公用 IP 位址](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr)。<br/><br/> 已容錯移轉的 VM (上的網路安全性群組規則，以及其所連線的 Azure 子網) 必須允許 RDP 埠的連入連線。<br/><br/> 勾選 [開機診斷]**** 以確認 VM 的螢幕擷取畫面。 如果您無法連線，請檢查 VM 是否正在執行，並檢查 [疑難排解秘訣](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。
**執行 Linux 的 Azure VM** | 在容錯移轉前的內部部署機器上 | 確定 VM 上的安全殼層服務已設定為在系統開機時自動啟動。<br/><br/> 請檢查防火牆規則是否允許 SSH 連線。
**執行 Linux 的 Azure VM** | 在容錯移轉後的 Azure VM 上 | 已容錯移轉的 VM 上的網路安全性群組規則 (以及它所連線的 Azure 子網路) 必須允許 SSH 連接埠的連入連線。<br/><br/> [新增 VM 的公用 IP 位址](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr)。<br/><br/> 勾選 [開機診斷]**** 以檢視 VM 的螢幕擷取畫面。<br/><br/>

## <a name="types-of-failover"></a>容錯移轉的類型

Site Recovery 提供不同的容錯移轉選項。

**容錯移轉** | **詳細資料** | **復原** | **工作流程**
--- | --- | --- | ---
**測試容錯移轉** | 用來執行演練，以驗證您的 BCDR 策略，而不會遺失任何資料或停止運作。| 在 Azure 中建立 VM 的複本，而不會影響進行中的複寫或生產環境。 | 1. 在單一 VM 或復原方案中的多個 Vm 上執行測試容錯移轉。<br/><br/> 2. 選取要用於測試容錯移轉的復原點。<br/><br/> 3. 選取 azure VM 在容錯移轉後建立時所在的 Azure 網路。 網路只用于測試容錯移轉。<br/><br/> 4. 確認鑽是否如預期般運作。 Site Recovery 會在演練期間自動清除在 Azure 中建立的 Vm。
**規劃的容錯移轉-Hyper-v**  | 通常用於計畫中的停機時間。<br/><br/> 來源 Vm 會關閉。 在起始容錯移轉之前，會同步處理最新的資料。 | 規劃的工作流程零資料遺失。 | 1. 規劃停機維護時段，並通知使用者。<br/><br/> 2. 讓使用者互動的應用程式離線。<br/><br/> 3. 使用最新的復原點起始計畫的容錯移轉。 如果電腦未關機或發生錯誤，則不會執行容錯移轉。<br/><br/> 4. 容錯移轉之後，請確認複本 Azure VM 在 Azure 中為作用中。<br/><br/> 5. 認可容錯移轉以完成。 認可動作會刪除所有復原點。
**容錯移轉-Hyper-v** | 通常會在發生未計畫的中斷或主要網站無法使用時執行。<br/><br/> 在起始容錯移轉之前，選擇性地關閉 VM，並同步處理最後的變更。  | 減少應用程式的資料遺失。 | 1. 起始您的 BCDR 計畫。 <br/><br/> 2. 起始容錯移轉。 指定 Site Recovery 是否應該在觸發容錯移轉之前，關閉 VM 並同步處理/複寫最新的變更。<br/><br/> 3. 您可以容錯移轉到多個復原點選項，如下表所摘要。<br/><br/> 如果您未啟用關閉 VM 的選項，或 Site Recovery 無法關閉 VM，則會使用最新的復原點。<br/>即使無法關閉電腦，仍會執行容錯移轉。<br/><br/> 4. 在容錯移轉之後，您會檢查 Azure 中的複本 Azure VM 是否為作用中。<br/> 如有需要，您可以從 [保留期間] 中選取24小時以外的不同復原點。<br/><br/> 5. 認可容錯移轉以完成。 認可動作會刪除所有可用的復原點。
**容錯移轉-VMware** | 通常會在發生未計畫的中斷或主要網站無法使用時執行。<br/><br/> （選擇性）指定 Site Recovery 應該嘗試觸發 VM 關機，並在起始容錯移轉之前同步處理和複寫最終變更。  | 減少應用程式的資料遺失。 | 1. 起始您的 BCDR 計畫。 <br/><br/> 2. 起始 Site Recovery 的容錯移轉。 指定 Site Recovery 是否應該在執行容錯移轉之前，嘗試觸發 VM 關機並進行同步處理。<br/> 即使無法關閉電腦，仍會執行容錯移轉。<br/><br/> 3. 容錯移轉之後，請確認複本 Azure VM 在 Azure 中為作用中。 <br/>如有需要，您可以從 [保留期間] 72 小時選取不同的復原點。<br/><br/> 5. 認可容錯移轉以完成。 認可動作會刪除所有復原點。<br/> 對於 Windows VM，Site Recovery 會在容錯移轉期間停用 VMware 工具。

## <a name="failover-processing"></a>容錯移轉處理

在某些情況下，容錯移轉需要額外的處理，這大約需要 8 到 10 分鐘才會完成。 您可能會發現下列各項的測試容錯移轉時間比較久：

* 執行的行動服務版本早於 9.8 的 VMware VM。
* 實體伺服器。
* VMware Linux VM。
* 視同實體伺服器受到保護的 Hyper-V VM。
* 未啟用 DHCP 服務的 VMware VM。
* 沒有下列開機驅動程式的 VMware VM：storvsc、vmbus、storflt、intelide、atapi。

## <a name="recovery-point-options"></a>復原點選項

在容錯移轉期間，您可以選取一些復原點選項。

**選項** | **詳細資料**
--- | ---
**最新 (最低 RPO) ** | 此選項提供最低的復原點目標 (RPO)。 它會先處理已傳送至 Site Recovery 服務的所有資料，以建立每個 VM 的復原點，再進行容錯移轉。 此復原點會在觸發容錯移轉時，將所有資料複寫至 Site Recovery。
**最新處理**  | 此選項會將 Vm 容錯移轉至 Site Recovery 所處理的最新復原點。 若要查看特定 VM 的最新復原點，請檢查 VM 設定中的 [最新復原點]。 此選項提供低 RTO (復原時間目標)，因為無須花費時間處理未處理的資料。
**最新應用程式一致** |  如果啟用應用程式一致復原點，此選項會將 Vm 容錯移轉至 Site Recovery 所處理的最新應用程式一致復原點。 檢查 VM 設定中的最新復原點。
**已處理的最新多 VM** | 此選項適用於一或多部 VM 已啟用多部 VM 一致性的復原計劃。 啟用這項設定的 VM 會容錯移轉至最新的一般多部 VM 一致復原點。 方案中的任何其他 Vm 都會容錯移轉至最新的已處理復原點。
**最新的多 VM 應用程式一致** |  此選項適用於一或多部 VM 已啟用多部 VM 一致性的復原計劃。 屬於最新一般多部 VM 應用程式一致復原點的複寫群組容錯移轉一部分的 VM。 對於最新應用程式一致復原點進行的其他 VM 容錯移轉。
**Custom** | 使用此選項可將特定 VM 容錯移轉至特定的復原點。 此選項不適用於復原方案。

> [!NOTE]
> 復原點無法遷移至其他復原服務保存庫。

## <a name="reprotectionfailback"></a>重新保護/容錯回復

在容錯移轉至 Azure 之後，複寫的 Azure Vm 會處於未受保護的狀態。

- 作為容錯回復至內部部署網站的第一個步驟，您必須啟動複寫至內部部署的 Azure Vm。 重新保護進程取決於您容錯移轉的機器類型。
- 將機器從 Azure 複寫到內部部署之後，您可以執行從 Azure 到內部部署網站的容錯移轉。
- 當機器再次在內部部署環境中執行時，您可以啟用複寫，以便將其複寫至 Azure 以進行嚴重損壞修復。

容錯回復的運作方式如下：

- 若要進行容錯回復，VM 需要至少一個復原點才能容錯回復。 在復原方案中，方案中的所有 Vm 都至少需要一個復原點。
- 建議您使用 **最新** 的復原點來容錯回復 (這是) 的損毀一致點。
    - 有一個應用程式一致的復原點選項。 在此情況下，單一 VM 會復原至其最新可用的應用程式一致復原點。 針對具有複寫群組的復原方案，每個複寫群組會復原至其一般可用的復原點。
    - 應用程式一致復原點可能會在一段時間後發生，而且資料可能會遺失。
- 從 Azure 容錯移轉至內部部署網站時，Site Recovery 會關閉 Azure Vm。 當您認可容錯移轉時，Site Recovery 會移除 Azure 中已容錯移轉的 Azure Vm。


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/實體重新保護/容錯回復

若要將 VMware 電腦和實體伺服器從 Azure 重新保護和容錯回復至內部部署，您需要容錯回復基礎結構，而且有一些需求。

- **Azure 中的暫時處理序伺服器**︰若要從 Azure 容錯回復，您可以設定 Azure VM 作為處理序伺服器，以處理來自 Azure 的複寫。 容錯回復完成後，您可以刪除此 VM。
- **VPN 連線**：若要容錯回復，您必須要有從 Azure 網路到內部部署網站的 VPN 連線 (或 ExpressRoute)。
- **個別的主要目標伺服器**︰根據預設，隨組態伺服器安裝的主要目標伺服器 (在內部部署 VMware VM 上) 會處理容錯回復。 如果您需要容錯回復大量流量，請針對此用途設定個別的內部部署主要目標伺服器。
- **容錯回復原則**︰若要複寫回到內部部署網站，您需要容錯回復原則。 當您建立從內部部署至 Azure 的複寫原則後，此原則會自動建立。
    - 此原則會自動與設定伺服器產生關聯。
    - 您無法編輯此原則。
    - 原則值： RPO 閾值-15 分鐘;復原點保留期-24 小時;應用程式一致的快照頻率-60 分鐘。

深入瞭解 VMware/實體重新保護和容錯回復：
- [查看](vmware-azure-reprotect.md#before-you-begin) 重新保護和容錯回復的其他需求。
- 在 Azure 中[部署](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure)進程伺服器。
- [部署](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) 個別的主要目標伺服器。

當您將 Azure Vm 重新保護到內部部署時，您可以指定要容錯回復至原始位置或替代位置。

- **原始位置**復原：這會從 Azure 容錯回復到相同的來源內部部署機器（如果存在的話）。 在此案例中，只會將變更複寫回內部部署。
- **替代位置**復原：如果內部部署機器不存在，您可以從 Azure 容錯回復到替代位置。 當您將 Azure VM 重新保護到內部部署時，會建立內部部署機器。 從 Azure 到內部部署進行的完整資料複寫。 -- [檢查](concepts-types-of-failback.md) 位置容錯回復的需求和限制。



## <a name="hyper-v-reprotectionfailback"></a>Hyper-v 重新保護/容錯回復

若要將 Hyper-v Vm 從 Azure 重新保護和容錯回復至內部部署：

- 您只能使用儲存體帳戶來容錯回復 Hyper-v Vm 複寫。 不支援容錯回復使用受控磁片進行複寫的 Hyper-v Vm。
- 內部部署 Hyper-v 主機 (或 System Center VMM （如果使用) 應該連接至 Azure）。
- 您可以執行從 Azure 到內部部署的計畫容錯回復。
- 不需要設定 Hyper-v VM 容錯回復的特定元件。
- 在規劃的容錯移轉期間，您可以選取在容錯回復之前同步處理資料的選項：
    - **在容錯移轉之前同步處理資料**：此選項可將虛擬機器的停機時間降到最低，因為它會同步處理電腦而不關閉它們
        - 階段1：取得 Azure VM 的快照集，並將其複製到內部部署 Hyper-v 主機。 機器會繼續在 Azure 中執行。
        - 階段2：關閉 Azure VM，如此一來，就不會發生任何新的變更。 最後一組差異變更會傳送到內部部署伺服器，並啟動內部部署 VM。
    - **僅在容錯移轉期間同步處理資料**：此選項的速度較快，因為我們預期大部分的磁片已經變更，因此不會執行總和檢查碼計算。 它會下載磁碟。 如果 VM 已在 Azure 中執行一段時間，但 (一個月或更多) ，或內部部署 VM 已刪除，建議您使用此選項。

[深入瞭解](hyper-v-azure-failback.md) hyper-v 重新保護和容錯回復。

當您將 Azure Vm 重新保護到內部部署時，您可以指定要容錯回復至原始位置或替代位置。

- **原始位置**復原：這會從 Azure 容錯回復到相同的來源內部部署機器（如果存在的話）。 在此案例中，您會選取先前程式中所述的其中一個同步處理選項。
- **替代位置**復原：如果內部部署機器不存在，您可以從 Azure 容錯回復到替代位置。 當您將 Azure VM 重新保護到內部部署時，會建立內部部署機器。 使用這個選項時，建議您選取在容錯移轉之前同步處理資料的選項。
- 請[參閱](hyper-v-azure-failback.md)位置容錯回復的需求和限制。


容錯回復至內部部署網站後，您可以啟用 **反向** 複寫，以開始將 VM 複寫至 Azure，並完成迴圈。




## <a name="next-steps"></a>後續步驟
- [針對特定 VMware vm](vmware-azure-tutorial-failover-failback.md)進行容錯移轉
- [針對特定 Hyper-v vm](hyper-v-azure-failover-failback-tutorial.md)進行容錯移轉。
- [建立](site-recovery-create-recovery-plans.md) 復原方案。
- 容錯移轉復原 [方案中的 vm](site-recovery-failover.md)。
- [準備](vmware-azure-failback.md) VMware 重新保護和容錯回復。
- 容錯回復 [Hyper-v vm](hyper-v-azure-failback.md)。