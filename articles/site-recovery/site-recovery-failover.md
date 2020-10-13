---
title: 使用 Azure Site Recovery 在嚴重損壞修復期間執行容錯移轉
description: 如何使用 Azure Site Recovery 將 Vm/實體伺服器容錯移轉至 Azure。
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 481e7c692be24bbebd14584f8158740a5b7043ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317883"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>執行從內部部署容錯移轉至 Azure

本文說明如何在[Azure Site Recovery](site-recovery-overview.md)中將內部部署機器容錯移轉至 Azure

## <a name="before-you-start"></a>開始之前

- [瞭解](failover-failback-overview.md) 嚴重損壞修復中的容錯移轉程式。
- 如果您想要容錯移轉多部機器，請 [瞭解](recovery-plan-overview.md) 如何在復原方案中一起收集機器。
- 執行完整容錯移轉之前，請執行嚴重損壞 [修復演練](site-recovery-test-failover-to-azure.md) ，以確定一切都如預期般運作。

## <a name="prepare-to-connect-after-failover"></a>準備在容錯移轉後連接

若要確定您可以連線到在容錯移轉後建立的 Azure Vm，您必須先在內部部署執行一些作業，然後再進行容錯移轉。


### <a name="prepare-on-premises-to-connect-after-failover"></a>準備在容錯移轉後連線至內部部署以進行連接

如果您想要在容錯移轉後使用 RDP/SSH 連線到 Azure Vm，您必須在容錯移轉之前，先在內部部署進行一些作業。

