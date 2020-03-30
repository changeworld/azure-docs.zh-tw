---
title: 使用 Azure 網站恢復對 Azure VM 的複製進行故障排除
description: 使用 Azure 網站恢復在 Azure VM 災害復原中排除複製故障
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: 67b68cc8a1db4a058675dc51fb3805093c455908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276660"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>在 Azure VM 災害復原中排除複製故障

本文說明當您在區域間複寫及復原 Azure 虛擬機器時的 Azure Site Recovery 常見問題。 它還解釋了如何排除常見問題。 如需受支援組態的詳細資訊，請參閱[複寫 Azure VM 的支援矩陣](site-recovery-support-matrix-azure-to-azure.md)。

Azure 網站恢復始終將資料從源區域複製到災害復原區域。 它還每 5 分鐘創建一個崩潰一致的復原點。 如果 Site Recovery 長達 60 分鐘無法建立復原點，則以此資訊對您提出警示：

錯誤訊息："在過去 60 分鐘內沒有 VM 可用的崩潰一致復原點。</br>
錯誤 ID： 153007

下列各節說明原因和解決方案。

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a><a name="high-data-change-rate-on-the-source-virtal-machine"></a>來源虛擬機器上的高資料變更率

如果源虛擬機器上的資料更改速率高於支援的限制，Azure 網站恢復將創建事件。 要查看問題是否由於高改動，轉到**複製項** > **VM** > **事件 - 持續 72 小時**。
您應該會看到事件"資料更改率超出受支援的限制"：

