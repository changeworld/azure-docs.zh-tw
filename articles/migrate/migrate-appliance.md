---
title: Azure Migrate 設備
description: 提供伺服器評估和遷移中所使用的 Azure Migrate 設備的總覽。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 439f6d9c80a0b93f071d30d580facc4604cabbac
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780329"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 設備

本文摘要說明 Azure Migrate 設備的必要條件和支援需求。 

## <a name="deployment-scenarios"></a>部署案例

在下列案例中會使用 Azure Migrate 設備。

**案例** | **工具** | **用於** 
--- | --- | ---
**VMware VM 評估** | Azure Migrate：伺服器評估 | 探索 VMware Vm<br/><br/> 探索機器應用程式和相依性<br/><br/> 收集機器中繼資料和效能中繼資料以進行評量。
**VMware VM 無代理程式遷移** | Azure Migrate：伺服器遷移 | 探索 VMware Vm <br/><br/> 使用無代理程式遷移來複寫 VMware Vm。
**Hyper-v VM 評估** | Azure Migrate：伺服器評估 | 探索 Hyper-v Vm<br/><br/> 收集機器中繼資料和效能中繼資料以進行評量。
**實體機器評估** |  Azure Migrate：伺服器評估 |  探索實體伺服器（或您視為實體伺服器的 Vm）。<br/><br/> 收集機器中繼資料和效能中繼資料以進行評量。

## <a name="deployment-methods"></a>部署方法

您可以使用幾種方法來部署設備：

- 您可以使用適用于 VMware Vm 和 Hyper-v Vm （適用于 VMware 的 OVA 範本或適用于 Hyper-v 的 VHD）的範本來部署設備。
- 如果您不想要使用範本，您可以使用 PowerShell 腳本來部署適用于 VMware 或 Hyper-v 的應用裝置。
- 在 Azure Government 中，您應該使用腳本來部署應用裝置。
- 針對實體伺服器，您一律會使用腳本來部署應用裝置。
- 下表提供下載連結。


## <a name="appliance---vmware"></a>設備-VMware 

下表摘要說明 VMware 的 Azure Migrate 設備需求。

