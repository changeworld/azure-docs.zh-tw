---
title: 針對使用 Azure Site Recovery 從 VMware VM 和實體伺服器至 Azure 的災害復原複寫問題進行疑難排解 | Microsoft Docs
description: 本文針對使用 Azure Site Recovery 將 VMware VM 和實體伺服器復原至 Azure 之災害復原期間發生的常見複寫問題，提供疑難排解資訊。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: e9e66cbb024aa64e8c4cb5db9fc1c172fdc573fc
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135366"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>針對 VMware VM 和實體伺服器的複寫問題進行疑難排解

本文描述使用 [Site Recovery](site-recovery-overview.md) 將內部部署 VMware VM 和實體伺服器複寫至 Azure 時，可能會遇到的一些常見問題和特定錯誤。

## <a name="step-1-monitor-process-server-health"></a>步驟 1:監視處理伺服器健康狀態

Site Recovery 會使用[處理伺服器](vmware-physical-azure-config-process-server-overview.md#process-server)來接收及最佳化複寫的資料，並將其傳送至 Azure。

我們建議在入口網站中監視處理伺服器的健康狀態，以確保這些伺服器已連線且運作正常，以及與處理伺服器建立關聯的來源機器正在進行複寫。

- [了解](vmware-physical-azure-monitor-process-server.md)如何監視處理伺服器。
- [檢閱最佳作法](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- 針對處理伺服器進行[疑難排解](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health)。

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>步驟 2:針對連線能力和複寫問題進行疑難排解

初始和進行中複寫失敗通常是來源伺服器與處理伺服器之間的連線問題所造成，或處理伺服器與 Azure 之間的連線問題所造成。

若要解決這些問題，請[針對連線能力和複寫進行疑難排解](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication)。




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>步驟 3：針對不適用於複寫的來源機器進行疑難排解

當您使用 Site Recovery 來嘗試選取來源機器以啟用複寫時，該機器可能會因下列其中一個原因而無法供您使用：

* **具有相同 UUID 的兩部虛擬機器**：如果 vCenter 底下的兩部虛擬機器具有相同的執行個體 UUID，則 Azure 入口網站中會顯示設定伺服器所探索到的第一部虛擬機器。 若要解決此問題，請確定沒有任何兩部虛擬機器具有相同的執行個體 UUID。 在備份 VM 變成作用中並已登入探索記錄的執行個體中，此案例很常見。 請參閱 [Azure Site Recovery VMware 至 Azure：如清除重複或過時的項目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)來解決問題。
* **不正確的 vCenter 使用者認證**：確定您在使用 OVF 範本或統一安裝來設定設定伺服器時，已新增正確的 vCenter 認證。 若要確認您在設定期間所新增的認證，請參閱[修改自動探索所需的認證](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)。
* **vCenter 權限不足**：如果所提供來存取 vCenter 的權限沒有所需的權限，可能會導致探索虛擬機器失敗。 請確定將[準備帳戶以進行自動探索](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)中所述的權限新增至 vCenter 使用者帳戶。
* **Azure Site Recovery 管理伺服器**：如果虛擬機器作為一或多個下列角色-管理伺服器的組態伺服器 /scale-out 處理序伺服器 / 主要目標伺服器，則您無法從入口網站中選擇虛擬機器。 無法複寫管理伺服器。
* **已經透過 Azure Site Recovery 服務保護/容錯移轉**：如果虛擬機器已經透過 Site Recovery 保護或容錯移轉，則在入口網站中便無法選取該虛擬機器來進行保護。 請確定您在入口網站中尋找的虛擬機器，尚未受到任何其他使用者或不同訂用帳戶的保護。
* **vCenter 未連線**：檢查 vCenter 是否處於已連線狀態。 若要確認，請移至 [復原服務保存庫] > [Site Recovery 基礎結構] > [組態伺服器]，按一下個別的組態伺服器，刀鋒視窗隨即在右邊開啟，內含相關聯的伺服器詳細資料。 檢查 vCenter 是否已連線。 如果其處於「未連線」狀態，請解決此問題，然後在入口網站上[重新整理設定伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **ESXi 已關閉**：如果虛擬機器所在的 ESXi 主機處於已關閉狀態，則虛擬機器不會列在 Azure 入口網站上或可在其上選取。 開啟 ESXi 主機電源，在入口網站上[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **擱置的重新開機**：如果虛擬機器上有擱置的重新開機，您就無法在 Azure 入口網站上選取此機器。 務必完成擱置的重新開機活動，[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **找不到 IP**：如果虛擬機器沒有與其相關聯的有效 IP 位址，您就無法在 Azure 入口網站上選取此機器。 務必將有效的 IP 位址指派給虛擬機器，[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>針對在入口網站中顯示為灰色的受保護虛擬機器進行疑難排解

如果系統中有重複項目，在 Azure 入口網站中便無法使用在 Site Recovery 底下複寫的虛擬機器。 若要了解如何刪除過時的項目並解決此問題，請參閱 [Azure Site Recovery VMware 至 Azure：如清除重複或過時的項目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) \(英文\)。

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>在過去 'XXX' 分鐘沒有可供 VM 使用的損毀一致復原點

下列是其中一些最常見的問題

### <a name="initial-replication-issues-error-78169"></a>初始複寫問題 [錯誤 78169]

在確定沒有連線、頻寬或時間同步相關問題的基礎上，請確保：

- 沒有防毒軟體正在封鎖 Azure Site Recovery。 [深入](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)了解 Azure Site Recovery 所需的資料夾排除。

### <a name="source-machines-with-high-churn-error-78188"></a>具有高變換率的來源機器 [錯誤 78188]

可能原因：
- 虛擬機器所列出磁碟上資料變更率 (寫入位元組/秒) 超過複寫目標儲存體帳戶類型的 [Azure Site Recovery 支援限制](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits)。
- 變換率突然暴增，原因是有大量資料等待上傳。

若要解決此問題：
- 確定目標儲存體帳戶類型 (標準或進階) 是根據來源的變換率需求來佈建。
- 如果您已複寫到進階受控磁碟 (asrseeddisk 類型)，請確定磁碟大小根據 Site Recovery 限制支援觀察到的變換率。 如有需要，您可增加 asrseeddisk 的大小。 請遵循下列步驟：
    - 巡覽至受影響複寫機器的 [磁碟] 刀鋒視窗，並複製複本磁碟名稱
    - 巡覽至此複本受控磁碟
    - 您可能會在 [概觀] 刀鋒視窗上看到一個橫幅，其指出已產生 SAS URL。 請按一下此橫幅並取消匯出。 如果看不到橫幅，則請忽略此步驟。
    - 一旦撤銷 SAS URL，請移至受控磁片的 [設定] 分頁並增加大小，讓 Azure Site Recovery 在來源磁片上支援觀察到的流失率
- 如果觀察到的變換為暫時性，請等候幾個小時讓擱置中的資料上傳，以趕上進度並建立復原點。
- 如果磁碟包含非關鍵性資料，例如暫存記錄、測試資料等，請考慮將此資料移至別處，或從複寫中完全排除此磁碟
- 如果問題持續發生，請使用 Site Recovery [部署規劃工具](site-recovery-deployment-planner.md#overview)來協助規劃複寫。

### <a name="source-machines-with-no-heartbeat-error-78174"></a>來源機器沒有任何活動訊號 [錯誤 78174]

當來源機器上的 Azure Site Recovery 行動代理程式未與設定伺服器 (CS) 通訊時，就會發生這種情況。

若要解決此問題，請使用下列步驟來確認從來源 VM 到設定伺服器的網路連線能力：

1. 確認來源機器正在執行。
2. 使用具有系統管理員權限的帳戶來登入來源機器。
3. 確認下列服務正在執行，如果沒有，請重新啟動這些服務：
   - Svagents (InMage Scout VX 代理程式)
   - InMage Scout 應用程式服務
4. 在來源機器上，檢查此位置上的記錄，以取得錯誤詳細資料：

    *C:\Program Files （X86） \Microsoft Azure Site Recovery\agent\svagents \* .log*

### <a name="process-server-with-no-heartbeat-error-806"></a>處理伺服器沒有任何活動訊號 [錯誤 806]
如果沒有任何活動訊號來自處理伺服器 (PS)，請檢查：
1. PS VM 已啟動且正在執行
2. 檢查 PS 上的下列記錄，以取得錯誤詳細資料：

    *C:\ProgramData\ASR\home\svsystems\eventmanager \* .log*\
    和
    *C:\ProgramData\ASR\home\svsystems\ monitor_protection \* .log*

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>主要目標伺服器沒有任何活動訊號 [錯誤 78022]

當主要目標伺服器上的 Azure Site Recovery 行動代理程式未與設定伺服器通訊時，就會發生這種情況。

若要解決此問題，請使用下列步驟來確認服務狀態：

1. 確認主要目標 VM 已啟動且正在執行。
2. 使用具有系統管理員權限的帳戶來登入主要目標 VM。
    - 確認 svagents 服務正在執行。 如果正在執行，請重新啟動該服務
    - 檢查此位置上的記錄，以取得錯誤詳細資料：

        *C:\Program Files （X86） \Microsoft Azure Site Recovery\agent\svagents \* .log*
3. 若要向設定伺服器註冊主要目標伺服器，請巡覽至 **%PROGRAMDATA%\ASR\Agent** 資料夾，然後在命令提示字元上執行下列命令：
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>錯誤識別碼 78144 - 在過去 'XXX' 分鐘沒有可供 VM 使用的應用程式一致復原點

行動代理程式 [9.23](vmware-physical-mobility-service-overview.md#mobility-service-agent-version-923-and-higher) & [9.27](site-recovery-whats-new.md#update-rollup-39) 版中新增了一些增強功能，可處理 VSS 安裝失敗行為。 請確定您是使用最新版本，才能取得針對 VSS 失敗進行疑難排解的最佳指引。

下列是其中一些最常見的問題

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>原因 1：SQL Server 2008/2008 R2 中的已知問題
**修正方式**：SQL Server 2008/2008 R2 有一個已知問題。 請參閱這篇知識庫文章：[在裝載 SQL Server 2008 R2 的伺服器中 Azure Site Recovery Agent 代理程式或其他非元件 VSS 的備份失敗](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>原因 2：在裝載任何 SQL Server 執行個體 (具有 AUTO_CLOSE 資料庫) 版本的伺服器上，Azure Site Recovery 作業失敗
**修正方式**：請參閱知識庫[文章](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>原因 3：SQL Server 2016 和 2017 中的已知問題
**修正方式**：請參閱知識庫[文章](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component)

#### <a name="cause-4-app-consistency-not-enabled-on-linux-servers"></a>原因4： Linux 伺服器上未啟用應用程式一致性
**如何修正**：適用于 Linux 作業系統的 Azure Site Recovery 支援應用程式的自訂腳本，以進行應用程式一致性。 具有前置和後置選項的自訂腳本，會由 Azure Site Recovery 行動代理程式用於應用程式一致性。 [以下](./site-recovery-faq.md#replication)是啟用它的步驟。

### <a name="more-causes-due-to-vss-related-issues"></a>造成 VSS 相關問題的其他原因：

若要進一步進行疑難排解，請檢查來源機器上的檔案，以取得失敗的確切錯誤碼：

*C:\Program Files （x86） \Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log*

如何找出檔案中的錯誤？
透過在編輯器中開啟 vacp.log 檔案來搜尋字串 "vacpError"

`Ex: `**`vacpError`**`:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|`

在上述範例中，**2147754994** 為錯誤碼，其用於告知如下所示的失敗相關資訊：

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>未安裝 VSS 寫入器 - 錯誤 2147221164

修正方式：為了產生應用程式一致性標籤，Azure Site Recovery 會使用 Microsoft 磁碟區陰影複製服務 (VSS)。 這會為其作業安裝 VSS 提供者，以取得應用程式一致性快照集。 此 VSS 提供者會安裝為服務。 如果未安裝 VSS 提供者服務，應用程式一致性快照集建立會失敗，並出現錯誤識別碼0x80040154 「類別未註冊」。 </br>
請參閱 [VSS 寫入器安裝疑難排解的文章](./vmware-azure-troubleshoot-push-install.md#vss-installation-failures)

#### <a name="vss-writer-is-disabled---error-2147943458"></a>已停用 VSS 寫入器 - 錯誤　2147943458

修正方式：為了產生應用程式一致性標籤，Azure Site Recovery 會使用 Microsoft 磁碟區陰影複製服務 (VSS)。 這會為其作業安裝 VSS 提供者，以取得應用程式一致性快照集。 此 VSS 提供者會安裝為服務。 如果停用 VSS 提供者服務，應用程式一致性快照集建立會失敗，並出現錯誤識別碼「指定的服務已停用且無法啟動（0x80070422）」。 </br>

- 如果已停用 VSS，
    - 請確認 VSS 提供者服務的啟動類型設定為 [自動]。
    - 請重新啟動下列服務：
        - VSS 服務
        - Azure Site Recovery VSS 提供者
        - VDS 服務

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS 提供者未登錄 - 錯誤 2147754756

**修正方式**：為了產生應用程式一致性標籤，Azure Site Recovery 會使用 Microsoft 磁碟區陰影複製服務 (VSS)。
檢查是否已安裝 Azure Site Recovery VSS 提供者服務。 </br>

- 使用下列命令重試提供者安裝：
- 解除安裝現有的提供者：C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- 重新安裝：C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

請確認 VSS 提供者服務的啟動類型設定為 [自動]。
    - 請重新啟動下列服務：
        - VSS 服務
        - Azure Site Recovery VSS 提供者
        - VDS 服務

## <a name="next-steps"></a>後續步驟

如果需要更多協助，請將問題貼到 [Azure Site Recovery 的 Microsoft 問與答頁面](/answers/topics/azure-site-recovery.html)。 我們有一個使用中的社群，我們的其中一位工程師將可協助您。