![Azure 網站恢復頁，顯示過高的資料更改率](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

如果您選取此事件，應會看到確切的磁碟資訊：

![顯示資料更改率事件詳細資訊的頁面](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制

下表提供 Azure Site Recovery 限制。 這些限制基於我們的測試，但它們不能涵蓋所有可能的應用程式輸入輸出 （I/O） 組合。 實際的結果會隨著您的應用程式 I/O 混合而有所不同。

需要考慮兩個限制：每個磁片的資料改動和每個虛擬機器的資料改動。 讓我們看下表中的高級 P20 磁片的示例。 對於單個 VM，網站恢復可以處理每個磁片 5 MB/s 的改動，最多隻能處理 5 個此類磁片。 網站恢復每個 VM 的總改動量限制為 25 MB/s。

**複製存儲目標** | **來源磁碟的平均 I/O 大小** |**來源磁碟的平均資料變換率** | **來源磁碟資料的每日整體資料變換率**
---|---|---|---
標準儲存體 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 16 KB | 4 MB/秒 |    每個磁碟 336 GB
進階 P10 或 P15 磁碟 | 32 KB 或更大 | 8 MB/秒 | 每個磁碟 672 GB
進階 P20、P30、P40 或 P50 磁碟 | 8 KB    | 5 MB/秒 | 每個磁碟 421 GB
進階 P20、P30、P40 或 P50 磁碟 | 16 KB 或更大 |20 MB/s | 每個磁片 1684 GB

### <a name="solution"></a>解決方法

Azure 網站恢復對資料更改速率有限制，具體取決於磁片類型。 要查看此問題是重複的還是臨時的，查找受影響的虛擬機器的資料更改率。 移至來源虛擬機器，在 [監視]**** 底下尋找計量，並新增如此螢幕擷取畫面所示的計量：

![顯示查找資料更改速率的三步過程的頁面](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. 選取 [新增計量]****，然後新增 [OS 磁碟寫入位元組/秒]**** 和 [資料磁碟寫入位元組/秒]****。
1. 監視如螢幕擷取畫面所示的激增。
1. 檢視發生於 OS 磁碟和所有資料磁碟的整體寫入作業。 這些計量可能不會提供給您每個磁碟層級的資訊，但表示資料變換率的整體模式。

資料更改速率的峰值可能來自偶爾的資料突發。 如果資料更改速率大於 10 MB/s（對於高級版）或 2 MB/s（標準版）並停機，則複製將迎頭趕上。 如果改動始終超出支援的限制，請考慮以下選項之一：

- 排除導致高資料更改速率的磁片：首先，禁用複製。 然後，您可以使用[PowerShell](./azure-to-azure-exclude-disks.md)排除磁片。
- 更改災害復原存儲磁片的層：僅當磁片資料改動小於 20 MB/s 時，才可以使用此選項。 假設具有 P10 磁片的 VM 的資料改動量大於 8 MB/s，但小於 10 MB/s。 如果客戶可以在保護期間將 P30 磁碟用於目標儲存體，則可解決此問題。 此解決方案僅適用于使用高級管理磁片的電腦。 請遵循下列步驟：

    1. 轉到受影響的複製電腦的**磁片**並複製副本磁片名稱。
    1. 轉到託管磁片的此副本。
    1. 您可能會在 **"概述"** 中看到一個橫幅，該橫幅顯示已生成 SAS URL。 選擇此橫幅並取消匯出。 如果看不到橫幅，請忽略此步驟。
    1. 一旦 SAS URL 被吊銷，轉到託管磁片的**配置**。 增加大小，以便網站恢復支援源磁片上的觀察到的改動率。

## <a name="network-connectivity-problems"></a><a name="Network-connectivity-problem"></a>網路連線問題

### <a name="network-latency-to-a-cache-storage-account"></a>快取儲存體帳戶的網路延遲

Site Recovery 會將複寫的資料傳送到快取儲存體帳戶。 如果將資料從虛擬機器上載到緩存存儲帳戶在 3 秒內低於 4 MB，則可能會遇到網路延遲。

要檢查與延遲相關的問題，請使用[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)。 您可以使用此命令列實用程式將資料從虛擬機器上載到緩存存儲帳戶。 如果延遲較高，請檢查您是否使用網路虛擬裝置 （NVA） 來控制來自 VM 的出站網路流量。 如果所有複寫流量都會通過 NVA，系統就可能會對該設備進行節流。

建議您在虛擬網路中為「儲存體」建立一個網路服務端點，這樣複寫流量就不會流向 NVA。 如需詳細資訊，請參閱[網路虛擬設定組態](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)。

### <a name="network-connectivity"></a>網路連線

要使網站恢復複製正常工作，需要 VM 提供與特定 URL 或 IP 範圍的出站連接。 您可能將 VM 放在防火牆後面，或者使用網路安全性群組 （NSG） 規則來控制出站連接。 如果是這樣，您可能會遇到問題。 若要確保所有 URL 皆已連線，請參閱 [Site Recovery URL 的輸出連線能力](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)。

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>錯誤 ID 153006 - 過去"X"分鐘中沒有可用於 VM 的應用一致性復原點

以下是一些最常見的問題。

#### <a name="known-issue-in-sql-server-20082008-r2"></a>SQL 伺服器 2008/2008 R2 中的已知問題

**如何修復**：SQL 伺服器 2008/2008 R2 存在已知問題。 請參閱文章[Azure 網站修復代理或其他非元件 VSS 備份失敗，對於託管 SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)的伺服器。

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Azure 網站恢復作業在承載具有AUTO_CLOSE GB 的任何版本的 SQL Server 實例的伺服器上失敗

**如何修復**：請參閱一些[非元件 VSS 備份，例如 Azure 網站恢復作業在承載具有AUTO_CLOSE GB 的 SQL Server 實例的伺服器上失敗](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)。


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>SQL Server 2016 和 2017 中的已知問題

**如何修復**： 請參閱在[SQL Server 2016 和 2017 中備份具有非基於元件的備份的虛擬機器時發生的錯誤](https://support.microsoft.com/en-us/help/4508218/cumulative-update-16-for-sql-server-2017)。

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>您正在使用 Azure 存儲空間直接配置

**如何修復**：Azure 網站恢復無法為存儲空間直接配置創建應用程式一致的復原點。 [配置複寫原則](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms)。

### <a name="more-causes-because-of-vss-related-issues"></a>更多原因由於 VSS 相關問題：

要進一步疑難排解，請檢查源電腦上的檔以獲取有關故障的確切錯誤代碼：

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

要查找檔中的錯誤，請在編輯器中打開 vacp.log 檔來搜索字串"vacpError"。

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

在前面的示例中 **，2147754994**是錯誤代碼，用於告訴您此句子之後的故障。

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>未安裝 VSS 寫入器 - 錯誤 2147221164

**如何修復**：要生成應用程式一致性標記，Azure 網站恢復使用卷卷卷影副本服務 （VSS）。 網站恢復安裝 VSS 提供程式的操作以拍攝應用一致性快照。 Azure 網站恢復將此 VSS 提供程式安裝為服務。 如果未安裝 VSS 提供程式，則應用程式一致性快照創建將失敗。 它顯示錯誤 ID 0x80040154"未註冊類"。 請參閱[VSS 編寫器安裝故障排除](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)的文章。

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 編寫器已禁用 - 錯誤 2147943458

**如何修復**：要生成應用程式一致性標記，Azure 網站恢復使用 VSS。 網站恢復安裝 VSS 提供程式的操作以拍攝應用一致性快照。 此 VSS 提供程式作為服務安裝。 如果未啟用 VSS 提供程式服務，則應用程式一致性快照創建將失敗。 它顯示錯誤"指定的服務已禁用，無法啟動 （0x80070422）。

如果禁用 VSS：

- 驗證 VSS 提供程式服務的啟動類型是否設置為 **"自動**"。
- 重新啟動下列服務：
   - VSS 服務
   - Azure Site Recovery VSS 提供者
   - VDS 服務

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS 供應商NOT_REGISTERED - 錯誤 2147754756

**如何修復**：要生成應用程式一致性標記，Azure 網站恢復使用 VSS。 檢查是否安裝了 Azure 網站恢復 VSS 提供程式服務。

使用以下命令重新安裝 VSS 提供程式：
1. 卸載現有提供程式：C：\程式檔 （x86）\微軟 Azure 網站恢復\代理\InMageVSSProvider_Uninstall.cmd
1. 重新安裝 VSS 提供程式：C：\程式檔 （x86）\微軟 Azure 網站恢復\代理\InMageVSSProvider_Install.cmd

驗證 VSS 提供程式服務的啟動類型是否設置為 **"自動**"。

重新啟動下列服務：
- VSS 服務
- Azure Site Recovery VSS 提供者
- VDS 服務
