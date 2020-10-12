---
title: 針對使用 Azure Site Recovery 的 Azure Vm 複寫進行疑難排解
description: 使用 Azure Site Recovery 針對 Azure VM 嚴重損壞修復中的複寫進行疑難排解
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: dc14334668b76ee8cbb81e48abfe1eecf17fa138
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86130395"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>針對 Azure VM 嚴重損壞修復中的複寫進行疑難排解

本文說明當您將 Azure 虛擬機器從一個區域 (VM) 複寫和復原到另一個區域時，Azure Site Recovery 的常見問題。 此外，也會說明如何針對常見問題進行疑難排解。 如需受支援組態的詳細資訊，請參閱[複寫 Azure VM 的支援矩陣](./azure-to-azure-support-matrix.md)。

Azure Site Recovery 一致地將資料從來源區域複製到嚴重損壞修復區域。 它也會每隔5分鐘建立一個損毀一致復原點。 如果 Site Recovery 長達 60 分鐘無法建立復原點，則以此資訊對您提出警示：

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

下列各節說明原因和解決方案。

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>來源虛擬機器上的高資料變更率

如果來源虛擬機器上的資料變更率高於支援的限制，Azure Site Recovery 會建立事件。 若要查看問題是否因為高變換而造成，請移至複寫的**專案**  >  **VM**  >  **事件-過去72小時**。
您應該會看到事件 **資料變更率超過支援的限制**：

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="顯示高資料變更率太高的 Azure Site Recovery 頁面。":::

如果您選取此事件，應會看到確切的磁碟資訊：

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="顯示高資料變更率太高的 Azure Site Recovery 頁面。":::

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制

下表提供 Azure Site Recovery 限制。 這些限制是以我們的測試為基礎，但無法涵蓋所有可能的應用程式輸入輸出 (i/o) 組合。 實際的結果會隨著您的應用程式 I/O 混合而有所不同。

有兩個要考慮的限制：每個磁片的資料變換和每部虛擬機器的資料變換。 讓我們看看下表中的 Premium P20 磁片，以取得範例。 針對單一 VM，Site Recovery 可以處理每個磁片 5 MB/s 的變換，最多五個這類磁片。 Site Recovery 的限制為每個 VM 的總流失 54 MB/秒。

**複寫儲存體目標** | **來源磁碟的平均 I/O 大小** |**來源磁碟的平均資料變換率** | **來源磁碟資料的每日整體資料變換率**
---|---|---|---
標準儲存體 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 16 KB | 4 MB/秒 |    每個磁碟 336 GB
進階 P10 或 P15 磁碟 | 32 KB 或更大 | 8 MB/秒 | 每個磁碟 672 GB
進階 P20、P30、P40 或 P50 磁碟 | 8 KB    | 5 MB/秒 | 每個磁碟 421 GB
進階 P20、P30、P40 或 P50 磁碟 | 16 KB 或更大 |20 MB/秒 | 每個磁碟 1684 GB

### <a name="solution"></a>解決方法

根據磁片類型而定，Azure Site Recovery 有資料變更率的限制。 若要查看此問題是週期性或暫時性的，請找出受影響虛擬機器的資料變更率。 移至來源虛擬機器，在 [監視]**** 底下尋找計量，並新增如此螢幕擷取畫面所示的計量：

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="顯示高資料變更率太高的 Azure Site Recovery 頁面。":::

1. 選取 [新增計量]****，然後新增 [OS 磁碟寫入位元組/秒]**** 和 [資料磁碟寫入位元組/秒]****。
1. 監視如螢幕擷取畫面所示的激增。
1. 檢視發生於 OS 磁碟和所有資料磁碟的整體寫入作業。 這些計量可能不會提供給您每個磁碟層級的資訊，但表示資料變換率的整體模式。

