---
title: Azure Migrate 設備
description: 提供伺服器評估和遷移中所使用的 Azure Migrate 設備的總覽。
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 652fe9d379d6e2ba50e9e282f384905e154368d8
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031658"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 設備

本文說明 Azure Migrate 設備。 當您使用[Azure Migrate： Server 評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具來探索及評估應用程式、基礎結構和工作負載，以 Microsoft Azure 進行遷移時，您會部署設備。 當您使用[Azure Migrate： Server 評定](migrate-services-overview.md#azure-migrate-server-migration-tool)搭配[無代理程式遷移](server-migrate-overview.md)，將 VMware vm 遷移至 Azure 時，也會使用設備。

## <a name="appliance-overview"></a>設備總覽

在下列案例中會使用 Azure Migrate 設備。

**案例** | **工具** | **用於** 
--- | --- | ---
VMware VM | Azure Migrate：伺服器評估<br/><br/> Azure Migrate：伺服器遷移 | 探索 VMware Vm<br/><br/> 探索機器應用程式和相依性<br/><br/> 收集機器中繼資料和效能中繼資料以進行評量。<br/><br/> 使用無代理程式遷移來複寫 VMware Vm。
Hyper-V VM | Azure Migrate：伺服器評估 | 探索 Hyper-v Vm<br/><br/> 收集機器中繼資料和效能中繼資料以進行評量。
實體機器 |  Azure Migrate：伺服器評估 |  探索實體伺服器<br/><br/> 收集機器中繼資料和效能中繼資料以進行評量。

## <a name="appliance---vmware"></a>設備-VMware 

**需求** | **VMware** 
--- | ---
**下載格式** | .OVA 
**下載連結** | https://aka.ms/migrate/appliance/vmware 
**下載大小** | 11.2 GB
**授權** | 下載的應用裝置範本隨附 Windows Server 2016 評估授權，其有效期為180天。 如果評估期接近到期日，建議您下載並部署新的應用裝置，或啟用設備 VM 的作業系統授權。
**部署** | 您會將設備部署為 VMware VM。 您在 vCenter Server 上需要足夠的資源來配置具有 32 GB RAM 的 VM、8個 vcpu、大約 80 GB 的磁片儲存體，以及外部虛擬交換器。<br/> 設備必須直接或透過 proxy 存取網際網路。<br/> 設備可以連接到單一 vCenter Server。
**硬體** | VCenter 上用來配置具有 32-GB RAM 8 個 vcpu 的 VM 的資源，大約 80 GB 的磁片儲存體，以及外部虛擬交換器。 
**雜湊值** | MD5： c06ac2a2c0f870d3b274a0b7a73b78b1<br/><br/> SHA256：4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c
**vCenter server/主機** | 設備 VM 必須部署在執行5.5 版或更新版本的 ESXi 主機上。<br/><br/> vCenter Server 執行5.5、6.0、6.5 或6.7。
**Azure Migrate 專案** | 應用裝置可以與單一專案相關聯。 <br/> 任何數目的設備都可以與單一專案相關聯。<br/> 
**探索** | 設備可以在 vCenter Server 上探索最多10000個 VMware Vm。<br/> 設備可以連接到單一 vCenter Server。
**設備元件** | 管理應用程式：應用裝置中的 Web 應用程式，可在部署期間進行使用者輸入。<br/> 探索代理程式：收集電腦設定資料。<br/> 評量代理程式：收集效能資料。<br/> DRA：協調 VM 複寫，並協調電腦/Azure 之間的通訊。<br/> 閘道：將複寫的資料傳送至 Azure。<br/> 自動更新服務：更新元件（每隔24小時執行一次）。
**VDDK （無代理程式遷移）** | 如果您是使用 Azure Migrate 伺服器遷移來執行無代理程式遷移，則必須在應用裝置 VM 上安裝 VMware vSphere VDDK。


## <a name="appliance---hyper-v"></a>設備-Hyper-v

**需求** | **Hyper-V** 
--- | ---
**下載格式** | Zip 資料夾（含 VHD）
**下載連結** | https://aka.ms/migrate/appliance/hyperv 
**下載大小** | 10 GB
**授權** | 下載的應用裝置範本隨附 Windows Server 2016 評估授權，其有效期為180天。 如果評估期接近到期日，建議您下載並部署新的應用裝置，或啟用設備 VM 的作業系統授權。
**設備部署**   |  您會將設備部署為 Hyper-v VM。<br/> Azure Migrate 提供的設備 VM 是 Hyper-v VM 5.0 版。<br/> Hyper-v 主機必須執行 Windows Server 2012 R2 或更新版本。<br/> 主機需要足夠的空間來配置 16 GB RAM、8個 vcpu、大約 80 GB 的儲存空間，以及適用于設備 VM 的外部交換器。<br/> 設備需要靜態或動態 IP 位址，以及網際網路存取。
**硬體** | Hyper-v 主機上的資源可配置 16 GB RAM、8個 vcpu、大約 80 GB 的儲存空間，以及適用于設備 VM 的外部交換器。
**雜湊值** | MD5：29a7531f32bcf69f32d964fa5ae950bc<br/><br/> SHA256：37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31
**Hyper-V 主機** | 正在執行 Windows Server 2012 R2 或更新版本。
**Azure Migrate 專案** | 應用裝置可以與單一專案相關聯。 <br/> 任何數目的設備都可以與單一專案相關聯。<br/> 
**探索** | 設備可以在 vCenter Server 上探索最多5000個 VMware Vm。<br/> 設備最多可以連線到300的 Hyper-v 主機。
**設備元件** | 管理應用程式：應用裝置中的 Web 應用程式，可在部署期間進行使用者輸入。<br/> 探索代理程式：收集電腦設定資料。<br/> 評量代理程式：收集效能資料。<br/>  自動更新服務：更新元件（每隔24小時執行一次）。


## <a name="appliance---physical"></a>設備-實體

**需求** | **實體** 
--- | ---
**下載格式** | Zip 資料夾（使用 PowerShell 型安裝程式腳本）
**下載連結** | [下載連結](https://go.microsoft.com/fwlink/?linkid=2105112)
**下載大小** | 59.7 MB
**硬體** | 專用實體機器，或使用虛擬機器。 執行設備的電腦需要 16 GB RAM、8個 vcpu，大約 80 GB 的儲存空間，以及外部交換器。<br/> 設備需要靜態或動態 IP 位址，以及網際網路存取。
**雜湊值** | MD5：1e92ede3e87c03bd148e56a708cdd33f<br/><br/> SHA256： a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c
**作業系統** | 設備機器應執行 Windows Server 2016。 
**設備部署**   |  設備安裝程式腳本會從入口網站下載（在壓縮的資料夾中）。 <br/> 您會將資料夾解壓縮，並執行 PowerShell 腳本（AzureMigrateInstaller）。
**探索** | 設備可以探索最多250部實體伺服器。
**設備元件** | 管理應用程式：應用裝置中的 Web 應用程式，可在部署期間進行使用者輸入。<br/> 探索代理程式：收集電腦設定資料。<br/> 評量代理程式：收集效能資料。<br/>  自動更新服務：更新元件（每隔24小時執行一次）。


## <a name="url-access"></a>URL 存取

Azure Migrate 設備需要網際網路的連線能力。

- 當您部署設備時，Azure Migrate 會對下表中摘要說明的 Url 進行連線檢查。
- 如果您使用以 URL 為基礎的 proxy 來連線到網際網路，請允許存取這些 Url，確保 proxy 會解析查詢 Url 時所收到的任何 CNAME 記錄。

**URL** | **詳細資料**  
--- | --- |
*.portal.azure.com  | 瀏覽至 Azure 入口網站。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | 登入您的 Azure 訂用帳戶。
\* microsoftonline.com <br/> *.microsoftonline-p.com | 建立設備 Active Directory 應用程式，以與 Azure Migrate 通訊。
management.azure.com | 建立設備 Active Directory 應用程式，以與 Azure Migrate 服務進行通訊。
dc.services.visualstudio.com | 上傳用於內部監視的應用程式記錄。
*.vault.azure.net | 管理 Azure Key Vault 中的秘密。
aka.ms/* | 允許存取稱為的連結。 用於 Azure Migrate 設備更新。
download.microsoft.com/download | 允許從 Microsoft 下載下載。
*.servicebus.windows.net | 設備與 Azure Migrate 服務之間的通訊。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | 連接到 Azure Migrate 服務 Url。
*.hypervrecoverymanager.windowsazure.com | **用於 VMware 無代理程式遷移**<br/><br/> 連接到 Azure Migrate 服務 Url。
*.blob.core.windows.net |  **用於 VMware 無代理程式遷移**<br/><br/>將資料上傳至儲存體以進行遷移。




## <a name="collected-data---vmware"></a>收集的資料-VMware

### <a name="collected-performance-data-vmware"></a>收集的效能資料-VMware

以下是設備收集並傳送至 Azure 的 VMware VM 效能資料。

**Data** | **計數器** | **評量影響**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 建議的 VM 大小/成本
記憶體使用率 | mem.usage.average | 建議的 VM 大小/成本
磁片讀取輸送量（每秒 MB） | virtualDisk.read.average | 磁片大小、儲存體成本、VM 大小的計算
磁片寫入輸送量（每秒 MB） | virtualDisk.write.average | 磁片大小、儲存體成本、VM 大小的計算
每秒的磁片讀取作業數 | virtualDisk.numberReadAveraged.average | 磁片大小、儲存體成本、VM 大小的計算
每秒的磁片寫入作業數 | virtualDisk.numberReadAveraged.average  | 磁片大小、儲存體成本、VM 大小的計算
NIC 讀取輸送量（每秒 MB） | net.received.average | VM 大小的計算
NIC 寫入輸送量（每秒 MB） | net.transmitted.average  |VM 大小的計算


### <a name="collected-metadata-vmware"></a>收集的中繼資料-VMware

> [!NOTE]
> Azure Migrate 設備所探索到的中繼資料，可協助您在將應用程式遷移至 Azure、執行 Azure 適用性分析、應用程式相依性分析和成本規劃時，適當地調整其大小。 Microsoft 不會使用此資料與任何授權合規性審查相關。

以下是設備收集並傳送至 Azure 的 VMware VM 中繼資料完整清單。

**Data** | **計數器**
--- | --- 
**機器詳細資料** | 
VM 識別碼 | vm.Config.InstanceUuid 
VM 名稱 | vm.Config.Name
vCenter Server 識別碼 | VMwareClient.Instance.Uuid
VM 描述 | vm.Summary.Config.Annotation
授權產品名稱 | vm.Client.ServiceContent.About.LicenseProductName
作業系統類型 | vm.SummaryConfig.GuestFullName
開機類型 | vm.Config.Firmware
核心數 | vm.Config.Hardware.NumCPU
記憶體 (MB) | vm.Config.Hardware.MemoryMB
磁碟數目 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
磁碟大小清單 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
網路介面卡清單 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU 使用率 | cpu.usage.average
記憶體使用率 |mem.usage.average
**每個磁片詳細資料** | 
磁碟機碼值 | disk.Key
Dikunit 號碼 | disk.UnitNumber
磁碟控制器機碼值 | disk.ControllerKey.Value
已佈建的 GB 數 | virtualDisk.DeviceInfo.Summary
磁碟名稱 | 使用磁片產生的值。UnitNumber，磁片。機碼，磁片。ControllerKey。值
每秒的讀取作業數 | virtualDisk.numberReadAveraged.average
每秒的寫入作業數 | virtualDisk.numberReadAveraged.average
讀取輸送量（每秒 MB） | virtualDisk.read.average
寫入輸送量（每秒 MB） | virtualDisk.write.average
**每個 NIC 詳細資料** | 
網路介面卡名稱 | nic.Key
MAC 位址 | ((VirtualEthernetCard)nic).MacAddress
IPv4 位址 | vm.Guest.Net
IPv6 位址 | vm.Guest.Net
讀取輸送量（每秒 MB） | net.received.average
寫入輸送量（每秒 MB） | net.transmitted.average
**清查路徑詳細資料** | 
名稱 | container.GetType().Name
子物件的類型 | container.ChildType
參考詳細資料 | container.MoRef
父系詳細資料 | Container.Parent
每個 VM 的資料夾詳細資料 | ((Folder)container).ChildEntity.Type
每個 VM 的資料中心詳細資料 | ((Datacenter)container).VmFolder
每個主機資料夾的資料中心詳細資料 | ((Datacenter)container).HostFolder
每一主機的叢集詳細資料 | （（ClusterComputeResource）容器）。設立
每個 VM 的主機詳細資料 | （（HostSystem）容器）。機內

## <a name="collected-data---hyper-v"></a>收集的資料-Hyper-v

### <a name="collected-performance-data-hyper-v"></a>收集的效能資料-Hyper-v

> [!NOTE]
> Azure Migrate 設備所探索到的中繼資料，可協助您在將應用程式遷移至 Azure、執行 Azure 適用性分析、應用程式相依性分析和成本規劃時，適當地調整其大小。 Microsoft 不會使用此資料與任何授權合規性審查相關。

以下是設備收集並傳送至 Azure 的超級 VM 效能資料。

**效能計數器類別** | **計數器** | **評量影響**
--- | --- | ---
Hyper-v 虛擬處理器 | % Guest 執行時間 | 建議的 VM 大小/成本
Hyper-v 動態記憶體 VM | 目前壓力（%）<br/> 來賓可見實體記憶體（MB） | 建議的 VM 大小/成本
Hyper-v 虛擬存放裝置 | 讀取位元組/秒 | 磁片大小、儲存體成本、VM 大小的計算
Hyper-v 虛擬存放裝置 | 寫入位元組數/秒 | 磁片大小、儲存體成本、VM 大小的計算
Hyper-V 虛擬網路介面卡 | 接收的位元組數/秒 | VM 大小的計算
Hyper-V 虛擬網路介面卡 | 傳送的位元組數/秒 | VM 大小的計算

- CPU 使用率是所有連接至 VM 的虛擬處理器的所有使用量總和。
- 記憶體使用率為（目前的壓力 * 來賓可見的實體記憶體）/100。
- 磁片和網路使用值是從列出的 Hyper-v 效能計數器收集而來。

### <a name="collected-metadata-hyper-v"></a>收集的中繼資料-Hyper-v

以下是設備收集並傳送至 Azure 的 Hyper-v VM 中繼資料完整清單。

**Data** | **WMI 類別** | **WMI 類別屬性**
--- | --- | ---
**機器詳細資料** | 
BIOS _ Msvm_BIOSElement 的序號 | BIOSSerialNumber
VM 類型（Gen 1 或2） | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM 顯示名稱 | Msvm_VirtualSystemSettingData | ElementName
VM 版本 | Msvm_ProcessorSettingData | VirtualQuantity
記憶體（位元組） | Msvm_MemorySettingData | VirtualQuantity
VM 可以使用的最大記憶體 | Msvm_MemorySettingData | 限制
已啟用動態記憶體 | Msvm_MemorySettingData | DynamicMemoryEnabled
作業系統名稱/版本/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems 名稱資料
VM 電源狀態 | Msvm_ComputerSystem | EnabledState
**每個磁片詳細資料** | 
磁片識別碼 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
虛擬硬碟類型 | Msvm_VirtualHardDiskSettingData | 類型
虛擬硬碟大小 | Msvm_VirtualHardDiskSettingData | MaxInternalSize
虛擬硬碟父系 | Msvm_VirtualHardDiskSettingData | ParentPath
**每個 NIC 詳細資料** | 
IP 位址（綜合 Nic） | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP 已啟用（綜合 Nic） | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC 識別碼（綜合 Nic） | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 位址（綜合 Nic） | Msvm_SyntheticEthernetPortSettingData | 地址
NIC 識別碼（舊版 Nic） | MsvmEmulatedEthernetPortSetting 資料 | InstanceID
NIC MAC 識別碼（舊版 Nic） | MsvmEmulatedEthernetPortSetting 資料 | 地址




## <a name="discovery-and-collection-process"></a>探索和收集程式

設備會使用下列程式，與 vCenter Server 和 Hyper-v 主機/叢集通訊。

1. **開始探索**：
    - 當您在 Hyper-v 應用裝置上啟動探索時，它會與 WinRM 埠5985（HTTP）和5986（HTTPS）上的 Hyper-v 主機通訊。
    - 當您在 VMware 設備上啟動探索時，它預設會與 TCP 埠443上的 vCenter server 通訊。 如果 vCenter server 在不同的埠上接聽，您可以在應用裝置 web 應用程式中加以設定。
2. **收集中繼資料和效能資料**：
    - 設備會使用通用訊息模型（CIM）會話，從埠5985和5986上的 Hyper-v 主機收集 Hyper-v VM 資料。
    - 設備預設會與埠443通訊，以從 vCenter Server 收集 VMware VM 資料。
3. **傳送資料**：設備會將收集到的資料傳送至 Azure Migrate Server 評估，並透過 SSL 埠 443 Azure Migrate 伺服器遷移。 設備可以透過網際網路連線到 Azure，或您可以使用 ExpressRoute 搭配公用/Microsoft 對等互連。
    - 針對效能資料，應用裝置會收集即時使用量資料。
        - VMware 每隔20秒會收集一次效能資料，針對每個效能計量，每隔30秒為 Hyper-v。
        - 收集的資料會匯總以建立10分鐘的單一資料點。
        - [尖峰使用率] 值會從所有 20/30 秒的資料點選取，並傳送至 Azure 進行評估計算。
        - 根據評估屬性中指定的百分位數值（第50個/第10部/第 95/第99），以遞增順序排序十分鐘點，並使用適當的百分位數值來計算評量
    - 針對伺服器遷移，應用裝置會開始收集 VM 資料，並將其複寫至 Azure。
4. **評估和遷移**：您現在可以從使用 Azure Migrate Server 評估的設備所收集的中繼資料來建立評量。 此外，您也可以使用 Azure Migrate Server 遷移來開始遷移 VMware Vm，以協調無代理程式 VM 複寫。


![架構](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>設備升級

設備會隨著應用裝置上執行的 Azure Migrate 代理程式更新而升級。

- 這會自動發生，因為預設會在設備上啟用自動更新。
- 您可以變更此預設設定，以手動更新代理程式。
- 若要停用自動更新，請移至 [登錄編輯程式] > HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance，並將登錄機碼 [自動更新] 設定為0（DWORD）。
 
### <a name="set-agent-updates-to-manual"></a>將代理程式更新設定為手動

若要進行手動更新，請確定您會使用設備上每個過期代理程式的 [**更新**] 按鈕，同時更新設備上的所有代理程式。 您可以隨時將更新設定切換回 [自動更新]。

## <a name="next-steps"></a>後續步驟

[瞭解如何](tutorial-assess-vmware.md#set-up-the-appliance-vm)設定 VMware 的應用裝置。
[瞭解如何](tutorial-assess-hyper-v.md#set-up-the-appliance-vm)設定 hyper-v 的應用裝置。