**需求** | **VMware** 
--- | ---
**權限** | 若要在本機或遠端存取應用裝置 web 應用程式，您必須是網域系統管理員，或設備電腦上的本機系統管理員。
**設備元件** | 設備具有下列元件：<br/><br/> - **管理應用程式**：這是在設備部署期間用於使用者輸入的 web 應用程式。 用來評估要遷移至 Azure 的機器。<br/> - **探索代理程式**：代理程式會收集電腦設定資料。 用來評估要遷移至 Azure 的機器。<br/>- **評量代理程式**：代理程式會收集效能資料。 用來評估要遷移至 Azure 的機器。<br/>- **自動更新服務**：更新設備元件（每隔24小時執行一次）。<br/>- **DRA 代理程式**：協調 VM 複寫，並協調複寫機器與 Azure 之間的通訊。 只有在使用無代理程式遷移將 VMware Vm 複寫至 Azure 時才使用。<br/>- **閘道**：將複寫的資料傳送至 Azure。 只有在使用無代理程式遷移將 VMware Vm 複寫至 Azure 時才使用。
**支援的部署** | 使用 OVA 範本部署為 VMware VM。<br/><br/> 使用 PowerShell 安裝腳本，將部署為 VMware VM 或實體機器。
**專案支援** |  應用裝置可以與單一專案相關聯。 <br/> 任何數目的設備都可以與單一專案相關聯。<br/> 
**探索限制** | 設備可以在 vCenter Server 上探索最多10000個 VMware Vm。<br/> 設備可以連接到單一 vCenter Server。
**OVA 範本** | 從入口網站或下載https://aka.ms/migrate/appliance/vmware。<br/><br/> 下載大小為 11.2 GB。<br/><br/> 下載的應用裝置範本隨附 Windows Server 2016 評估授權，其有效期為180天。 如果評估期接近到期日，建議您下載並部署新的應用裝置，或啟用設備 VM 的作業系統授權。
**PowerShell 指令碼** | 腳本[下載](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 
**軟體/硬體** |  設備應該在具有 Windows Server 2016、32 GB RAM、8個 vcpu、大約 80 GB 磁片儲存體和外部虛擬交換器的機器上執行。<br/> 設備必須直接或透過 proxy 存取網際網路。<br/><br/> 如果您在 VMware VM 上執行設備，您需要 vCenter Server 的足夠資源，才能配置符合需求的 VM。<br/><br/> 如果您在實體機器上執行設備，請確定它正在執行 Windows Server 2016，並符合硬體需求。 
**VMware 需求** | 如果您將設備部署為 VMware VM，則必須將它部署在執行5.5 版或更新版本的 ESXi 主機上。<br/><br/> vCenter Server 執行5.5、6.0、6.5 或6.7。
**VDDK （無代理程式遷移）** | 如果您將設備部署為 VMware VM，而且您正在執行無代理程式遷移，則必須在應用裝置 VM 上安裝 VMware vSphere VDDK。
**雜湊值-OVA** | [確認](tutorial-assess-vmware.md#verify-security)OVA 範本雜湊值。
**雜湊值-PowerShell 腳本** | [確認](deploy-appliance-script.md#verify-file-security)PowerShell 腳本雜湊值。




## <a name="appliance---hyper-v"></a>設備-Hyper-v

**需求** | **Hyper-V** 
--- | ---
**權限** | 若要在本機或遠端存取應用裝置 web 應用程式，您必須是網域系統管理員，或設備電腦上的本機系統管理員。
**設備元件** | 設備具有下列元件：<br/><br/>- **管理應用程式**：這是在設備部署期間用於使用者輸入的 web 應用程式。 用來評估要遷移至 Azure 的機器。<br/> - **探索代理程式**：代理程式會收集電腦設定資料。 用來評估要遷移至 Azure 的機器。<br/>- **評量代理程式**：代理程式會收集效能資料。 用來評估要遷移至 Azure 的機器。<br/>- **自動更新服務**：更新設備元件（每隔24小時執行一次）。
**支援的部署** | 使用 VHD 範本部署為 Hyper-v VM。<br/><br/> 使用 PowerShell 安裝腳本部署為 Hyper-v VM 或實體機器。
**專案支援** |  應用裝置可以與單一專案相關聯。 <br/> 任何數目的設備都可以與單一專案相關聯。<br/> 
**探索限制** | 設備可以探索最多5000部 Hyper-v Vm。<br/> 設備最多可以連線到300的 Hyper-v 主機。
**VHD 範本** | 包含 VHD 的 ZIP 檔案夾。 從入口網站或下載https://aka.ms/migrate/appliance/hyperv。<br/><br/> 下載大小為 10 GB。<br/><br/> 下載的應用裝置範本隨附 Windows Server 2016 評估授權，其有效期為180天。 如果評估期接近到期日，建議您下載並部署新的應用裝置，或啟用設備 VM 的作業系統授權。
**PowerShell 指令碼** | 腳本[下載](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 
**軟體/硬體***   |  設備應該在具有 Windows Server 2016、32 GB RAM、8個 vcpu、大約 80 GB 磁片儲存體和外部虛擬交換器的機器上執行。<br/> 設備需要靜態或動態 IP 位址，而且必須直接或透過 proxy 存取網際網路。<br/><br/> 如果您以 Hyper-v VM 的身分執行設備，則在 Hyper-v 主機上需要足夠的資源來配置 16 GB RAM、8個 vcpu，大約 80 GB 的儲存空間，以及適用于設備 VM 的外部交換器。<br/><br/> 如果您在實體機器上執行設備，請確定它正在執行 Windows Server 2016，並符合硬體需求。 
**Hyper-v 需求** | 如果您使用 VHD 範本來部署設備，Azure Migrate 提供的設備 VM 就是 Hyper-v VM 5.0 版。<br/><br/> Hyper-v 主機必須執行 Windows Server 2012 R2 或更新版本。 
**雜湊值-VHD** | [確認](tutorial-assess-hyper-v.md#verify-security)VHD 範本的雜湊值。
**雜湊值-PowerShell 腳本** | [確認](deploy-appliance-script.md#verify-file-security)PowerShell 腳本雜湊值。


## <a name="appliance---physical"></a>設備-實體

**需求** | **實體** 
--- | ---
**權限** | 若要在本機或遠端存取應用裝置 web 應用程式，您必須是網域系統管理員，或設備電腦上的本機系統管理員。
**設備元件** | 設備具有下列元件： <br/><br/> - **管理應用程式**：這是在設備部署期間用於使用者輸入的 web 應用程式。 用來評估要遷移至 Azure 的機器。<br/> - **探索代理程式**：代理程式會收集電腦設定資料。 用來評估要遷移至 Azure 的機器。<br/>- **評量代理程式**：代理程式會收集效能資料。 用來評估要遷移至 Azure 的機器。<br/>- **自動更新服務**：更新設備元件（每隔24小時執行一次）。
**支援的部署** | 使用 PowerShell 安裝腳本，部署為專用的實體機器或 VM。 您可以從入口網站下載此腳本。
**專案支援** |  應用裝置可以與單一專案相關聯。 <br/> 任何數目的設備都可以與單一專案相關聯。<br/> 
**探索限制** | 設備可以探索最多250部實體伺服器。
**PowerShell 指令碼** | 從入口網站下載 ZIP 檔案夾中的腳本（AzureMigrateInstaller）。 [深入了解](tutorial-assess-physical.md#set-up-the-appliance)。 或者，[直接下載](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 下載大小為 59.7 MB。
**軟體/硬體** |  設備應該在具有 Windows Server 2016、32 GB RAM、8個 vcpu、大約 80 GB 磁片儲存體和外部虛擬交換器的機器上執行。<br/> 設備需要靜態或動態 IP 位址，而且必須直接或透過 proxy 存取網際網路。<br/><br/> 如果您在實體機器上執行設備，請確定它正在執行 Windows Server 2016，並符合硬體需求。<br/> 不支援在具有 Windows Server 2019 的電腦上執行設備。
**雜湊值** | [確認](deploy-appliance-script.md#verify-file-security)PowerShell 腳本雜湊值。

## <a name="url-access"></a>URL 存取

Azure Migrate 設備需要網際網路的連線能力。

- 當您部署設備時，Azure Migrate 會對所需的 Url 進行連線檢查。
- 您需要允許存取清單中的所有 Url。 如果您只是進行評估，可以略過標示為 [僅限 VMware 無代理程式遷移] 所需的 Url。
-  如果您使用以 URL 為基礎的 proxy 來連線到網際網路，請確定 proxy 會解析查詢 Url 時所收到的任何 CNAME 記錄。

### <a name="public-cloud-urls"></a>公用雲端 Url

**URL** | **詳細資料**  
--- | --- |
*.portal.azure.com  | 瀏覽至 Azure 入口網站。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | 登入您的 Azure 訂用帳戶。
*.microsoftonline.com <br/> *.microsoftonline-p.com | 建立設備 Azure Active Directory （AD）應用程式，以與 Azure Migrate 通訊。
management.azure.com | 建立設備 Azure AD 應用程式，以與 Azure Migrate 服務進行通訊。
*.services.visualstudio.com | 上傳用於內部監視的應用程式記錄。
*.vault.azure.net | 管理 Azure Key Vault 中的秘密。
aka.ms/* | 允許存取稱為的連結。 用於 Azure Migrate 設備更新。
download.microsoft.com/download | 允許從 Microsoft 下載下載。
*.servicebus.windows.net | 設備與 Azure Migrate 服務之間的通訊。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | 連接到 Azure Migrate 服務 Url。
*.hypervrecoverymanager.windowsazure.com | **用於 VMware 無代理程式遷移**<br/><br/> 連接到 Azure Migrate 服務 Url。
*.blob.core.windows.net |  **用於 VMware 無代理程式遷移**<br/><br/>將資料上傳至儲存體以進行遷移。

### <a name="government-cloud-urls"></a>政府雲端 Url

**URL** | **詳細資料**  
--- | --- |
*.portal.azure.us  | 瀏覽至 Azure 入口網站。
graph.windows.net | 登入您的 Azure 訂用帳戶。
login.microsoftonline.us  | 建立設備 Azure Active Directory （AD）應用程式，以與 Azure Migrate 通訊。
management.usgovcloudapi.net | 建立設備 Azure AD 應用程式，以與 Azure Migrate 服務進行通訊。
dc.services.visualstudio.com | 上傳用於內部監視的應用程式記錄。
*. vault.usgovcloudapi.net | 管理 Azure Key Vault 中的秘密。
aka.ms/* | 允許存取稱為的連結。 用於 Azure Migrate 設備更新。
download.microsoft.com/download | 允許從 Microsoft 下載下載。
*. servicebus.usgovcloudapi.net  | 設備與 Azure Migrate 服務之間的通訊。
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | 連接到 Azure Migrate 服務 Url。
*.hypervrecoverymanager.windowsazure.us | **用於 VMware 無代理程式遷移**<br/><br/> 連接到 Azure Migrate 服務 Url。
*.blob.core.usgovcloudapi.net  |  **用於 VMware 無代理程式遷移**<br/><br/>將資料上傳至儲存體以進行遷移。
*. applicationinsights.us | 上傳用於內部監視的應用程式記錄。





## <a name="collected-data---vmware"></a>收集的資料-VMware

設備會收集中繼資料、效能資料和相依性分析資料（如果使用無代理程式相依性[分析](concepts-dependency-visualization.md)）。

### <a name="metadata"></a>中繼資料

Azure Migrate 設備所探索到的中繼資料，可協助您找出機器和應用程式是否已準備好遷移至 Azure、適當調整機器和應用程式的大小、規劃成本，以及分析應用程式相依性。 Microsoft 不會在任何授權合規性審查中使用此資料。

以下是設備收集並傳送至 Azure 的 VMware VM 中繼資料完整清單。

**DATA** | **抵禦**
--- | --- 
**機器詳細資料** | 
VM 識別碼 | vm.Config.InstanceUuid 
VM 名稱 | vm.Config.Name
vCenter Server 識別碼 | VMwareClient. Instance. Uuid
VM 描述 | vm.Summary.Config.Annotation
授權產品名稱 | vm.Client.ServiceContent.About.LicenseProductName
作業系統類型 | 機內.SummaryConfig.GuestFullName
開機類型 | vm.Config.Firmware
核心數目 | vm.Config.Hardware.NumCPU
記憶體 (MB) | vm.Config.Hardware.MemoryMB
磁碟數量 | 機內.ToList （）。FindAll （x => 是 VirtualDisk）。計數
磁碟大小清單 | 機內.ToList （）。FindAll （x => 是 VirtualDisk）
網路介面卡清單 | 機內.ToList （）。FindAll （x => 是 VirtualEthernet）。計數
CPU 使用率 | cpu.usage.average
記憶體使用量 |mem.usage.average
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

### <a name="performance-data"></a>效能資料


以下是設備收集並傳送至 Azure 的 VMware VM 效能資料。

**Data** | **計數器** | **評定影響**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 建議的 VM 大小/成本
記憶體使用量 | mem.usage.average | 建議的 VM 大小/成本
磁片讀取輸送量（每秒 MB） | virtualDisk.read.average | 磁片大小、儲存體成本、VM 大小的計算
磁片寫入輸送量（每秒 MB） | virtualDisk.write.average | 磁片大小、儲存體成本、VM 大小的計算
每秒的磁片讀取作業數 | virtualDisk.numberReadAveraged.average | 磁片大小、儲存體成本、VM 大小的計算
每秒的磁片寫入作業數 | virtualDisk.numberReadAveraged.average  | 磁片大小、儲存體成本、VM 大小的計算
NIC 讀取輸送量（每秒 MB） | net.received.average | VM 大小的計算
NIC 寫入輸送量（每秒 MB） | net.transmitted.average  |VM 大小的計算

### <a name="app-dependencies-metadata"></a>應用程式相依性中繼資料

無代理程式相依性分析會收集連接和處理資料。

#### <a name="connection-data"></a>連接資料

以下是設備從啟用無代理程式相依性分析的每個 VM 收集到的連線資料。 此資料會傳送至 Azure。

**Data** | **使用的命令** 
--- | --- 
本機連接埠 | netstat
本機 IP 位址 | netstat
遠端連接埠 | netstat
遠端 IP 位址 | netstat
TCP 連接狀態 | netstat
處理序識別碼 | netstat
不需要。 作用中連接的 | netstat

#### <a name="process-data"></a>處理資料
以下是設備從啟用了無代理程式相依性分析的每個 VM 收集的處理資料。 此資料會傳送至 Azure。

**Data** | **WMI 類別** | **WMI 類別屬性**
--- | --- | ---
程序名稱 | Win32_Process | ExecutablePath
進程引數 | Win32_Process | CommandLine
應用程式名稱 | Win32_Process | ExecutablePath 屬性的 VersionInfo 參數

#### <a name="linux-vm-data"></a>Linux VM 資料

以下是設備從針對無代理程式相依性分析啟用的每個 Linux VM 所收集的連線和處理資料。 此資料會傳送至 Azure。

**Data** | **使用的命令** 
--- | ---
本機連接埠 | netstat 
本機 IP 位址 | netstat 
遠端連接埠 | netstat 
遠端 IP 位址 | netstat 
TCP 連接狀態 | netstat 
不需要。 作用中連接的 | netstat
處理序識別碼  | netstat 
程序名稱 | ps
進程引數 | ps
應用程式名稱 | dpkg 或 rpm



## <a name="collected-data---hyper-v"></a>收集的資料-Hyper-v

設備會收集中繼資料、效能資料和相依性分析資料（如果使用無代理程式相依性[分析](concepts-dependency-visualization.md)）。

### <a name="metadata"></a>中繼資料
Azure Migrate 設備所探索到的中繼資料，可協助您找出機器和應用程式是否已準備好遷移至 Azure、適當調整機器和應用程式的大小、規劃成本，以及分析應用程式相依性。 Microsoft 不會在任何授權合規性審查中使用此資料。

以下是設備收集並傳送至 Azure 的 Hyper-v VM 中繼資料完整清單。

**DATA** | **WMI 類別** | **WMI 類別屬性**
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
NIC MAC 位址（綜合 Nic） | Msvm_SyntheticEthernetPortSettingData | 位址
NIC 識別碼（舊版 Nic） | MsvmEmulatedEthernetPortSetting 資料 | InstanceID
NIC MAC 識別碼（舊版 Nic） | MsvmEmulatedEthernetPortSetting 資料 | 位址

### <a name="performance-data"></a>效能資料

以下是設備收集並傳送至 Azure 的超級 VM 效能資料。

**效能計數器類別** | **計數器** | **評定影響**
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


## <a name="collected-data---physical"></a>收集的資料-實體

設備會收集中繼資料、效能資料和相依性分析資料（如果使用無代理程式相依性[分析](concepts-dependency-visualization.md)）。

### <a name="windows-metadata"></a>Windows 中繼資料

Azure Migrate 設備所探索到的中繼資料，可協助您找出機器和應用程式是否已準備好遷移至 Azure、適當調整機器和應用程式的大小、規劃成本，以及分析應用程式相依性。 Microsoft 不會在任何授權合規性審查中使用此資料。

以下是設備收集並傳送至 Azure 的 Windows server 中繼資料完整清單。

**DATA** | **WMI 類別** | **WMI 類別屬性**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domain、Name、PartOfDomain
處理器核心計數 | Win32_PRocessor | NumberOfCores
配置的記憶體 | Win32_ComputerSystem | TotalPhysicalMemory
BIOS 序號 | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS GUID | Win32_ComputerSystemProduct | UUID
開機類型 | Win32_DiskPartition | 檢查類型 = **GPT： System**是否有 EFI/BIOS 的磁碟分割
作業系統名稱 | Win32_OperatingSystem | Caption
OS 版本 |Win32_OperatingSystem | 版本
作業系統架構 | Win32_OperatingSystem | OSArchitecture
硬碟計數 | Win32_DiskDrive | Model、Size、DeviceID、媒體型、名稱
磁碟大小 | Win32_DiskDrive | 大小
NIC 清單 | Win32_NetworkAdapterConfiguration | 描述，索引
NIC IP 位址 | Win32_NetworkAdapterConfiguration | IPAddress
NIC MAC 位址 | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Linux 中繼資料

以下是設備收集並傳送至 Azure 的 Linux 伺服器中繼資料完整清單。

**DATA** | **廠商** 
--- | --- 
FQDN | 貓/proc/sys/kernel/hostname，hostname-f
處理器核心計數 |  /proc/cpuinfo \| awk '/^ processor/{print $3} ' \| wc-l
配置的記憶體 | 貓/proc/meminfo \| grep MemTotal \| awk ' {printf "%. 0f"，$ 2/1024} '
BIOS 序號 | lshw \| grep "序列：" \| head-n1 \| awk ' {print $2} ' <br/> /usr/sbin/dmidecode-t 1 \| Grep ' 序列 ' \| awk ' {$1 = ""; $2 = ""; print} '
BIOS GUID | 貓/sys/class/dmi/id/product_uuid
開機類型 | [-d/sys/firmware/efi]  && echo efi \| \| echo BIOS
OS 名稱/版本 | 我們會針對 OS 版本和名稱存取這些檔案：<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
作業系統架構 | Uname-m
硬碟計數 | fdisk-l \| Egrep ' Disk. * bytes ' \| awk ' {print $2} ' \|剪下-f1-d '： '
開機磁片 | df/boot \| sed-n 2p \| awk ' {print $1} '
磁碟大小 | fdisk-l \| Egrep ' Disk. * bytes ' \| egrep $disk： \| awk ' {print $5} '
NIC 清單 | \| ip-HTTPs ' {print $2} '
NIC IP 位址 | ip 位址顯示 $nic \| grep inet \| awk ' {print $2} ' \|剪下-f1-d "/" 
NIC MAC 位址 | ip 位址顯示 $nic \| grep 乙太幣\| awk ' {print $2} '

### <a name="windows-performance-data"></a>Windows 效能資料

以下是設備收集並傳送至 Azure 的 Windows server 效能資料。

**Data** | **WMI 類別** | **WMI 類別屬性**
--- | --- | ---
CPU 使用量 | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
記憶體使用量 | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
NIC 計數 | Win32_PerfFormattedData_Tcpip_NetworkInterface | 取得網路裝置計數。
每個 NIC 收到的資料 | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
每個 NIC 傳輸的資料 | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
硬碟計數 | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | 磁片計數
磁片詳細資料 | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Linux 效能資料

以下是設備收集並傳送至 Azure 的 Linux 伺服器效能資料。

**Data** | **Linux** 
--- | --- 
CPU 使用量 | 貓/proc/stat/| grep ' cpu '/proc/stat
記憶體使用量 | free \| grep 記憶體\| awk ' {print $ 3/$ 2 * 100.0} '
NIC 計數 | lshw 類別網路\| grep eth [0-60] \| wc-l
每個 NIC 收到的資料 | cat/sys/class/net/eth $ nic/statistics/rx_bytes
每個 NIC 傳輸的資料 | cat/sys/class/net/eth $ nic/statistics/tx_bytes
硬碟計數 | fdisk-l \| Egrep ' Disk. * bytes ' \| awk ' {print $2} ' \|剪下-f1-d '： '
磁片詳細資料 | 貓/proc/diskstats


## <a name="appliance-upgrades"></a>設備升級

設備會隨著應用裝置上執行的 Azure Migrate 代理程式更新而升級。 這會自動發生，因為預設會在設備上啟用自動更新。 您可以變更此預設設定，以手動更新應用裝置服務。

### <a name="turn-off-auto-update"></a>關閉自動更新

1. 在執行設備的電腦上，開啟 [登錄編輯程式]。
2. 流覽至**HKEY_LOCAL_MACHINE \software\microsoft\azureappliance**。
3. 若要關閉自動更新，請建立具有 DWORD 值0的登錄機碼**更新**金鑰。

    ![設定登錄機碼](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>開啟自動更新

您可以使用下列其中一種方法來開啟自動更新：

- 從 HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance. 刪除自動更新登錄機碼
- 探索完成之後，在 [設備] Configuration Manager。

若要刪除登錄機碼：

1. 在執行設備的電腦上，開啟 [登錄編輯程式]。
2. 流覽至**HKEY_LOCAL_MACHINE \software\microsoft\azureappliance**。
3. 刪除先前建立用來關閉自動更新的登錄機碼**更新**。

若要在探索完成後從設備 Configuration Manager 開啟：

1. 在設備機器上，開啟 [設備] Configuration Manager。
2. 在 [**設備服務** > ] [**Azure Migrate 元件的自動更新已關閉**] 中，按一下以開啟 [自動更新]。

    ![開啟自動更新](./media/migrate-appliance/turn-on.png)

### <a name="check-the-appliance-services-version"></a>檢查設備服務版本

您可以使用下列其中一種方法來檢查設備服務版本：

- 在設備 Configuration Manager 中，在探索完成後。
- 在設備電腦上的 [**控制台** > ] [**程式和功能**] 中。

若要簽入設備 Configuration Manager：

1. 探索完成之後，請開啟設備 Configuration Manager （在設備 web 應用程式中）。
2. 在 [**設備服務**] 中，確認設備服務版本。

    ![檢查版本](./media/migrate-appliance/version.png)

若要在 [控制台] 中簽入：

1. 在應用裝置上，按一下 [**開始** > **] 控制台** > 的 [**程式和功能**]
2. 檢查清單中的設備服務版本。

    ![檢查 [控制台] 中的版本](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>手動更新較舊的版本

如果您針對任何元件執行較舊的版本，您必須卸載服務，並手動更新為最新版本。

1. 若要檢查最新的設備服務版本，請[下載](https://aka.ms/latestapplianceservices)LatestComponents 檔案。
2.  下載之後，請在 [記事本] 中開啟 LatestComponents。
3. 尋找檔案中最新的服務版本，以及其下載連結。 例如：

    "Name"： "ASRMigrationWebApp"，"DownloadLink"： "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi"，"Version"： "6.0.211.2"，"Md5Hash"： "e00a742acc35e78a64a6a81e75469b84"

4.  使用檔案中的下載連結，下載過時服務的最新版本。
5. 下載之後，請在 [系統管理員命令] 視窗中執行下列命令，以確認所下載 MSI 的完整性。

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ```例如： C：\>CertUtil-HASHFILE C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. 檢查命令輸出是否符合檔案中服務的雜湊值專案（例如，上述的 MD5 雜湊值）。
6. 現在，請執行 MSI 以安裝服務。 這是無訊息安裝，而且安裝視窗會在完成後關閉。
7. 安裝完成之後，請在 [**控制台** > ] [**程式和功能**] 中檢查服務的版本。 服務版本現在應該會升級為 json 檔案中顯示的最新。



## <a name="next-steps"></a>後續步驟

- [瞭解如何](how-to-set-up-appliance-vmware.md)設定 VMware 的應用裝置。
- [瞭解如何](how-to-set-up-appliance-hyper-v.md)設定 hyper-v 的應用裝置。
- [瞭解如何](how-to-set-up-appliance-physical.md)設定實體伺服器的設備。

