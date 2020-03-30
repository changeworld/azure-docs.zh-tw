---
title: 使用 Azure 網站恢復在災害復原期間運行容錯移轉
description: 如何通過 Azure 網站恢復將 VM/物理伺服器容錯移轉到 Azure。
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 99a197e8f5ebac8a3b0be1b567ee41b43a2c4476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471263"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>執行從內部部署容錯移轉至 Azure

本文介紹如何在[Azure 網站恢復](site-recovery-overview.md)中將本地電腦容錯移轉到 Azure

## <a name="before-you-start"></a>開始之前

- [瞭解](failover-failback-overview.md)災害復原中的容錯移轉過程。
- 如果要容錯移轉多台電腦，[瞭解如何](recovery-plan-overview.md)在恢復計畫中將電腦集中在一起。
- 在進行完全容錯移轉之前，運行[災害復原演練](site-recovery-test-failover-to-azure.md)以確保一切按預期工作。

## <a name="prepare-to-connect-after-failover"></a>準備在容錯移轉後連接

為了確保可以連接到容錯移轉後創建的 Azure VM，以下是在容錯移轉之前需要在本地執行的多項操作。


### <a name="prepare-on-premises-to-connect-after-failover"></a>在容錯移轉後準備本地連接

如果要在容錯移轉後使用 RDP/SSH 連接到 Azure VM，則需要在容錯移轉之前在本地執行許多操作。