**容錯移轉後** | **位置** | **動作**
--- | --- | ---
**執行 Windows 的 Azure VM** | 在容錯移轉前的內部部署機器 | 若要透過網際網路存取 Azure VM，請啟用 RDP，並確定已針對 [公用]**** 新增 TCP 和 UDP 規則，且在 [Windows 防火牆]**** > [允許的應用程式]**** 中已針對所有設定檔允許 RDP。<br/><br/> 若要透過站對站連線存取 Azure VM，請在機器上啟用 rdp，並確定**Windows 防火牆**  ->  **允許的應用程式和功能**（適用于**網域和專用**網）允許 rdp。<br/><br/> <br/><br/> 移除任何靜態持續性路由和 WinHTTP proxy。 確定作業系統的 SAN 原則已設為 **OnlineAll**。 [深入了解](https://support.microsoft.com/kb/3031135)。<br/><br/> 觸發容錯移轉時，請確定 VM 上沒有任何暫止的 Windows 更新。 容錯移轉時，可能會啟動 Windows 更新，必須等到更新完成，才能登入 VM。
**執行 Linux 的 Azure VM** | 在容錯移轉前的內部部署機器 | 確定 VM 上的安全殼層服務已設定為在系統開機時自動啟動。<br/><br/> 請檢查防火牆規則是否允許 SSH 連線。


## <a name="run-a-failover"></a>執行容錯移轉

此程序說明如何針對[復原方案](site-recovery-create-recovery-plans.md)執行容錯移轉。 如果您想要執行單一 VM 的容錯移轉，請遵循 [VMWARE vm](vmware-azure-tutorial-failover-failback.md)、 [實體伺服器](physical-to-azure-failover-failback.md)或 [hyper-v vm](hyper-v-azure-failover-failback-tutorial.md)的指示。


執行復原方案容錯移轉，如下所示：

1. 在 Site Recovery 保存庫中，選取 [復原**方案**]  >  *recoveryplan_name*。
2. 按一下 [容錯移轉]。

    ![Azure Site Recovery 的螢幕擷取畫面，其中顯示 [ADRP] 窗格，並從 [其他] 功能表選取 [容錯移轉]](./media/site-recovery-failover/Failover.png)

3. 如果您要複寫至 Azure，請在**容錯移轉**  >  **容錯移轉方向**中保留預設值。
4. 在 [ **容錯移轉**] 中，選取要容錯移轉的 **復原點** 。

    - **最新**：使用最新點。 這會處理傳送給 Site Recovery 服務的所有資料，並為每部機器建立復原點。 此選項會提供最低的 RPO (復原點目標) ，因為在容錯移轉後建立的 VM 具有在觸發容錯移轉時複寫至 Site Recovery 的所有資料。
    請注意，當來源區域停止運作時，就無法再進行記錄處理。 因此，您需要容錯移轉至最新的已處理復原點。 若要深入瞭解，請參閱下一個重點。
   - **最新處理**：使用此選項可將 vm 容錯移轉至已由 Site Recovery 處理的最新復原點。 您可以在 VM **最新復原點**中看到最近處理的復原點。 此選項提供低 RTO，因為未花費時間處理未處理的資料
   - **最新應用程式一致**：使用此選項可將 vm 容錯移轉至由 Site Recovery 處理的最新應用程式一致復原點。
   - 已**處理的最新多 vm**：使用此選項時，屬於複寫群組的 vm 會容錯移轉至最新的一般多部 vm 一致復原點。 其他虛擬機器容錯移轉至其最新的已處理復原點。 此選項僅適用于至少有一個已啟用多部 vm 一致性的 VM 的復原方案。
   - **最新的多 vm 應用程式一致**：使用此選項時，屬於複寫群組一部分的 vm 會容錯移轉至最新的一般多部 vm 應用程式一致復原點。 對於最近的應用程式一致復原點進行的其他虛擬機器容錯移轉。 僅適用于至少有一部 VM 啟用多部 vm 一致性的復原計畫。
   - **自訂**：無法使用復原方案。 此選項僅適用于個別 Vm 的容錯移轉。

5. 如果您想要在啟動容錯移轉之前 Site Recovery 先關閉來源 Vm，請選取 [ **在開始容錯移轉之前先關機電腦** ]。 即使關機失敗，仍會繼續容錯移轉。  

    > [!NOTE]
    > 如果您容錯移轉 Hyper-v Vm，關閉會嘗試在觸發容錯移轉之前，同步處理並複寫尚未傳送至服務的內部部署資料。 

6. 在 [ **作業** ] 頁面上追蹤容錯移轉進度。 即使發生錯誤，復原方案還是會執行，直到完成為止。
7. 在容錯移轉之後，請登入 VM 進行驗證。 
8. 如果您想要切換至不同的復原點以用於容錯移轉，請使用 [ **變更復原點**]。
9. 當您準備好時，您可以認可容錯移轉。 **認可** 動作會刪除服務可用的所有復原點。 將無法再使用 [ **變更復原點** ] 選項。

## <a name="run-a-planned-failover-hyper-v"></a> (Hyper-v) 執行規劃的容錯移轉

您可以針對 Hyper-v Vm 執行規劃的容錯移轉。

- 規劃的容錯移轉是零資料遺失的容錯移轉選項。
- 觸發計劃性容錯移轉時，首先會將來源虛擬機器關機、將最新的資料進行同步處理，然後觸發容錯移轉。
- 您使用 **規劃的故障** 轉移選項執行規劃的容錯移轉。 它會以類似的方式執行，以進行定期容錯移轉。
 
## <a name="track-failovers"></a>追蹤容錯移轉

有一些與容錯移轉相關聯的工作。

![[工作] 頁面的螢幕擷取畫面，其中顯示 [名稱] 資料行中的 [群組1：開始] (1) 展開的作業清單。 會反白顯示 SQLServer 工作的行。](./media/site-recovery-failover/FailoverJob.png)

- **必要條件檢查**：確保符合容錯移轉所需的所有條件。
- **容錯移轉**：處理資料，以便從中建立 Azure VM。 如果您已選擇 **最新** 的復原點，則會從已傳送至服務的資料建立復原點。
- **啟動**：使用上一個步驟中處理的資料建立 Azure VM。

> [!WARNING]
> **不要取消進行中的容錯移轉**：容錯移轉開始之前，已停止 VM 的複寫。 如果您取消進行中的作業，容錯移轉會停止，但 VM 不會開始複寫。 無法重新開機複寫。


### <a name="extra-failover-time"></a>額外的容錯移轉時間

在某些情況下，VM 容錯移轉需要中繼步驟，通常需要約8到10分鐘才能完成。 這些是受此額外步驟/時間影響的機器：

* 執行早于9.8 的行動服務版本的 VMware 虛擬機器。
* 實體伺服器，以及做為實體伺服器保護的 Hyper-v Vm。
* VMware Linux VM。
* 這些驅動程式未以開機驅動程式形式出現的 VMware Vm：
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* 未啟用 DHCP 的 VMware Vm，不論它們是使用 DHCP 還是靜態 IP 位址。


## <a name="automate-actions-during-failover"></a>在容錯移轉期間自動執行動作

您可能會想要在容錯移轉期間自動執行動作。 若要這樣做，您可以使用腳本或復原方案中的 Azure 自動化 runbook。

- [瞭解](site-recovery-create-recovery-plans.md) 如何建立和自訂復原方案，包括新增腳本。
- [瞭解](site-recovery-runbook-automation.md) 如何將 Azure 自動化 runbook 新增至復原方案。


## <a name="configure-settings-after-failover"></a>在容錯移轉之後設定設定

### <a name="retain-drive-letters-after-failover"></a>在容錯移轉後保留磁碟機號

Site Recovery 處理磁碟機號的保留。 如果您要在 VM 複寫期間排除磁片，請 [參閱其](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) 運作方式的範例。

### <a name="prepare-in-azure-to-connect-after-failover"></a>在 Azure 中準備以在容錯移轉後進行連接

如果您想要連線至使用 RDP 或 SSH 進行容錯移轉之後建立的 Azure Vm，請遵循資料表中摘要說明的需求。

**容錯移轉** | **位置** | **動作**
--- | --- | ---
**執行 Windows 的 Azure VM** | 容錯移轉後的 Azure VM |  [新增 VM 的公用 IP 位址](https://aka.ms/addpublicip)。<br/><br/> 已容錯移轉的 VM 上的網路安全性群組規則 (以及它所連線的 Azure 子網路) 必須允許 RDP 連接埠的連入連線。<br/><br/> 勾選 [開機診斷]**** 以確認 VM 的螢幕擷取畫面。<br/><br/> 如果您無法連線，請檢查 VM 是否正在執行，並檢閱這些[疑難排解祕訣](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。
**執行 Linux 的 Azure VM** | 容錯移轉後的 Azure VM | 已容錯移轉的 VM 上的網路安全性群組規則 (以及它所連線的 Azure 子網路) 必須允許 SSH 連接埠的連入連線。<br/><br/> [新增 VM 的公用 IP 位址](https://aka.ms/addpublicip)。<br/><br/> 勾選 [開機診斷]**** 以檢視 VM 的螢幕擷取畫面。<br/><br/>

請依照[這裡](site-recovery-failover-to-azure-troubleshoot.md)所述的步驟，對容錯移轉後的連線問題進行疑難排解。

## <a name="set-up-ip-addressing"></a>設定 IP 位址

- **內部 ip 位址**：若要在容錯移轉之後設定 Azure VM 的內部 ip 位址，您有幾個選項：
    - 保留相同的 IP 位址：您可以使用 Azure VM 上的相同 IP 位址，作為配置給內部部署機器的 IP 位址。
    - 使用不同的 IP 位址：您可以針對 Azure VM 使用不同的 IP 位址。
    - [深入瞭解](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) 如何設定內部 IP 位址。
- **外部 ip 位址**：您可以在容錯移轉時保留公用 ip 位址。 在容錯移轉程式中建立的 azure Vm 必須獲指派 azure 區域中可用的 Azure 公用 IP 位址。 您可以手動指派公用 IP 位址，或透過復原方案將程式自動化。 [深入了解](concepts-public-ip-address-with-site-recovery.md)。


## <a name="next-steps"></a>後續步驟

在您容錯移轉之後，您需要重新保護以開始將 Azure Vm 複寫回內部部署網站。 在複寫啟動並執行之後，您可以在準備好時容錯回復內部部署。

- [深入瞭解](failover-failback-overview.md#reprotectionfailback) 重新保護和容錯回復。
- [準備](vmware-azure-reprotect.md) VMware 重新保護和容錯回復。
- [容錯回復](hyper-v-azure-failback.md) Hyper-v Vm。
- [瞭解](physical-to-azure-failover-failback.md) 實體伺服器的容錯移轉和容錯回復程式。

