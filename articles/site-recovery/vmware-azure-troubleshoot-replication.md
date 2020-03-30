---
title: 針對使用 Azure Site Recovery 從 VMware VM 和實體伺服器至 Azure 的災害復原複寫問題進行疑難排解 | Microsoft Docs
description: 本文針對使用 Azure Site Recovery 將 VMware VM 和實體伺服器復原至 Azure 之災害復原期間發生的常見複寫問題，提供疑難排解資訊。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 7237bb7e0538ba1a9b6333ccb6589efe657a247d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74423956"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>針對 VMware VM 和實體伺服器的複寫問題進行疑難排解

本文介紹了在使用[網站恢復](site-recovery-overview.md)將本地 VMware VM 和物理伺服器複製到 Azure 時可能會遇到的一些常見問題和特定錯誤。

## <a name="step-1-monitor-process-server-health"></a>第 1 步：監視進程伺服器運行狀況

網站恢復使用[進程伺服器](vmware-physical-azure-config-process-server-overview.md#process-server)接收和優化複製的資料，並將其發送到 Azure。

我們建議您監視門戶中進程伺服器的運行狀況，以確保它們連接並正常工作，並且與進程伺服器關聯的源電腦的複製正在進行。

- [瞭解](vmware-physical-azure-monitor-process-server.md)監視進程伺服器。
- [檢閱最佳作法](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- 排除進程伺服器運行狀況[的故障](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health)。

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>第 2 步：解決連接和複製問題

初始和持續複製失敗通常是由源伺服器與進程伺服器之間的連接問題或進程伺服器和 Azure 之間的連接問題引起的。 

要解決這些問題，[請使用故障排除連接和複製](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication)。




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>第 3 步：對不可用複製的源電腦進行故障排除

當您使用 Site Recovery 來嘗試選取來源機器以啟用複寫時，該機器可能會因下列其中一個原因而無法供您使用：

* **具有相同實例 UUID 的兩個虛擬機器**：如果 vCenter 下的兩個虛擬機器具有相同的實例 UUID，則佈建服務器發現的第一個虛擬機器將顯示在 Azure 門戶中。 若要解決此問題，請確定沒有任何兩部虛擬機器具有相同的執行個體 UUID。 此方案通常出現在備份 VM 處於活動狀態並登錄到我們的發現記錄中的實例中。 請參閱[Azure 網站恢復 VMware 到 Azure：如何清理重複或陳舊的條目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)以進行解析。
* **vCenter 使用者憑據不正確**：請確保使用 OVF 範本或統一設置設置佈建服務器時添加了正確的 vCenter 憑據。 若要確認您在設定期間所新增的認證，請參閱[修改自動探索所需的認證](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)。
* **vCenter 許可權不足**：如果為訪問 vCenter 提供的許可權沒有所需的許可權，則可能會出現無法發現虛擬機器的情況。 請確定將[準備帳戶以進行自動探索](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)中所述的權限新增至 vCenter 使用者帳戶。
* **Azure 網站恢復管理伺服器**：如果虛擬機器在以下一個或多個角色下用作管理伺服器 - 佈建服務器/橫向擴展進程伺服器/主目標伺服器，則您將無法從門戶中選擇虛擬機器。 無法複寫管理伺服器。
* **已通過 Azure 網站恢復服務保護/故障：** 如果虛擬機器已通過網站恢復受到保護或故障，則虛擬機器無法選擇在門戶中進行保護。 請確定您在入口網站中尋找的虛擬機器，尚未受到任何其他使用者或不同訂用帳戶的保護。
* **vCenter 未連接**：檢查 vCenter 是否處於連接狀態。 若要確認，請移至 [復原服務保存庫] > [Site Recovery 基礎結構] > [組態伺服器]，按一下個別的組態伺服器，刀鋒視窗隨即在右邊開啟，內含相關聯的伺服器詳細資料。 檢查 vCenter 是否已連線。 如果處於"未連接"狀態，請解決此問題，然後[刷新門戶上的佈建服務器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **ESXi 已關閉**電源：如果虛擬機器所在的 ESXi 主機處於關閉狀態，則虛擬機器將不會列出或在 Azure 門戶上處於不可選擇狀態。 開啟 ESXi 主機電源，在入口網站上[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **掛起重新開機**：如果虛擬機器上存在掛起的重新開機，則將無法在 Azure 門戶上選擇該電腦。 務必完成擱置的重新開機活動，[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **未找到 IP：** 如果虛擬機器沒有與其關聯的有效 IP 位址，則將無法在 Azure 門戶中選擇該電腦。 務必將有效的 IP 位址指派給虛擬機器，[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>在門戶中灰顯的受保護虛擬機器的故障排除

如果系統中有重複項目，在 Azure 入口網站中便無法使用在 Site Recovery 底下複寫的虛擬機器。 要瞭解如何刪除陳舊條目並解決問題，請參閱[Azure 網站恢復 VMware 到 Azure：如何清理重複項或過時條目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)。

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>在過去"XXX"分鐘內沒有 VM 可用的崩潰一致復原點

下面列出了一些最常見的問題

### <a name="initial-replication-issues-error-78169"></a>初始複製問題 [錯誤 78169]

在上面確保沒有連接、頻寬或時間同步相關問題，確保：

- 沒有防毒軟體阻止 Azure 網站恢復。 [詳細瞭解](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)Azure 網站恢復所需的資料夾排除項。

### <a name="source-machines-with-high-churn-error-78188"></a>高多動量的源電腦 [錯誤 78188]

可能的原因：
- 虛擬機器列出的磁片上的資料更改率（寫入位元組/秒）超過[Azure 網站恢復支援的](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits)複製目標存儲帳戶類型限制。
- 由於大量資料有待上傳，流失率突然飆升。

若要解決此問題：
- 確保根據源的改動率要求預配目標存儲帳戶類型（標準帳戶或高級帳戶）。
- 如果您已複製到高級託管磁片（asrseeddisk 類型），請確保磁片的大小支援按網站恢復限制觀察到的改動率。 如果需要，可以增加 asrseeddisk 的大小。 請遵循下列步驟：
    - 導航到受影響的複製電腦的磁片邊欄選項卡並複製副本磁片名稱
    - 導航到此副本託管磁片
    - 您可能會在"概述"邊欄選項卡上看到一條橫幅，指出已生成 SAS URL。 按一下此橫幅並取消匯出。 如果看不到橫幅，請忽略此步驟。
    - 一旦 SAS URL 被吊銷，轉到託管磁片的配置邊欄選項卡並增加大小，以便 ASR 支援源磁片上的觀察到的改動率
- 如果觀察到的改動是暫時的，請等待幾個小時，等待掛起的資料上載趕上並創建復原點。
- 如果磁片包含非關鍵資料（如臨時日誌、測試資料等），請考慮將此資料移到其他位置，或完全排除此磁片的複製
- 如果問題仍然存在，請使用網站恢復[部署計畫程式](site-recovery-deployment-planner.md#overview)來説明規劃複製。

### <a name="source-machines-with-no-heartbeat-error-78174"></a>無活動訊號的源電腦 [錯誤 78174]

當源電腦上的 Azure 網站恢復移動代理未與佈建服務器 （CS） 通信時，將發生這種情況。

要解決此問題，請使用以下步驟驗證從源 VM 到 Config 伺服器的網路連接：

1. 驗證源電腦是否正在運行。
2. 使用具有管理員許可權的帳戶登錄到源電腦。
3. 驗證以下服務是否正在運行，如果不重新開機服務：
   - Svagents (InMage Scout VX 代理程式)
   - InMage Scout 應用程式服務
4. 在源電腦上，檢查位置的日誌以瞭解錯誤詳細資訊：

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>沒有活動訊號的進程伺服器 [錯誤 806]
如果進程伺服器 （PS） 沒有活動訊號，請檢查：
1. PS VM 已啟動並運行
2. 有關錯誤詳細資訊，請查看 PS 上的以下日誌：

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>無活動訊號的主目標伺服器 [錯誤 78022]

當主目標的 Azure 網站恢復移動代理未與佈建服務器通信時，將發生這種情況。

要解決此問題，請使用以下步驟驗證服務狀態：

1. 驗證主目標 VM 是否正在運行。
2. 使用具有管理員許可權的帳戶登錄到主目標 VM。
    - 驗證 svagent 服務是否正在運行。 如果正在運行，請重新開機服務
    - 檢查位置的日誌以瞭解錯誤詳細資訊：
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. 要向佈建服務器註冊主目標，請導航到資料夾 **%PROGRAMDATA%\ASR\代理**，並在命令提示符上運行以下內容：
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>錯誤 ID 78144 - 在過去"XXX"分鐘內沒有可用於 VM 的應用一致性復原點

移動代理[9.23](vmware-physical-mobility-service-overview.md#from-923-version-onwards) & [9.27](site-recovery-whats-new.md#update-rollup-39)版本進行了增強，以處理 VSS 安裝失敗行為。 確保您處於最新版本上，以瞭解有關 VSS 故障故障排除的最佳指南。

下面列出了一些最常見的問題

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>原因 1：SQL 伺服器 2008/2008 R2 中的已知問題 
**如何修復**：SQL 伺服器 2008/2008 R2 存在已知問題。 請參閱此 KB 文章[Azure 網站修復代理或其他非元件 VSS 備份失敗，對於託管 SQL Server 2008 R2 的伺服器](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>原因 2：Azure 網站恢復作業在承載具有AUTO_CLOSE GB 的任何版本的 SQL Server 實例的伺服器上失敗 
**如何修復**：請參閱 Kb[文章](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>原因 3：SQL Server 2016 和 2017 中的已知問題
**如何修復**：請參閱 Kb[文章](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 


### <a name="more-causes-due-to-vss-related-issues"></a>由於 VSS 相關問題，更多原因：

要進一步疑難排解，請檢查源電腦上的檔以獲取有關故障的確切錯誤代碼：
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

如何查找檔中的錯誤？
通過在編輯器中打開 vacp.log 檔來搜索字串"vacpError"
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

在上面的示例中**2147754994**是告訴您故障的錯誤代碼，如下所示

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>未安裝 VSS 寫入器 - 錯誤 2147221164 

*如何修復*：要生成應用程式一致性標記，Azure 網站恢復使用 Microsoft 卷卷卷影副本服務 （VSS）。 它安裝 VSS 提供程式以進行操作以拍攝應用一致性快照。 此 VSS 提供程式作為服務安裝。 如果不安裝 VSS 提供程式服務，則應用程式一致性快照創建失敗，錯誤 ID 0x80040154"類未註冊"。 </br>
[有關 VSS 編寫器安裝故障排除的參考文章](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 編寫器已禁用 - 錯誤 2147943458

**如何修復**：要生成應用程式一致性標記，Azure 網站恢復使用 Microsoft 卷卷卷影副本服務 （VSS）。 它安裝 VSS 提供程式以進行操作以拍攝應用一致性快照。 此 VSS 提供程式作為服務安裝。 如果 VSS 提供程式服務被禁用，則應用程式一致性快照創建失敗，錯誤 ID"指定的服務已禁用，無法啟動 （0x80070422）"。 </br>

- 如果禁用 VSS，
    - 驗證 VSS 提供程式服務的啟動類型是否設置為 **"自動**"。
    - 重新啟動下列服務：
        - VSS 服務
        - Azure Site Recovery VSS 提供者
        - VDS 服務

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS 供應商NOT_REGISTERED - 錯誤 2147754756

**如何修復**：要生成應用程式一致性標記，Azure 網站恢復使用 Microsoft 卷卷卷影副本服務 （VSS）。 檢查是否安裝了 Azure 網站恢復 VSS 提供程式服務。 </br>

- 使用以下命令重試提供程式安裝：
- 卸載現有提供程式：C：\程式檔 （x86）\微軟 Azure 網站恢復\代理\InMageVSSProvider_Uninstall.cmd
- 重新安裝：C：\程式檔 （x86）\微軟 Azure 網站恢復\代理\InMageVSSProvider_Install.cmd
 
驗證 VSS 提供程式服務的啟動類型是否設置為 **"自動**"。
    - 重新啟動下列服務：
        - VSS 服務
        - Azure Site Recovery VSS 提供者
        - VDS 服務

## <a name="next-steps"></a>後續步驟

如果您需要更多協助，請將您的問題貼到 [Azure Site Recovery 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) \(英文\) 中。 我們有一個使用中的社群，我們的其中一位工程師將可協助您。