資料變更率的尖峰可能來自偶爾的資料高載。 如果資料變更率大於 (Premium) 的 10 MB/s，或標準) 的 2 MB/s (，則複寫會趕上。 如果變換的效果持續超過支援的限制，請考慮下列其中一個選項：

- 排除造成高資料變更率的磁片：首先，停用複寫。 然後，您可以使用 [PowerShell](azure-to-azure-exclude-disks.md)來排除磁片。
- 變更嚴重損壞修復儲存體磁片的層級：只有當磁片資料變換小於 20 MB/秒時，才可能有此選項。 例如，具有 P10 磁片的 VM 的資料變換率大於 8 MB/秒，但小於 10 MB/秒。 如果客戶可以在保護期間將 P30 磁碟用於目標儲存體，則可解決此問題。 只有使用 Premium-Managed 磁片的電腦可以使用此解決方案。 請遵循下列步驟：

  1. 移至受影響複寫機器的 **磁片** ，然後複製複本磁片名稱。
  1. 移至此受控磁片的複本。
  1. 您可能會在 **總覽** 中看到橫幅，指出已產生 SAS URL。 選取此橫幅並取消匯出。 如果您沒有看到橫幅，請略過此步驟。
  1. 一旦 SAS URL 遭到撤銷，請 **移至受控磁片的設定** 。 增加大小，讓 Site Recovery 在來源磁片上支援觀察到的變換率。

## <a name="network-connectivity-problems"></a>網路連線問題

### <a name="network-latency-to-a-cache-storage-account"></a>快取儲存體帳戶的網路延遲

Site Recovery 會將複寫的資料傳送到快取儲存體帳戶。 如果從虛擬機器將資料上傳至快取儲存體帳戶的速度低於3秒的 4 MB，您可能會遇到網路延遲。

若要檢查與延遲相關的問題，請使用 [AzCopy](../storage/common/storage-use-azcopy-v10.md)。 您可以使用此命令列公用程式，從虛擬機器將資料上傳至快取儲存體帳戶。 如果延遲很高，請檢查您是否使用網路虛擬裝置 (NVA) 來控制來自 Vm 的輸出網路流量。 如果所有複寫流量都會通過 NVA，系統就可能會對該設備進行節流。

建議您在虛擬網路中為「儲存體」建立一個網路服務端點，這樣複寫流量就不會流向 NVA。 如需詳細資訊，請參閱[網路虛擬設定組態](azure-to-azure-about-networking.md#network-virtual-appliance-configuration)。

### <a name="network-connectivity"></a>網路連線

若要讓 Site Recovery 複寫正常運作，它需要 VM 提供特定 Url 或 IP 範圍的輸出連線能力。 您的 VM 可能會位於防火牆後方，或使用網路安全性群組 (NSG) 規則來控制輸出連線能力。 若是如此，您可能會遇到問題。 若要確定所有 Url 都已連線，請參閱 [url 的輸出連線能力](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)。

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>錯誤 ID 153006-過去 "X" 分鐘內沒有可供 VM 使用的應用程式一致復原點

以下是一些最常見的問題。

### <a name="known-issue-in-sql-server-20082008-r2"></a>SQL server 2008/2008 R2 中的已知問題

**如何修正：** SQL server 2008/2008 R2 有已知的問題。 [如需裝載 SQL Server 2008 R2 的伺服器，請參閱 Azure Site Recovery 代理程式或其他非元件 VSS 備份失敗](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)的文章。

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>使用 AUTO_CLOSE Db 裝載任何 SQL Server 實例版本的伺服器上 Azure Site Recovery 工作失敗

**如何修正：** 請參閱 [非元件 VSS 備份的文章，例如 Azure Site Recovery 工作在裝載具有 AUTO_CLOSE db SQL Server 實例的伺服器上失敗](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)。

### <a name="known-issue-in-sql-server-2016-and-2017"></a>SQL Server 2016 和 2017 中的已知問題

**如何修正**：請參閱 [SQL Server 2017 的累計更新 16](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017)文章。

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>您目前使用 Azure 儲存體空間直接存取設定

**如何修正**： Azure Site Recovery 無法為儲存空間直接存取設定建立應用程式一致的復原點。 [設定複寫原則](azure-to-azure-how-to-enable-replication-s2d-vms.md)。

### <a name="app-consistency-not-enabled-on-linux-servers"></a>Linux 伺服器上未啟用的應用程式一致性

**如何修正** ：適用于 Linux 作業系統的 Azure Site Recovery 支援應用程式自訂腳本以進行應用程式一致性。 使用前置和後置選項的自訂腳本，將會由 Azure Site Recovery 行動代理程式用來進行應用程式一致性。 [以下](./site-recovery-faq.md#replication) 是啟用它的步驟。

### <a name="more-causes-because-of-vss-related-issues"></a>有更多原因是因為 VSS 相關的問題：

若要進一步進行疑難排解，請檢查來源機器上的檔案，以取得正確的失敗錯誤碼：

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

若要找出錯誤，請在文字編輯器中開啟 _vacp.exe_ 檔案，以搜尋 **vacpError**的字串。

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

在上述範例中， **2147754994** 是指出在此句子之後失敗的錯誤碼。

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>未安裝 VSS 寫入器 - 錯誤 2147221164

**如何修正**：若要產生應用程式一致性標記，Azure Site Recovery 使用磁碟區陰影複製服務 (VSS) 。 Site Recovery 會安裝 VSS 提供者，以取得應用程式一致性快照集的作業。 Azure Site Recovery 將此 VSS 提供者安裝為服務。 如果未安裝 VSS 提供者，則會建立應用程式一致性快照集。 它會顯示 **未註冊的錯誤 ID 0X80040154 類別**。 請參閱適用于 [VSS 寫入器安裝疑難排解](vmware-azure-troubleshoot-push-install.md#vss-installation-failures)的文章。

#### <a name="vss-writer-is-disabled---error-2147943458"></a>已停用 VSS 寫入器 - 錯誤　2147943458

**如何修正**：若要產生應用程式一致性標記，Azure Site Recovery 會使用 VSS。 Site Recovery 會安裝 VSS 提供者，以取得應用程式一致性快照集的作業。 此 VSS 提供者會安裝為服務。 如果您未啟用 VSS 提供者服務，應用程式一致性快照集建立將會失敗。 它會顯示錯誤： **指定的服務已停用，且無法 (0x80070422) 啟動 **。

如果 VSS 已停用：

- 請確認 VSS 提供者服務的啟動類型設定為 [自動]。
- 請重新啟動下列服務：
  - VSS 服務。
  - Azure Site Recovery VSS 提供者。
  - VDS 服務。

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS 提供者未登錄 - 錯誤 2147754756

**如何修正**：若要產生應用程式一致性標記，Azure Site Recovery 會使用 VSS。 檢查是否已安裝 Azure Site Recovery VSS 提供者服務。

使用下列命令來重新安裝 VSS 提供者：

1. 卸載現有提供者：

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. 重新安裝 VSS 提供者：

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

請確認 VSS 提供者服務的啟動類型設定為 [自動]。

請重新啟動下列服務：

- VSS 服務。
- Azure Site Recovery VSS 提供者。
- VDS 服務。
