---
title: Azure 網站恢復中的新增功能
description: 提供 Azure 網站恢復服務中的新功能和最新更新的摘要。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: abb1592bcacf025e9a052d7a9222f6fb3d2b72d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257429"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery 最新功能

[Azure Site Recovery](site-recovery-overview.md) 服務會持續更新並改進。 為了協助您掌握最新訊息，本文提供最新版本、新功能和新內容的相關資訊。 此頁面會定期更新。

您可以在[Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)通道中跟蹤和訂閱網站恢復更新通知。

## <a name="supported-updates"></a>支援的更新

對於網站恢復元件，我們支援 N-4 版本，其中 N 是最新版本。 下表將摘要說明這些報表：

**更新** |  **統一安裝** | **佈建服務器 ova** | **移動服務代理** | **站台復原提供者** | **復原服務代理程式**
--- | --- | --- | --- | --- | ---
[匯總 45](https://support.microsoft.com/help/4550047/) | 9.32.5487.1 | 5.1.5400.0 | 9.32.5487.1 | 5.1.5400.0 | 2.0.9165.0
[匯總 43](https://support.microsoft.com/help/4537047/) | 9.31.5449.1 | 5.1.5300.0 | 9.31.5449.1 | 5.1.5300.0 | 2.0.9165.0
[匯總 42](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[匯總 41](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[卷起來 40](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0

[瞭解有關](service-updates-how-to.md)更新安裝和支援的詳細資訊。

> [!NOTE]
> 更新彙總套件 44 未顯示在表中，因為它不包括網站恢復提供程式和代理的更新。

## <a name="updates-march-2020"></a>更新 （2020 年 3 月）

### <a name="update-rollup-45"></a>更新彙總套件 45

[更新彙總套件 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery)提供以下更新：

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 對網站修復代理和提供程式的更新，如匯總中詳細說明。
**問題修復/改進** | 匯總中詳述的一些修復和改進。

## <a name="updates-january-2020"></a>更新 （2020 年 1 月）

### <a name="update-rollup-44"></a>更新彙總套件 44

[更新彙總套件 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 網站恢復提供程式和代理沒有更新。
**問題修復/改進** | 匯總中詳述的一些修復和改進。

### <a name="azure-vmware-disaster-recovery"></a>Azure VMware 災害復原

Azure 虛擬機器現在支援 VM 啟用使用客戶管理的金鑰進行靜態加密。 [深入了解](azure-to-azure-how-to-enable-replication-cmk-disks.md)。


### <a name="update-rollup-43"></a>更新彙總套件 43

[更新彙總套件 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 網站修復代理和提供程式的更新（如匯總中詳細說明）
**問題修復/改進** | 一些修復和改進（如匯總中詳細）


## <a name="updates-november-2019"></a>更新 （2019 年 11 月）

### <a name="update-rollup-42"></a>更新彙總套件 42

[更新彙總套件 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 網站修復代理和提供程式的更新（如匯總中詳細說明）
**問題修復/改進** | 一些修復和改進（如匯總中詳細）


### <a name="azure-vm-disaster-recovery"></a>Azure VM 災害復原

下表匯總了 Azure VM 災害復原的新功能。

**特徵** | **詳細資料**
--- | ---
**UEFI** | 網站恢復現在支援 Azure VM 的災害復原，這些虛擬機器具有基於 UEFI 的啟動體系結構。
**Linux** | 網站恢復現在支援使用 Azure 磁片加密 （ADE） 運行 Linux 的 Azure VM。
**第 2 代** | 現在支援所有第 2 代 Azure VM 進行災害復原。
**地區** | 現在，您可以在挪威地理中為 Azure VM 啟用災害復原。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware 至 Azure 災害復原

下表匯總了 VMware 到 Azure 災害復原的新功能。

**特徵** | **詳細資料**
--- | ---
**UEFI** | 網站恢復現在支援 VMware VM 的災害復原，這些 VM 具有基於 UEFI 的啟動體系結構。<br/><br/> 支援的作業系統包括 Windows 伺服器 2019、 Windows 伺服器 2016、 Windows 伺服器 2012 R2、 Windows 伺服器 2012、 SLES 12 SP4、 RHEL 8。

## <a name="update-to-servicing-stack-updatesha-2"></a>更新到服務堆疊更新/SHA-2

對於將 Azure VM 恢復到次要區域或本地 VMware VM 或 Azure 的物理伺服器的災害復原，請注意以下事項：

- 從移動服務擴展版版本 9.30.5407.1（對於 Azure VM）和移動服務代理（對於 VMware/物理電腦），某些電腦作業系統必須運行服務堆疊更新和 SHA-2。 詳情見下表。
- 根據連結的 KB 安裝更新和 SHA-2。 從 2019 年 9 月起不支援 SHA-1，如果未啟用 SHA-2 代碼簽名，代理擴展將不會按預期安裝/升級。
- 瞭解有關[SHA-2 升級和要求](https://aka.ms/SHA-2KB)的更多。

**作業系統** | **Azure VM** | **VMware VM/物理機器**
--- | --- | ---
**視窗 2008 R2 SP1** | [服務堆疊更新](https://support.microsoft.com/help/4490628)<br/> [沙-2](https://support.microsoft.com/help/4474419)| [服務堆疊更新](https://support.microsoft.com/help/4490628)<br/> [沙-2](https://support.microsoft.com/help/4474419)
**視窗 2008 SP2** | [服務堆疊更新](https://support.microsoft.com/help/4493730)<br/> [沙-2](https://support.microsoft.com/help/4474419)| [服務堆疊更新](https://support.microsoft.com/help/4493730)<br/> [沙-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [服務堆疊更新](https://support.microsoft.com/help/4490628)<br/> [沙-2](https://support.microsoft.com/help/4474419)| [服務堆疊更新](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>更新 （2019 年 10 月）

### <a name="update-rollup-41"></a>更新彙總套件 41

[更新彙總套件 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 網站修復代理和提供程式的更新（如匯總中詳細說明）
**問題修復/改進** | 一些修復和改進（如匯總中詳細）



### <a name="azure-vm-disaster-recovery"></a>Azure VM 災害復原

下表匯總了 Azure VM 災害復原的新功能。

**特徵** | **詳細資料**
--- | ---
**測試容錯移轉設置** | 設置測試容錯移轉時，現在可以為測試容錯移轉 VM 和網路設定設置，包括 IP 位址、NSG、內部負載平衡以及每台電腦 NIC 的公共 IP 位址。 這些設置是可選的，不會更改當前行為。 如果不配置這些設置，則可以在測試容錯移轉時選擇 Azure VNet。 [深入了解](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/)。
**恢復計畫** | 恢復計畫現在限制為 100 個 VM，以確保容錯移轉的可靠性。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware 至 Azure 災害復原

下表匯總了 VMware 到 Azure 災害復原的新功能。

**特徵** | **詳細資料**
--- | ---
**恢復計畫** | 恢復計畫現在限制為 100 個 VM，以確保容錯移轉的可靠性。


## <a name="updates-september-2019"></a>更新 （2019 年 9 月）

### <a name="update-rollup-40"></a>更新彙總套件 40

[更新彙總套件 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 網站修復代理和提供程式的更新（如匯總中詳細說明）
**問題修復/改進** | 一些修復和改進（如匯總中詳細）




### <a name="azure-vm-disaster-recovery"></a>Azure VM 災害復原

下表匯總了 Azure VM 災害復原的新功能。

**特徵** | **詳細資料**
--- | ---
**故障恢復後清理** | 容錯移轉到輔助 Azure，然後故障返回主區域後，網站恢復會自動清理次要區域中的電腦。 無需手動刪除 VM 和 NIC。
**測試容錯移轉保留 IP 位址** | 現在，您可以在災害復原演練期間保留源 VM 的 IP 位址，並為測試容錯移轉選擇靜態 IP 位址。

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理伺服器災害復原

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
新的進程伺服器警報 | 我們添加了新的進程伺服器警報。 [深入了解](vmware-physical-azure-monitor-process-server.md)。

### <a name="hyper-v-disaster-recovery"></a>超 V 災害復原

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
儲存體帳戶 | 網站恢復現在支援使用啟用防火牆的存儲帳戶，以便 Hyper-V 到 Azure 災害復原。  您可以選擇啟用防火牆的存儲帳戶作為目標帳戶，也可以選擇用於緩存存儲。 如果使用啟用防火牆的帳戶，請確保啟用允許受信任 Microsoft 服務的選項。<br/><br/> 對於帶或不帶系統中心 VMM 的超 V VM，都支援此功能。


## <a name="updates-august-2019"></a>更新 （2019 年 8 月）

### <a name="update-rollup-39"></a>更新彙總套件 39

[更新彙總套件 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 網站修復代理和提供程式的更新（如匯總中詳細說明）
**問題修復/改進** | 一些修復和改進（如匯總中詳細）


### <a name="azure-vm-disaster-recovery"></a>Azure VM 災害復原

下表匯總了 Azure VM 災害復原的新功能。

**特徵** | **詳細資料**
--- | ---
**沒有 Azure AD 的加密** | 現在，Azure VM 複製到運行 Windows 的託管磁片時，不支援沒有 Azure AD 應用的加密。
**容錯移轉的網路資源** | 當容錯移轉到其他區域時，現在可以將網路資源設置（NSG、負載平衡、公共 IP 位址）附加到 VM。

## <a name="updates-july-2019"></a>更新 （2019 年 7 月）

### <a name="update-rollup-38"></a>更新彙總套件 38

[更新彙總套件 38](https://support.microsoft.com/help/4513507/)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 網站修復代理和提供程式的更新（如匯總中詳細說明）
**問題修復/改進** | 一些修復和改進（如匯總中詳細）


### <a name="general"></a>一般

網站恢復現在支援使用通用 v2 存儲帳戶進行緩存存儲或目標存儲。 以前僅支援 v1。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware 至 Azure 災害復原

現在，使用託管磁碟複製到 Azure VM 時，可以複製高達 8 TB 的磁片。


## <a name="updates-june-2019"></a>更新 （2019 年 6 月）

### <a name="update-rollup-37"></a>更新彙總套件 37

[更新彙總套件 37](https://support.microsoft.com/help/4508614/)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 網站修復代理和提供程式的更新（如匯總中詳細說明）
**問題修復/改進** | 一些修復和改進（如匯總中詳細）


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理伺服器災害復原

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**GPT 分區** | 從更新彙總套件 37 開始（移動服務版本 9.25.5241.1），UEFI 中最多支援五個 GPT 分區。 在此更新之前，有四個受支援。



## <a name="updates-may-2019"></a>更新 （2019 年 5 月）

### <a name="update-rollup-36"></a>更新彙總套件 36

[更新彙總套件 36](https://support.microsoft.com/help/4503156)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 對網站修復代理和提供程式的更新（如匯總中詳細說明）
**問題修復/改進** | 一些修復和改進（如匯總中詳細）

### <a name="azure-vm-disaster-recovery"></a>Azure VM 災害復原

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**複製添加的磁片** | 為已啟用災害復原的 Azure VM 中添加的資料磁片啟用複製。 [深入了解](azure-to-azure-enable-replication-added-disk.md)。
**自動更新** | 為啟用災害復原的 Azure VM 上運行的移動服務擴展配置自動更新時，現在可以選擇要使用的現有自動化帳戶，而不是使用網站恢復創建的預設帳戶。 [深入了解](azure-to-azure-autoupdate.md)。


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理伺服器災害復原

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**進程伺服器監視** | 對於本地 VMware VM 和物理伺服器的災害復原，通過改進的伺服器運行狀況報告和警報來監視和排除進程伺服器問題。 [深入了解](vmware-physical-azure-monitor-process-server.md)。





## <a name="updates-march-2019"></a>更新 （2019 年 3 月）

### <a name="update-rollup-35"></a>更新彙總套件 35

[更新彙總套件 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 對網站修復代理和提供程式的更新（如匯總中詳細說明）
**問題修復/改進** | 一些修復和改進（如匯總中詳細）

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理伺服器災害復原

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**託管磁片** | 本地 VMware VM 和物理伺服器的複製現在直接複製到 Azure 中的託管磁片。 本地資料將發送到 Azure 中的緩存存儲帳戶，並在目標位置的託管磁片中創建復原點。 這可確保您無需管理多個目標存儲帳戶。
**佈建服務器** | 網站恢復現在支援具有多個 NIC 的佈建服務器。 在保存庫中註冊佈建服務器之前，向佈建服務器 VM 添加其他配接器。 如果之後添加，則需要在保存庫中重新註冊伺服器。


## <a name="updates-february-2019"></a>更新 （2019 年 2 月）

### <a name="update-rollup-34"></a>更新彙總套件 34

[更新彙總套件 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 對網站修復代理和提供程式的更新（如匯總中詳細說明）。
**問題修復/改進** | 許多修復和改進（如匯總中詳細）。


### <a name="update-rollup-33"></a>更新彙總套件 33

[更新彙總套件 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 對網站修復代理和提供程式的更新（如匯總中詳細說明）。
**問題修復/改進** | 許多修復和改進（如匯總中詳細）。


### <a name="azure-vm-disaster-recovery"></a>Azure VM 災害復原

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**網路對應** | 對於 Azure VM 災害復原，現在可以在啟用複製時使用任何可用的目標網路。
**標準 SSD** | 現在可以使用[標準 SSD 磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)為 Azure VM 設置災害復原。
**儲存空間直接存取** | 您可以使用[存儲空間直接](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)為在 Azure VM 應用上運行的應用設置災害復原，以獲得高可用性。  將存儲空間直接 （S2D） 與網站恢復一起使用可全面保護 Azure VM 工作負荷。 S2D 允許您在 Azure 中託管來賓群集。 當 VM 承載關鍵應用程式（如 SAP ASCS 層、SQL Server 或橫向擴展檔案伺服器）時，這尤其有用。


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理伺服器災害復原

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**Linux BRTFS 檔案系統** | 網站恢復現在支援使用 BRTFS 檔案系統複製 VMware VM。 如果：<br/><br/>- 啟用複製後更改 BTRFS 檔案系統子卷。<br/><br/>- 檔案系統分佈在多個磁片上。<br/><br/>- BTRFS 檔案系統支援 RAID。
**Windows Server 2019** | 為運行 Windows 伺服器 2019 的電腦添加支援。


## <a name="updates-january-2019"></a>更新 （2019 年 1 月）


### <a name="accelerated-networking-azure-vms"></a>加速網路（Azure VM）

加速網路使單個根 I/O 虛擬化 （SR-IOV） 連接到 VM，從而提高了網路性能。 當您為 Azure VM 啟用複寫功能時，Site Recovery 會偵測是否已啟用加速網路。 如果是，則在容錯移轉之後，Site Recovery 將會自動對 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) 和 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms) 的目標複本 Azure VM 設定加速網路。

[深入了解](azure-vm-disaster-recovery-with-accelerated-networking.md)。

### <a name="update-rollup-32"></a>更新彙總套件 32

[更新彙總套件 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 對網站修復代理和提供程式的更新（如匯總中詳細說明）。
**問題修復/改進** | 許多修復和改進（如匯總中詳細）。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 災害復原

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**Linux 支援** | 為 RedHat 工作站 6/7 添加了支援，並為 Ubuntu、Debian 和 SUSE 添加了新的內核版本。
**儲存空間直接存取** | 網站恢復支援使用存儲空間直接 （S2D） 的 Azure VM。

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware VM/物理伺服器災害復原

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**Linux 支援** | 添加了對 Redhat 企業 Linux 7.6、RedHat 工作站 6/7、Oracle Linux 6.10 和 Oracle Linux 7.6 以及 Ubuntu、Debian 和 SUSE 的新內核版本的支援。


### <a name="update-rollup-31"></a>更新彙總套件 31

[更新彙總套件 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 對網站修復代理和提供程式的更新（如匯總中詳細說明）。
**問題修復/改進** | 許多修復和改進（如匯總中詳細）。

### <a name="vmware-vmsphysical-servers-replication"></a>VMware VM/物理伺服器複製

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**Linux 支援** |  添加了對甲骨文Linux 6.8、甲骨文Linux 6.9和甲骨文Linux 7.0的支援，以及紅帽相容內核，以及不可破壞的企業內核 （UEK） 版本 5。
**LVM** | 添加了對 LVM 和 LVM2 卷的支援。<br/><br/> 現在支援磁碟分割和 LVM 卷上的 /boot 目錄。
**目錄** | 添加了對這些設置為單獨的分區的目錄或不在同一系統磁片上的檔案系統的支援：<br/><br/> /（根），/引導，/usr，/usr/本地，/var，/等。
**視窗伺服器 2008** | 添加了對動態磁碟的支援。
**故障** | 改進的 VMware VM 容錯移轉時間，其中 Storvsc 和 vsbus 不是引導驅動程式。
**UEFI 支援** | Azure VM 不支援啟動類型 UEFI。 現在，您可以使用 UEFI 將本地物理伺服器遷移到 Azure 網站恢復。 網站恢復通過在遷移前將引導類型轉換為 BIOS 來遷移伺服器。 以前僅支援 VM 的網站恢復此轉換。 支援適用于運行 Windows Server 2012 或更高版本的物理伺服器。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 災害復原

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**Linux 支援** | 添加了對甲骨文Linux 6.8、甲骨文Linux 6.9和甲骨文Linux 7.0的支援，以及紅帽相容內核，以及不可破壞的企業內核 （UEK） 版本 5。
**Linux BRTFS 檔案系統** | 支援 Azure VM。
**可用性區域中的 Azure VM** | 可以為部署在可用性區域中的 Azure VM 將複製複製到另一個區域。 現在，您可以為 Azure VM 啟用複寫功能，並將容錯移轉的目標設為單一 VM 執行個體、可用性設定組中的 VM，或可用性區域中的 VM。 此設定不會影響複寫。 [閱讀](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)公告。
**啟用防火牆的存儲（門戶/電源外殼）** | 為[啟用防火牆的存儲帳戶](https://docs.microsoft.com/azure/storage/common/storage-network-security)添加了支援。<br/><br/> 您可以將啟用防火牆的存儲帳戶上具有非託管磁片的 Azure VM 複製到另一個 Azure 區域以進行災害復原。<br/><br/> 您可以將啟用防火牆的存儲帳戶用作非託管磁片的目標存儲帳戶。<br/><br/> 在門戶中支援並使用 PowerShell。

## <a name="updates-december-2018"></a>更新 （2018 年 12 月）

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>移動服務（Azure VM）的自動更新

Site Recovery 新增了行動服務擴充功能的自動更新選項。 行動服務擴充功能會安裝在 Site Recovery 所複寫的每個 Azure VM 上。 當您啟用複寫時，您會選取是否允許 Site Recovery 管理此擴充功能的更新。

更新不需要重新啟動 VM，也不會影響複寫。 [深入了解](azure-to-azure-autoupdate.md)。

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM 災害復原的定價計算機

Azure VM 的災害復原會產生 VM 許可成本以及網路和存儲成本。 Azure 提供[定價計算機](https://aka.ms/a2a-cost-estimator)來協助您計算這些成本。 Site Recovery 現在提供[範例定價預估](https://aka.ms/a2a-cost-estimator)功能，可根據三層式應用程式計算範例部署的價格；該應用程式使用 6 個 VM，其中包含 12 個標準 HDD 磁碟和六個進階 SSD 磁碟。

- 該示例假定標準資料每天更改 10 GB，高級資料更改 20 GB。
- 您可以針對本身的特定部署，變更估計成本的變數。
- 您可以指定 VM 的數目、受控磁碟的數目和類型，以及跨 VM 的預期總資料變更率。
- 此外，您也可以套用壓縮因數來估計頻寬成本。

[閱讀](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)公告。


## <a name="updates-october-2018"></a>更新 （2018 年 10 月）

### <a name="update-rollup-30"></a>更新彙總套件 30

[更新彙總套件 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 對網站修復代理和提供程式的更新（如匯總中詳細說明）。
**問題修復/改進** | 許多修復和改進（如匯總中詳細）。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 災害復原
本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**區域支援** | 為澳大利亞中部 1 和澳大利亞中央 2 添加了網站恢復支援。
**支援磁片加密** | 添加了使用 Azure AD 應用加密 Azure 磁片加密 （ADE） 的 Azure VM 的災害復原支援。 [深入了解](azure-to-azure-how-to-enable-replication-ade-vms.md)。
**磁片排除** | 未初始化的磁片現在在 Azure VM 複製期間自動排除。
**啟用防火牆的存儲（電源外殼）** | 為[啟用防火牆的存儲帳戶](https://docs.microsoft.com/azure/storage/common/storage-network-security)添加了支援。<br/><br/> 您可以將啟用防火牆的存儲帳戶上具有非託管磁片的 Azure VM 複製到另一個 Azure 區域以進行災害復原。<br/><br/> 您可以將啟用防火牆的存儲帳戶用作非託管磁片的目標存儲帳戶。<br/><br/> 僅限使用 PowerShell 支援。


### <a name="update-rollup-29"></a>更新彙總套件 29

[更新彙總套件 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 對網站修復代理和提供程式的更新（如匯總中詳細說明）。
**問題修復/改進** | 許多修復和改進（如匯總中詳細）。


## <a name="updates-august-2018"></a>更新 （2018 年 8 月）

### <a name="update-rollup-28"></a>更新彙總套件 28

[更新彙總套件 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 對網站修復代理和提供程式的更新（如匯總中詳細說明）。
**問題修復/改進** | 許多修復和改進（如匯總中詳細）。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 災害復原
本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**Linux 支援** | 為 RedHat 企業 Linux 6.10 添加了支援;CentOS 6.10.<br/><br/>
**雲支援** | 支援德國雲中 Azure VM 的災害復原。
**跨訂閱災害復原** | 支援將一個區域中的 Azure VM 複製到同一 Azure 活動目錄租戶中不同訂閱中的另一個區域。 [深入了解](https://aka.ms/cross-sub-blog)。

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/物理伺服器災害復原
本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**Linux 支援** | 為 RedHat 企業 Linux 6.10、CentOS 6.10 添加了支援。<br/><br/> 現在支援在舊 BIOS 相容性模式下使用 GUID 分區表 （GPT） 分區樣式的基於 Linux 的 VM。 有關詳細資訊，請查看[Azure VM 常見問題解答](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks)。
**遷移後 VM 的災害復原** | 支援為遷移到 Azure 的本地 VMware VM 啟用次要區域的災害復原，而無需在啟用複製之前在 VM 上卸載移動服務。
**視窗伺服器 2008** | 支援運行 Windows Server 2008 R2/2008 64 位和 32 位的遷移電腦。<br/><br/> 僅遷移（複製和容錯移轉）。 不支援故障倒退。

## <a name="updates-july-2018"></a>更新 （2018 年 7 月）

### <a name="update-rollup-27-july-2018"></a>更新彙總套件 27 （2018 年 7 月）

[更新彙總套件 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)提供以下更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | 對網站修復代理和提供程式的更新（如匯總中詳細說明）。
**問題修復/改進** | 許多修復和改進（如匯總中詳細）。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 災害復原

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**Linux 支援** | 為紅帽企業 Linux 7.5 添加了支援。

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/物理伺服器災害復原

本月添加的功能匯總在表中。

**特徵** | **詳細資料**
--- | ---
**Linux 支援** | 支援紅帽企業Linux 7.5，SUSE Linux企業伺服器12。



## <a name="next-steps"></a>後續步驟

透過 [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)頁面掌握相關更新的最新訊息。
