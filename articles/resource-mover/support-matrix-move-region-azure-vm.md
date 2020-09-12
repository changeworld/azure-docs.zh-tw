---
title: 使用 Azure 資源移動器將 Azure Vm 移至另一個區域的支援矩陣
description: 請參閱使用 Azure 資源移動器在區域之間移動 Azure Vm 的支援。
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 4462ea0277193f0f8a4112cad5991d1e12c5f600
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652807"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>支援在 Azure 區域之間移動 Azure Vm

本文摘要說明當您使用資源移動器將虛擬機器和相關網路資源移至不同的 Azure 區域時的支援和必要條件。

> [!IMPORTANT]
> Azure 資源移動器目前為預覽狀態。


## <a name="windows-vm-support"></a>Windows VM 支援

資源移動器支援執行這些 Windows 作業系統的 Azure Vm。

**作業系統** | **詳細資料**
--- | ---
Windows Server 2019 | 支援伺服器核心、含有桌面體驗的伺服器。
Windows Server 2016  | 支援的伺服器核心、含有桌面體驗的伺服器。
Windows Server 2012 R2 | 支援。
Windows Server 2012 | 支援。
Windows Server 2008 R2 (含 SP1/SP2) | 支援。<br/><br/> 針對執行 Windows Server 2008 R2 SP1/SP2 的電腦，您必須安裝 Windows [服務堆疊更新 (SSU) ](https://support.microsoft.com/help/4490628) 和 [sha-1 更新](https://support.microsoft.com/help/4474419)。  2019 年 9 月起不支援 SHA-1，而且如果未啟用 SHA-2 程式碼簽署，代理程式擴充功能將不會如預期般安裝/升級。 深入了解 [SHA-2 升級和需求](https://aka.ms/SHA-2KB)。
Windows 10 (x64) | 支援。
Windows 8.1 (x64) | 支援。
Windows 8 (x64) | 支援。
Windows 7 (x64)，含 SP1 和更新版本 | 在執行 Windows 7 SP1 的電腦上安裝 Windows [服務堆疊更新 (SSU) ](https://support.microsoft.com/help/4490628) 和 [SHA-2 更新](https://support.microsoft.com/help/4474419) 。  2019年9月不支援 SHA-1，如果未啟用 SHA-2 程式碼簽署，「準備」步驟將不會成功。 深入了解 [SHA-2 升級和需求](https://aka.ms/SHA-2KB)。


## <a name="linux-vm-support"></a>Linux VM 支援

資源移動支援執行這些 Linux 作業系統的 Azure Vm。

**作業系統** | **詳細資料**
--- | ---
Red Hat Enterprise Linux | 6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、[8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)、8.1
CentOS | 6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、7.7、8.0、8.1
Ubuntu 14.04 LTS 伺服器 | [支援的核心版本](#supported-ubuntu-kernel-versions)
Ubuntu 16.04 LTS 伺服器 | [支援的核心版本](#supported-ubuntu-kernel-versions)<br/><br/> 使用密碼型驗證和登入的 Ubuntu server，以及用來設定雲端 Vm 的雲端初始化套件，可能會在容錯移轉 (上停用密碼登入，視雲端初始化設定) 而定。 您可以重新啟用虛擬機器上的密碼型登入，方法是從 Azure 入口網站中容錯移轉之 VM 的支援 > 疑難排解 > 設定] 功能表 (重設密碼。
Ubuntu 18.04 LTS 伺服器 | [支援的核心版本](#supported-ubuntu-kernel-versions)。
Debian 7 | [支援的核心版本](#supported-debian-kernel-versions)。
Debian 8 | [支援的核心版本](#supported-debian-kernel-versions)。
SUSE Linux Enterprise Server 12 | SP1、SP2、SP3、SP4。 [支援的核心版本](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 和 15 SP1。 [(支援的核心版本)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | SP3
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> 執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3、4 及 5 版 (UEK3、UEK4、UEK5)


### <a name="supported-ubuntu-kernel-versions"></a>支援的 Ubuntu 核心版本

**版本** | **核心版本** 
--- | --- 
14.04 LTS |  3.13.0-24-generic 至 3.13.0-170-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-148-generic、<br/>4.15.0-1023-azure 至 4.15.0-1045-azure 
16.04 LTS |  4.4.0-21-generic 至 4.4.0-171-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-74-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 至 4.15.0-1066-azure
18.04 LTS | 4.15.0-20-generic 至 4.15.0-74-generic </br> 4.18.0-13-generic 至 4.18.0-25-generic </br> 5.0.0-15-generic 至 5.0.0-37-generic </br> 5.3.0-19-generic 至 5.3.0-24-generic </br> 4.15.0-1009-azure 至 4.15.0-1037-azure </br> 4.18.0-1006-azure 至 4.18.0-1025-azure </br> 5.0.0-1012-azure 至 5.0.0-1028-azure </br> 5.3.0-1007-azure 至 5.3.0-1009-azure


### <a name="supported-debian-kernel-versions"></a>支援的 Debian 核心版本 

**版本** |  **核心版本** 
--- |  --- 
Debian 7 |  3.2.0-4-amd64 至 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 
Debian 8 |  3.16.0-4-amd64 至 3.16.0-10-amd64、4.9.0-0.bpo.4-amd64 至 4.9.0-0.bpo.11-amd64 
Debian 8 |  3.16.0-4-amd64 至 3.16.0-10-amd64、4.9.0-0.bpo.4-amd64 至 4.9.0-0.bpo.9-amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>支援的 SUSE Linux Enterprise Server 12 核心版本 

**版本** | **核心版本** 
--- |  --- 
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4)  |  支援所有 [股票 SUSE 12 SP1、SP2、SP3、SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) 核心。</br></br> 4.4.138-4.7-azure 至 4.4.180-4.31-azure、</br>4.12.14-6.3-azure 至 4.12.14-6.34-azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>支援 SUSE Linux Enterprise Server 15 核心版本

**版本** | **核心版本** |
--- |  --- |
SUSE Linux Enterprise Server 15 和 15 SP1 |  支援所有股票 SUSE 15 和15核心。</br></br> 4.12.14-5.5-azure 至 4.12.14-8.22-azure |

## <a name="supported-linux-file-systemguest-storage"></a>支援的 Linux 檔案系統/來賓儲存體

* 檔案系統：ext3、ext4、XFS、BTRFS
* 磁碟區管理員：LVM2
* 多重路徑軟體：裝置對應程式


## <a name="supported-vm-compute-settings"></a>支援的 VM 計算設定

**設定** | **支援** | **詳細資料**
--- | --- | ---
大小 | 至少具有兩個 CPU 核心和 1 GB RAM 的任何 Azure VM 大小 | 確認 [Azure 虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/sizes-general)。
可用性設定組 | 目前不支援 | 如果您使用預設選項將具有可用性設定組的 Azure VM 新增至移動集合，則準備程式會失敗。 您可以選擇將 VM 移至可用性區域，或將它移動為單一實例 VM。 您可以在 [編輯目標屬性] 頁面中修改這些設定。
可用性區域 | 支援 | 支援，視目的地區域支援而定。
Microsoft)  (發佈的 Azure 資源庫影像 | 支援 | 只要 VM 在支援的作業系統上執行即支援。
協力廠商)  (發佈的 Azure 資源庫影像  | 支援 | 只要 VM 在支援的作業系統上執行即支援。
協力廠商)  (發佈的自訂映射| 支援 | 只要 VM 在支援的作業系統上執行即支援。
使用 Site Recovery 的 Vm | 不支援 | 使用後端上的 Site Recovery，跨區域為 Vm 移動資源。 如果您已經在使用 Site Recovery，請停用複寫，然後開始準備程式。
RBAC 原則 | 不支援 | 在 Vm 上 (RBAC) 原則的角色型存取控制不會複製到目的地區域中的 VM。
延伸模組 | 不支援 | 延伸模組不會複製到目的地區域中的 VM。 在移動完成之後，手動安裝這些專案。


## <a name="supported-vm-storage-settings"></a>支援的 VM 儲存體設定

下表摘要說明 Azure VM OS 磁碟、資料磁碟和暫存磁碟的支援。 請務必注意 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/disk-scalability-targets) 和 [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets) VM 的 VM 磁碟限制和目標，以避免發生任何效能問題。

**元件** | **支援** | **詳細資料**
--- | --- | ---
OS 磁碟的大小上限 | 2048 GB | [深入了解](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) VM 磁碟。
暫存磁碟 | 不支援 | 暫存磁片一律會從準備程式中排除。<br/><br/> 請不要將任何永續性資料儲存於暫存磁碟上。 [深入了解](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#temporary-disk)。
資料磁碟的大小上限 | 8192 GB (若為受控磁碟)
資料磁碟的大小下限 |  2 GB (若為受控磁碟) |
資料磁碟的數目上限 | 最多 64 個 (根據特定的 Azure VM 大小支援) | [深入了解](https://docs.microsoft.com/azure/virtual-machines/windows/sizesd) VM 大小。
資料磁碟的變更率 | 進階儲存體的每個磁碟最多 10 MBps。 標準儲存體的每個磁碟最多 2 MBps。 | 如果磁片上的平均資料變更率持續高於最大值，則準備將無法趕上。<br/><br/>  但是，如果最大值是偶發性的，則準備可以趕上，但您可能會看到稍微延遲的復原點。
資料磁片 (標準儲存體帳戶)  | 不支援。 | 將儲存體類型變更為受控磁片，然後嘗試移動 VM。
資料磁片 (Premium 儲存體帳戶)  | 不支援 | 將儲存體類型變更為受控磁片，然後嘗試移動 VM。
受控磁片 (標準)  | 支援  |
受控磁片 (Premium)  | 支援 |
標準 SSD | 支援 |
第2代 (UEFI 開機) | 支援
開機診斷儲存體帳戶 | 不支援 | 將 VM 移至目的地區域之後，將它重新啟用。

### <a name="limits-and-data-change-rates"></a>限制與資料變更率

下表摘要說明以我們的測試為基礎的限制。 這些不會涵蓋所有可能的應用程式 i/o 組合。 實際的結果會根據您的應用程式 i/o 混合而有所不同。 有兩個要考慮的限制：每個磁片資料變換，以及每個 VM 資料變換。

**儲存目標** | **平均來源磁碟 I/O** |**平均來源磁碟資料變換** | **每日的來源磁碟資料變換總計**
---|---|---|---
標準儲存體 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 8 KB    | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 16 KB | 4 MB/秒 |    每個磁碟 336 GB
進階 P10 或 P15 磁碟 | 32 KB 或更大 | 8 MB/秒 | 每個磁碟 672 GB
進階 P20、P30、P40 或 P50 磁碟 | 8 KB    | 5 MB/秒 | 每個磁碟 421 GB
進階 P20、P30、P40 或 P50 磁碟 | 16 KB 或更大 |20 MB/秒 | 每個磁碟 1684 GB

## <a name="supported-vm-networking-settings"></a>支援的 VM 網路功能設定

**設定** | **支援** | **詳細資料**
--- | --- | ---
NIC | 支援 | 請在目的地區域中指定現有的資源，或在準備程式期間建立新的資源。 
內部負載平衡器 | 支援 | 請在目的地區域中指定現有的資源，或在準備程式期間建立新的資源。  
公用 Load Balancer | 目前不支援 | 請在目的地區域中指定現有的資源，或在準備程式期間建立新的資源。  
公用 IP 位址 | 支援 | 請在目的地區域中指定現有的資源，或在準備程式期間建立新的資源。  
網路安全性群組 | 支援 | 請在目的地區域中指定現有的資源，或在準備程式期間建立新的資源。  
保留 (靜態) IP 位址 | 支援 | 您目前無法設定此設定。 值預設為來源值。 <br/><br/> 如果來源 VM 上的 NIC 有靜態 IP 位址，且目標子網具有相同的可用 IP 位址，則會將它指派給目標 VM。<br/><br/> 如果目標子網沒有相同的可用 IP 位址，則 VM 的起始移動將會失敗。
動態 IP 位址 | 支援 | 您目前無法設定此設定。 值預設為來源值。<br/><br/> 如果來源上的 NIC 有動態 IP 位址，則目標 VM 上的 NIC 預設也是動態的。
IP 組態 | 支援 | 您目前無法設定此設定。 值預設為來源值。

## <a name="outbound-access-requirements"></a>輸出存取需求

您想要移動的 Azure Vm 需要輸出存取。


### <a name="url-access"></a>URL 存取

 如果您使用以 URL 為基礎的防火牆 Proxy 來控制輸出連線能力，請允許存取這些 URL：

**名稱** | **Azure 公用雲端** | **詳細資料** 
--- | --- | --- 
儲存體 | `*.blob.core.windows.net`  | 允許將資料從 VM 寫入來源區域的快取儲存體帳戶中。 
Azure Active Directory | `login.microsoftonline.com`  | 提供 Site Recovery 服務 URL 的授權和驗證。 
複寫 | `*.hypervrecoverymanager.windowsazure.com` | 允許 VM 與 Site Recovery 服務進行通訊。 
服務匯流排 | `*.servicebus.windows.net` | 允許 VM 寫入 Site Recovery 監視和診斷資料。 

## <a name="nsg-rules"></a>NSG 規則
如果您使用網路安全性群組 (NSG) 規則來控制輸出連線能力，請建立這些 [服務標記](../virtual-network/service-tags-overview.md) 規則。 每個規則都應該允許 HTTPS (443) 的輸出存取。
- 建立來源區域的儲存體標記規則。
- 建立 *AzureSiteRecovery* 標記規則，以允許存取任何區域中的 Site Recovery 服務。 此標記具有這些其他標記的相依性，因此您必須為其建立規則：
    - *AzureActiveDirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- 建議您在非生產環境中測試規則。 請[參閱一些範例](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)。 


## <a name="next-steps"></a>接下來的步驟

請嘗試使用資源移動器將 [AZURE VM 移](tutorial-move-region-virtual-machines.md) 至另一個區域。