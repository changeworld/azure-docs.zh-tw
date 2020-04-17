---
title: 支援 Azure 移轉中的 VMware 移轉
description: 瞭解在 Azure 遷移中對 VMware VM 遷移的支援。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: eee16b244ae4f9d517bdd42a0b7f37b1494ac480
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538132"
---
# <a name="support-matrix-for-vmware-migration"></a>VMware 移轉支援矩陣

本文總結了使用[Azure 遷移:伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool)遷移 VMware VM 的支持設置和限制。 如果要尋找有關評估 VMware VM 以移轉到 Azure 的資訊,請查看[評估矩陣](migrate-support-matrix-vmware.md)。


## <a name="migration-options"></a>移轉選項

您可以透過多種方式移至 VMware VM:

- 使用無代理遷移:遷移 VM 而無需在 VM 上安裝任何內容。 部署[Azure 遷移設備](migrate-appliance.md)以進行無代理遷移。
- 使用基於代理的遷移:在 VM 上安裝代理以進行複製。 對代理的移轉,您需要部署[複製裝置](migrate-replication-appliance.md)。

查看[本文](server-migrate-overview.md),瞭解要使用的方法。

## <a name="migration-limitations"></a>移轉限制

- 一次最多可以選擇 10 個 VM 進行複製。 如果要遷移更多計算機,則以 10 的組進行複製。
- 針對 VMware 無代理程式移轉，您最多可以同時執行 100 個複寫。

## <a name="agentless-vmware-servers"></a>沒有代理 VMware 伺服器

**VMware** | **詳細資料**
--- | ---
**VMware vCenter 伺服器** | 版本 5.5、6.0、6.5 或 6.7。
**VMware vSphere ESXI 主機** | 版本 5.5、6.0、6.5 或 6.7。
**vCenter 伺服器權限** | 沒有代理移至[移植裝置](migrate-appliance.md)。 裝置需要以下權限:<br/><br/> - **資料儲存.瀏覽**:允許瀏覽 VM 紀錄檔以排除快照創建和刪除問題。<br/><br/> - **資料儲存.低電平檔操作**:允許在資料儲存瀏覽器中讀取/寫入/刪除/重新命名操作,以排除快照創建和刪除問題。<br/><br/> - **虛擬機.設定.磁碟更改追蹤**:允許啟用或禁用 VM 磁碟的更改追蹤,以便在快照之間提取已更改的數據塊。<br/><br/> - **虛擬機.配置.磁碟租賃**:允許 VM 的磁碟租賃操作,使用 VMware vSphere 虛擬磁碟開發工具組 (VDDK) 讀取磁碟。<br/><br/> - **虛擬機.預配.允許DiskAccess:(** 特別是 vSphere 6.0 及以上)允許使用 VDDK 在 VM 上打開磁碟以進行隨機讀取訪問。<br/><br/> - **虛擬機.預配.允許ReadOnlyDisk存取**:允許在VM上打開磁碟,以便使用VDDK讀取磁碟。<br/><br/> - **虛擬機.預先設定.允許Disk隨機存取**:允許在VM上打開磁碟,以便使用VDDK讀取磁碟。<br/><br/> - **虛擬機.預配.允許虛擬電腦下載**:允許對與 VM 關聯的檔進行讀取操作,以便在發生故障時下載日誌並進行故障排除。<br/><br/> - [虛擬機.快照管理.]:允許創建和管理用於複製的 VM 快照。<br/><br/> - **虛擬機器.互動.關閉電源**:允許在遷移到 Azure 期間關閉 VM 的電源。



## <a name="agentless-vmware-vms"></a>沒有代理 VMware VM

**支援** | **詳細資料**
--- | ---
**支援的作業系統** | 可以使用無代理遷移遷移 Azure 支援的[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)作業系統。
**Azure 需要變更** | 有些 VM 可能需要變更，才能在 Azure 中執行。 Azure 移轉會自動為以下作業系統進行這些變更:<br/> - 紅帽企業 Linux 6.5+,7.0*<br/> - CentOS 6.5°, 7.0°</br> - SUSE Linux 企業伺服器 12 SP1*<br/> - 烏本圖 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8<br/><br/> 對於其他作業系統,您需要在遷移之前手動進行調整。 相關文章包含有關如何執行此操作的說明。
**Linux 啟動** | 如果 /boot 位於專用分區上,則應駐留在操作系統磁碟上,並且不應跨多個磁碟分佈。<br/> 如果 /boot 是根 (/) 分區的一部分,則『/』 分區應位於 OS 磁碟上,而不是跨其他磁碟。
**UEFI 啟動** | 遷移不支援具有 UEFI 啟動的 VM。
**磁碟大小** | 2 TB OS 磁碟;8 TB 的數據磁碟。
**磁碟限制** |  每個 VM 最多 60 個磁碟。
**加密磁碟/卷** | 遷移不支援具有加密磁碟/卷的 VM。
**共用磁碟叢集** | 不支援。
**獨立磁碟** | 不支援。
**RDM/直通磁碟** | 如果 VM 具有 RDM 或直通磁碟,則這些磁碟不會複製到 Azure。
**NFS** | 在 VM 上裝載的卷的 NFS 卷不會複製。
**iSCSI 目標** | 不支援具有 iSCSI 目標的 VM 進行無代理遷移。
**多路徑 IO** | 不支援。
**儲存 vMotion** | 不支援。 如果 VM 使用儲存 vMotion,則複製不起作用。
**已組 NIC** | 不支援。
**IPv6** | 不支援。
**目標磁碟** | VM 只能遷移到 Azure 中的託管磁碟(標準硬碟、進階 SSD)。
**同時複製** | 每個 vCenter 伺服器 100 個 VM。 如果更多,則分批遷移 100。