**容錯移轉之後** | **位置** | **動作**
--- | --- | ---
**執行 Windows 的 Azure VM** | 在容錯移轉前的內部部署機器 | 若要透過網際網路存取 Azure VM，請啟用 RDP，並確定已針對 [公用]**** 新增 TCP 和 UDP 規則，且在 [Windows 防火牆]**** > [允許的應用程式]**** 中已針對所有設定檔允許 RDP。<br/><br/> 要通過網站到網站連接訪問 Azure VM，請使用電腦上的 RDP，並確保**在域和專用**網路的**Windows 防火牆** -> **允許的應用和功能**中允許 RDP。<br/><br/> <br/><br/> 刪除任何靜態持久路由和 WinHTTP 代理。 確定作業系統的 SAN 原則已設為 **OnlineAll**。 [深入了解](https://support.microsoft.com/kb/3031135)。<br/><br/> 觸發容錯移轉時，請確定 VM 上沒有任何暫止的 Windows 更新。 容錯移轉時，可能會啟動 Windows 更新，必須等到更新完成，才能登入 VM。
**執行 Linux 的 Azure VM** | 在容錯移轉前的內部部署機器 | 確定 VM 上的安全殼層服務已設定為在系統開機時自動啟動。<br/><br/> 請檢查防火牆規則是否允許 SSH 連線。


## <a name="run-a-failover"></a>執行容錯移轉

此程序說明如何針對[復原方案](site-recovery-create-recovery-plans.md)執行容錯移轉。 如果要為單個 VM 運行容錯移轉，請按照[VMware VM、](vmware-azure-tutorial-failover-failback.md)[物理伺服器](physical-to-azure-failover-failback.md)或[Hyper-V VM](hyper-v-azure-failover-failback-tutorial.md)的說明進行操作。


運行恢復計畫容錯移轉，如下所示：

1. 在網站恢復保存庫中，選擇**恢復計畫** > *recoveryplan_name*。
2. 按一下 [容錯移轉]****。

    ![容錯移轉](./media/site-recovery-failover/Failover.png)

3. 在**容錯移轉** > **容錯移轉方向**中，如果要複製到 Azure，請保留預設值。
4. 在**容錯移轉中**，選擇要容錯移轉**的復原點**。

    - **最新**：使用最新點。 這將處理發送到網站恢復服務的所有資料，並為每台電腦創建一個復原點。 此選項提供最低的 RPO（復原點目標），因為容錯移轉後創建的 VM 具有觸發容錯移轉時已複製到網站恢復的所有資料。
   - **最新處理**：使用此選項將 VM 容錯移轉到網站恢復已處理的最新復原點。 您可以在 VM**最新復原點**中查看最新的處理復原點。 此選項提供低 RTO，因為無需花費時間處理未處理的資料
   - **最新的應用一致性**：使用此選項將 VM 容錯移轉到網站恢復處理的最新應用程式一致性復原點。
   - **已處理的最新多 VM：** 使用此選項的 VM，這些 VM 是複製組容錯移轉到最新常見多 VM 一致性復原點的 VM。 其他虛擬機器容錯移轉到其最新處理的復原點。 此選項僅適用于至少啟用了多 VM 一致性的一個 VM 的恢復計畫。
   - **最新的多 VM 應用一致性**：使用此選項，作為複製組一部分的 VM 容錯移轉到最新的常見多 VM 應用程式一致性復原點。 對於最近的應用程式一致復原點進行的其他虛擬機器容錯移轉。 僅適用于至少啟用了多 VM 一致性的一個 VM 的恢復計畫。
   - **自訂**：不適用於恢復計畫。 此選項僅適用于單個 VM 的容錯移轉。

5. 如果您希望網站恢復在啟動容錯移轉之前關閉源 VM，請在**開始容錯移轉之前關閉電腦**。 即使關機失敗，仍會繼續容錯移轉。  

    > [!NOTE]
    > 如果容錯移轉 Hyper-VM，關閉將嘗試同步並複製尚未發送到服務的本地資料，然後再觸發容錯移轉。 

6. 關注 **"作業**"頁上的容錯移轉進度。 即使發生錯誤，恢復計畫也會一直運行到它完成為止。
7. 容錯移轉後，登錄到 VM 以驗證它。 
8. 如果要切換到不同的復原點以用於容錯移轉，請使用**更改復原點**。
9. 準備就緒後，可以提交容錯移轉。"**提交"** 操作將刪除服務中可用的所有復原點。 **更改復原點**選項將不再可用。

## <a name="run-a-planned-failover-hyper-v"></a>運行計畫容錯移轉（Hyper-V）

您可以為超 VM 運行計畫容錯移轉。

- 計畫容錯移轉是零資料丟失容錯移轉選項。
- 觸發計劃性容錯移轉時，首先會將來源虛擬機器關機、將最新的資料進行同步處理，然後觸發容錯移轉。
- 使用 **"計畫容錯移轉"** 選項運行計畫容錯移轉。 它以與常規容錯移轉類似的方式運行。
 
## <a name="track-failovers"></a>跟蹤容錯移轉

有許多作業與容錯移轉相關聯。

![容錯移轉](./media/site-recovery-failover/FailoverJob.png)

- **先決條件檢查**：確保滿足容錯移轉所需的所有條件。
- **容錯移轉**：處理資料，以便可以從中創建 Azure VM。 如果選擇了 **"最新**復原點"，則從發送到服務的資料創建復原點。
- **開始**：使用上一步驟中處理的資料創建 Azure VM。

> [!WARNING]
> **不要取消正在進行的容錯移轉**：在啟動容錯移轉之前，VM 的複製已停止。 如果取消正在進行的作業，容錯移轉將停止，但 VM 不會開始複製。 無法再次啟動複製。


### <a name="extra-failover-time"></a>額外的容錯移轉時間

在某些情況下，VM 容錯移轉需要中間步驟，通常需要大約 8 到 10 分鐘才能完成。 這些是受此附加步驟/時間影響的電腦：

* VMware 虛擬機器運行移動服務版本早于 9.8。
* 物理伺服器和作為物理伺服器保護的超 VM。
* VMware Linux VM。
* VMware VM 這些驅動程式不存在作為引導驅動程式：
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* VMware VM 未啟用 DHCP，無論它們使用的是 DHCP 還是靜態 IP 位址。


## <a name="automate-actions-during-failover"></a>容錯移轉期間自動執行操作

您可能希望在容錯移轉期間自動執行操作。 為此，可以在恢復計畫中使用腳本或 Azure 自動化 Runbook。

- [瞭解如何](site-recovery-create-recovery-plans.md)創建和自訂恢復計畫，包括添加腳本。
- [瞭解如何](site-recovery-runbook-automation.md)將 Azure 自動化運行簿添加到恢復計畫。


## <a name="configure-settings-after-failover"></a>容錯移轉後配置設置

### <a name="retain-drive-letters-after-failover"></a>容錯移轉後保留磁碟機號

網站恢復處理磁碟機號的保留。 如果在 VM 複製期間排除磁片，[請查看](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)此示例，瞭解其工作原理。

### <a name="prepare-in-azure-to-connect-after-failover"></a>在 Azure 中準備容錯移轉後連接

如果要連接到使用 RDP 或 SSH 容錯移轉後創建的 Azure VM，請按照表中總結的要求進行操作。

**故障** | **位置** | **動作**
--- | --- | ---
**執行 Windows 的 Azure VM** | 容錯移轉後的 Azure VM |  [新增 VM 的公用 IP 位址](https://aka.ms/addpublicip)。<br/><br/> 已容錯移轉的 VM 上的網路安全性群組規則 (以及它所連線的 Azure 子網路) 必須允許 RDP 連接埠的連入連線。<br/><br/> 勾選 [開機診斷]**** 以確認 VM 的螢幕擷取畫面。<br/><br/> 如果您無法連線，請檢查 VM 是否正在執行，並檢閱這些[疑難排解祕訣](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。
**執行 Linux 的 Azure VM** | 容錯移轉後的 Azure VM | 已容錯移轉的 VM 上的網路安全性群組規則 (以及它所連線的 Azure 子網路) 必須允許 SSH 連接埠的連入連線。<br/><br/> [新增 VM 的公用 IP 位址](https://aka.ms/addpublicip)。<br/><br/> 勾選 [開機診斷]**** 以檢視 VM 的螢幕擷取畫面。<br/><br/>

請依照[這裡](site-recovery-failover-to-azure-troubleshoot.md)所述的步驟，對容錯移轉後的連線問題進行疑難排解。

## <a name="set-up-ip-addressing"></a>設置 IP 定址

- **內部 IP 位址**：要在容錯移轉後設置 Azure VM 的內部 IP 位址，有幾個選項：
    - 保留相同的 IP 位址：您可以在 Azure VM 上使用與分配給本地電腦的 IP 位址相同的 IP 位址。
    - 使用不同的 IP 位址：可以為 Azure VM 使用不同的 IP 位址。
    - [詳細瞭解](concepts-on-premises-to-azure-networking.md#assign-an-internal-address)如何設置內部 IP 位址。
- **外部 IP 位址**：您可以在容錯移轉時保留公共 IP 位址。 作為容錯移轉過程的一部分創建的 Azure VM 必須為 Azure 區域中的可用 Azure 公共 IP 位址分配。 您可以手動分配公共 IP 位址，也可以通過恢復計畫自動分配進程。 [深入了解](concepts-public-ip-address-with-site-recovery.md)。


## <a name="next-steps"></a>後續步驟

故障結束後，需要重新保護以開始將 Azure VM 複製回本地網站。 啟動並運行複製後，您可以在準備就緒後在本地故障回。

- [瞭解有關](failover-failback-overview.md#reprotectionfailback)重新保護和故障恢復的更多詳細資訊。
- [準備](vmware-azure-reprotect.md)VMware 重新保護和故障恢復。
- [故障恢復](hyper-v-azure-failback.md)超 VM。
- [瞭解](physical-to-azure-failover-failback.md)物理伺服器的容錯移轉和容錯移轉過程。

