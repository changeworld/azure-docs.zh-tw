---
title: 使用 Azure Site Recovery 在嚴重損壞修復期間執行容錯移轉
description: 如何使用 Azure Site Recovery 將 Vm/實體伺服器故障切換至 Azure。
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 99a197e8f5ebac8a3b0be1b567ee41b43a2c4476
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471263"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>執行從內部部署容錯移轉至 Azure

本文說明如何在[Azure Site Recovery](site-recovery-overview.md)中將內部部署機器故障切換至 Azure

## <a name="before-you-start"></a>在您開始使用 Intune 之前

- [瞭解](failover-failback-overview.md)嚴重損壞修復中的容錯移轉程式。
- 如果您想要故障處理多部電腦，請[瞭解](recovery-plan-overview.md)如何在復原方案中一起收集機器。
- 執行完整容錯移轉之前，請執行嚴重損壞[修復演練](site-recovery-test-failover-to-azure.md)，以確保所有專案都如預期般運作。

## <a name="prepare-to-connect-after-failover"></a>準備在容錯移轉後連接

為了確保您可以連線到在容錯移轉後建立的 Azure Vm，以下是在容錯移轉之前，您必須在內部部署執行的一些動作。


### <a name="prepare-on-premises-to-connect-after-failover"></a>準備在容錯移轉後進行內部部署連接

如果您想要在容錯移轉後使用 RDP/SSH 連線到 Azure Vm，您必須在容錯移轉之前，于內部部署執行一些動作。