## <a name="agentless-azure-migrate-appliance"></a>無代理-Azure 移轉裝置 

沒有代理移至[Azure 移轉裝置](migrate-appliance.md)。 您可以使用 OVA 樣本、導入到 vCenter 伺服器或使用[PowerShell 文稿](deploy-appliance-script.md)將設備部署為 VMWare VM。

- 瞭解 VMware[的裝置要求](migrate-appliance.md#appliance---vmware)。
- 了解產品[在公共和政府雲](migrate-appliance.md#public-cloud-urls)中需要訪問的[government](migrate-appliance.md#government-cloud-urls)URL。
- 在 Azure 政府中,必須使用腳本部署設備。

## <a name="agentless-ports"></a>沒有代理埠

**裝置** | **Connection**
--- | ---
Appliance (設備) | 埠 443 上的出站連接,用於將複製的資料上載到 Azure,並與 Azure 遷移服務進行協調複製和遷移。
vCenter 伺服器 | 連接埠 443 上的入站連線,讓裝置協調複製- 建立快照、複製資料、發佈快照
vSphere/EXSI 主機 | 在 TCP 埠 902 上入站,用於設備從快照複製數據。


## <a name="agent-based-vmware-servers"></a>代理的 VMware 伺服器
下表總結了 VMware 虛擬化伺服器的評估支援和限制。

**VMware 要求** | **詳細資料**
--- | ---
**VMware vCenter 伺服器** | 版本 5.5、6.0、6.5 或 6.7。
**VMware vSphere ESXI 主機** | 版本 5.5、6.0、6.5 或 6.7。
**vCenter 伺服器權限** | vCenter 伺服器的唯讀帳戶。

## <a name="agent-based-vmware-vms"></a>基於代理的 VMware VM

該表總結了 VMware VM 對要使用基於代理的遷移遷移的 VMware VM 的支援。

**支援** | **詳細資料**
--- | ---
**機器工作負載** | Azure 遷移支援在受支援的電腦上運行的任何工作負荷(如活動目錄、SQL 伺服器等)的遷移。
**作業系統** | 有關最新資訊,請檢視作業系統對網站恢復[支援](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。 Azure 遷移提供相同的 VM 作業系統支援。
**Linux 檔案系統/來賓存儲** | 有關最新資訊,請查看[Linux 檔案系統](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)對網站恢復的支援。 Azure 遷移具有相同的 Linux 檔案系統支援。
**網路/儲存** | 有關最新[資訊,請查看](../site-recovery/vmware-physical-azure-support-matrix.md#network)站點恢復的網路和[存儲](../site-recovery/vmware-physical-azure-support-matrix.md#storage)先決條件。 Azure 遷移提供相同的網路/存儲要求。
**Azure 需求** | 有關最新資訊,請查看網站恢復的[Azure 網路](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[儲存](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)和[計算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)要求。 Azure 遷移對 VMware 遷移具有相同的要求。
**行動服務** | 移動服務代理必須安裝在要遷移的每個 VM 上。
**UEFI 啟動** | Azure 中移轉的 VM 將自動轉換為 BIOS 引導 VM。<br/><br/> 操作系統磁碟應最多具有四個分區,並且卷應使用 NTFS 進行格式化。
**目標磁碟** | VM 只能遷移到 Azure 中的託管磁碟(標準硬碟、進階 SSD)。
**磁碟大小** | 2 TB OS 磁碟;8 TB 的數據磁碟。
**磁碟限制** |  每個 VM 最多 63 個磁碟。
**加密磁碟/卷** | 遷移不支援具有加密磁碟/卷的 VM。
**共用磁碟叢集** | 不支援。
**獨立磁碟** | 支援。
**直通磁碟** | 支援。
**NFS** | 在 VM 上裝載的卷的 NFS 卷不會複製。
**iSCSI 目標** | 不支援具有 iSCSI 目標的 VM 進行無代理遷移。
**多路徑 IO** | 不支援。
**儲存 vMotion** | 支援
**已組 NIC** | 不支援。
**IPv6** | 不支援。




## <a name="agent-based-replication-appliance"></a>代理的複製裝置 

使用 Azure 遷移中心提供的 OVA 範本設定複製設備時,該設備將運行 Windows Server 2016 並符合支援要求。 如果在物理伺服器上手動設置複製設備,請確保它符合要求。

- 瞭解 VMware 的[複製裝置要求](migrate-replication-appliance.md#appliance-requirements)。
- 必須在設備上安裝 MySQL。 瞭解[安裝選項](migrate-replication-appliance.md#mysql-installation)。
- 瞭解複製設備需要[在公共](migrate-replication-appliance.md#url-access)[和政府雲](migrate-replication-appliance.md#azure-government-url-access)中訪問的 URL。
- 檢視裝置需要存取的[連接埠](migrate-replication-appliance.md#port-access)。

## <a name="agent-based-ports"></a>代理的連接埠

**裝置** | **Connection**
--- | ---
VM | 在 VM 上運行的行動服務與埠 HTTPS 443 入站上的本地複製設備(配置伺服器)進行通訊,以便進行複製管理。<br/><br/> VM 會透過輸入連接埠 HTTPS 9443 將複寫資料傳送至處理伺服器 (在設定伺服器電腦上執行)。 您可以修改此連接埠。
複製裝置 | 複製設備透過連接埠 HTTPS 443 出站協調使用 Azure 進行複製。
處理序伺服器 | 進程伺服器接收複製數據、優化和加密數據,然後通過埠 443 出站將其發送到 Azure 儲存。<br/> 預設情況下,進程伺服器在複製設備上運行。

## <a name="azure-vm-requirements"></a>Azure VM 需求

複製到 Azure 的所有本地 VM 必須滿足此表中總結的 Azure VM 要求。 當網站恢復運行複製的先決條件檢查時,如果某些要求未得到滿足,檢查將失敗。

**元件** | **需求** | **詳細資料**
--- | --- | ---
客體作業系統 | 驗證支援的 VMware VM 作業系統進行遷移。<br/> 您可以遷移在受支援的作業系統上運行的任何工作負載。 | 若不支援，則檢查會失敗。
客體作業系統架構 | 64 位元。 | 若不支援，則檢查會失敗。
作業系統磁碟大小 | 最多 2,048 GB。 | 若不支援，則檢查會失敗。
作業系統磁碟計數 | 1 | 若不支援，則檢查會失敗。
資料磁碟計數 | 64 或以下。 | 若不支援，則檢查會失敗。
資料磁碟大小 | 最多 4,095 GB | 若不支援，則檢查會失敗。
網路介面卡 | 支援多個介面卡。 |
共用 VHD | 不支援。 | 若不支援，則檢查會失敗。
FC 磁碟 | 不支援。 | 若不支援，則檢查會失敗。
BitLocker | 不支援。 | 為電腦啟用複寫之前必須先停用 BitLocker。
VM 名稱 | 從 1 到 63 個字元。<br/> 只能使用字母、數字和連字號。<br/><br/> 電腦名稱必須以字母或數字為開頭或結尾。 |  更新 Site Recovery 中電腦屬性的值。
移轉後連線 - Windows | 在移移後連接到執行 Windows 的 Azure VM,請執行:<br/> - 在移移在本地 VM 上啟用 RDP 之前。 確定已針對 [公用]**** 設定檔新增 TCP 和 UDP 規則，且在 [Windows 防火牆]**** > [允許的應用程式]**** 中已針對所有設定檔允許 RDP。<br/> 對於網站到網站 VPN 訪問,請在**Windows 防火牆** -> **允許的應用和功能**中啟用**Domain and Private**RDP。 此外,請檢查作業系統的 SAN 政策是否設定為**OnlineAll**。 [深入了解](prepare-for-migration.md)。 |
移轉後連接 -Linux | 要使用 SSH 在遷移後連接到 Azure VM,<br/> 在遷移之前,在本地電腦上,檢查安全外殼服務是否設置為"開始",並且防火牆規則是否允許 SSH 連接。<br/> 故障轉移後,在 Azure VM 上,允許傳入連接到 SSH 埠,用於透過 VM 故障的網路安全組規則以及連接到它的 Azure 子網。 此外,為 VM 添加公共 IP 位址。 |  


## <a name="next-steps"></a>後續步驟

[選擇](server-migrate-overview.md)VMware 遷移選項。