**容錯移轉之後** | **位置** | **動作**
--- | --- | ---
**執行 Windows 的 Azure VM** | 在容錯移轉前的內部部署機器 | 若要透過網際網路存取 Azure VM，請啟用 RDP，並確定已針對 [公用]**** 新增 TCP 和 UDP 規則，且在 [Windows 防火牆]**** > [允許的應用程式]**** 中已針對所有設定檔允許 RDP。<br/><br/> 若要透過站對站連線來存取 Azure VM，請在機器上啟用 rdp，並確定在 [ **Windows 防火牆** -> ] [**允許的應用程式和功能**] 中，針對 [**網域] 和 [私人**] 網路允許 rdp。<br/><br/> <br/><br/> 移除任何靜態持續性路由和 WinHTTP proxy。 確定作業系統的 SAN 原則已設為 **OnlineAll**。 [深入了解](https://support.microsoft.com/kb/3031135)。<br/><br/> 觸發容錯移轉時，請確定 VM 上沒有任何暫止的 Windows 更新。 容錯移轉時，可能會啟動 Windows 更新，必須等到更新完成，才能登入 VM。
**執行 Linux 的 Azure VM** | 在容錯移轉前的內部部署機器 | 確定 VM 上的安全殼層服務已設定為在系統開機時自動啟動。<br/><br/> 請檢查防火牆規則是否允許 SSH 連線。


## <a name="run-a-failover"></a>執行容錯移轉

此程序說明如何針對[復原方案](site-recovery-create-recovery-plans.md)執行容錯移轉。 如果您想要針對單一 VM 執行容錯移轉，請遵循[VMWARE VM](vmware-azure-tutorial-failover-failback.md)、[實體伺服器](physical-to-azure-failover-failback.md)或[hyper-v vm](hyper-v-azure-failover-failback-tutorial.md)的指示。


執行復原方案容錯移轉，如下所示：

1. 在 Site Recovery 保存庫中，選取 [復原**方案** > ]*recoveryplan_name*。
2. 按一下 [容錯移轉]****。

    ![容錯移轉](./media/site-recovery-failover/Failover.png)

3. 如果您要複寫至 Azure，請在 [**容錯移轉** > **容錯移轉方向**] 中保留預設值。
4. 在 [**容錯移轉**] 中，選取要容錯移轉的目標**復原點**。

    - **最新**：使用最新的點。 這會處理已傳送到 Site Recovery 服務的所有資料，並為每部機器建立復原點。 此選項會提供最低的 RPO （復原點目標），因為在容錯移轉後建立的 VM 會在觸發容錯移轉時，將所有資料複寫到 Site Recovery。
   - **最新處理**：使用此選項可將 vm 損毀修復到已由 Site Recovery 處理的最新復原點。 您可以在 VM**最新的復原點**中看到最新處理的復原點。 此選項提供低 RTO，因為沒有時間花費在處理未處理的資料
   - **最新的應用程式一致**：使用此選項可將 vm 故障，移至 Site Recovery 所處理的最新應用程式一致復原點。
   - 已**處理最新多個 vm**：使用此選項時，屬於複寫群組的 vm 會容錯移轉至最新的一般多部 VM 一致復原點。 其他虛擬機器故障切換至其最近處理的復原點。 此選項僅適用于至少有一部 VM 已啟用多部 VM 一致性的復原計畫。
   - **最新多部 vm 應用程式一致**：使用此選項時，屬於複寫群組的 vm 會故障切換至最新的一般多部 vm 應用程式一致復原點。 對於最近的應用程式一致復原點進行的其他虛擬機器容錯移轉。 僅適用于至少有一部 VM 已啟用多部 VM 一致性的復原方案。
   - **自訂**：無法用於復原方案。 此選項僅適用于個別 Vm 的容錯移轉。

5. 如果您想要 Site Recovery 在啟動容錯移轉之前先關閉來源 Vm，請選取 [**在開始容錯移轉之前將機器關機**]。 即使關機失敗，仍會繼續容錯移轉。  

    > [!NOTE]
    > 如果您容錯移轉 Hyper-v Vm，則在觸發容錯移轉之前，關機會嘗試同步處理並複寫尚未傳送至服務的內部部署資料。 

6. 在 [**作業**] 頁面上追蹤容錯移轉進度。 即使發生錯誤，復原方案還是會執行，直到完成為止。
7. 容錯移轉之後，請登入 VM 以進行驗證。 
8. 如果您想要切換至不同的復原點以用於容錯移轉，請使用 [**變更復原點**]。
9. 當您準備好時，就可以認可容錯移轉。**認可**動作會刪除服務可用的所有復原點。 [**變更復原點**] 選項將無法再使用。

## <a name="run-a-planned-failover-hyper-v"></a>執行規劃的容錯移轉（Hyper-v）

您可以針對 Hyper-v Vm 執行規劃的容錯移轉。

- 規劃的容錯移轉是零的資料遺失容錯移轉選項。
- 觸發計劃性容錯移轉時，首先會將來源虛擬機器關機、將最新的資料進行同步處理，然後觸發容錯移轉。
- 您可以使用**規劃的容錯移轉**選項來執行計畫的容錯移轉。 其執行方式類似于一般容錯移轉。
 
## <a name="track-failovers"></a>追蹤容錯移轉

有一些與容錯移轉相關聯的作業。

![容錯移轉](./media/site-recovery-failover/FailoverJob.png)

- **必要條件檢查**：確保符合容錯移轉所需的所有條件。
- **容錯移轉**：處理資料，讓您可以從它建立 Azure VM。 如果您已選擇**最新**的復原點，則會從已傳送至服務的資料建立復原點。
- **啟動**：使用上一個步驟中處理的資料建立 Azure VM。

> [!WARNING]
> **不要取消正在進行的容錯移轉**：在啟動容錯移轉之前，VM 的複寫已停止。 如果您取消進行中的作業，容錯移轉會停止，但 VM 不會開始複寫。 無法再次啟動複寫。


### <a name="extra-failover-time"></a>額外的容錯移轉時間

在某些情況下，VM 容錯移轉需要通常需要大約8到10分鐘才能完成的中繼步驟。 這些是受此額外步驟/時間影響的電腦：

* 執行比9.8 更舊之行動服務版本的 VMware 虛擬機器。
* 實體伺服器和以實體伺服器形式保護的 Hyper-v Vm。
* VMware Linux VM。
* 這些驅動程式不會呈現為開機驅動程式的 VMware Vm：
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* 未啟用 DHCP 的 VMware Vm，不論它們是使用 DHCP 或靜態 IP 位址。


## <a name="automate-actions-during-failover"></a>在容錯移轉期間自動化動作

您可能想要在容錯移轉期間自動化動作。 若要這樣做，您可以在復原方案中使用腳本或 Azure 自動化 runbook。

- [瞭解](site-recovery-create-recovery-plans.md)如何建立和自訂復原方案，包括新增腳本。
- [瞭解](site-recovery-runbook-automation.md)如何將 runbook Azure 自動化新增至復原方案。


## <a name="configure-settings-after-failover"></a>在容錯移轉後進行設定

### <a name="retain-drive-letters-after-failover"></a>在容錯移轉後保留磁碟機號

Site Recovery 會處理磁碟機號的保留。 如果您在 VM 複寫期間排除磁片，請[參閱其運作方式的範例](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)。

### <a name="prepare-in-azure-to-connect-after-failover"></a>在 Azure 中準備好在容錯移轉後連接

如果您想要連接到使用 RDP 或 SSH 在容錯移轉之後建立的 Azure Vm，請遵循資料表中摘要說明的需求。

**容錯移轉** | **位置** | **動作**
--- | --- | ---
**執行 Windows 的 Azure VM** | 容錯移轉後的 Azure VM |  [新增 VM 的公用 IP 位址](https://aka.ms/addpublicip)。<br/><br/> 已容錯移轉的 VM 上的網路安全性群組規則 (以及它所連線的 Azure 子網路) 必須允許 RDP 連接埠的連入連線。<br/><br/> 勾選 [開機診斷]**** 以確認 VM 的螢幕擷取畫面。<br/><br/> 如果您無法連線，請檢查 VM 是否正在執行，並檢閱這些[疑難排解祕訣](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。
**執行 Linux 的 Azure VM** | 容錯移轉後的 Azure VM | 已容錯移轉的 VM 上的網路安全性群組規則 (以及它所連線的 Azure 子網路) 必須允許 SSH 連接埠的連入連線。<br/><br/> [新增 VM 的公用 IP 位址](https://aka.ms/addpublicip)。<br/><br/> 勾選 [開機診斷]**** 以檢視 VM 的螢幕擷取畫面。<br/><br/>

請依照[這裡](site-recovery-failover-to-azure-troubleshoot.md)所述的步驟，對容錯移轉後的連線問題進行疑難排解。

## <a name="set-up-ip-addressing"></a>設定 IP 位址

- **內部 ip 位址**：若要在容錯移轉之後設定 Azure VM 的內部 ip 位址，您有幾個選項：
    - 保留相同的 IP 位址：您可以在 Azure VM 上使用與配置給內部部署機器相同的 IP 位址。
    - 使用不同的 IP 位址：您可以針對 Azure VM 使用不同的 IP 位址。
    - [深入瞭解](concepts-on-premises-to-azure-networking.md#assign-an-internal-address)如何設定內部 IP 位址。
- **外部 IP 位址**：您可以在容錯移轉時保留公用 ip 位址。 在容錯移轉程式中建立的 azure Vm 必須獲指派 azure 區域中可用的 Azure 公用 IP 位址。 您可以手動指派公用 IP 位址，或透過復原方案將程式自動化。 [深入了解](concepts-public-ip-address-with-site-recovery.md)。


## <a name="next-steps"></a>後續步驟

容錯回復之後，您必須重新保護，以開始將 Azure Vm 複寫回內部部署網站。 複寫啟動並執行之後，您可以在準備好時，容錯回復內部部署。

- [深入瞭解](failover-failback-overview.md#reprotectionfailback)重新保護和容錯回復。
- [準備](vmware-azure-reprotect.md)VMware 重新保護和容錯回復。
- [容錯回復](hyper-v-azure-failback.md)Hyper-v Vm。
- [瞭解](physical-to-azure-failover-failback.md)實體伺服器的容錯移轉和容錯回復程式。

