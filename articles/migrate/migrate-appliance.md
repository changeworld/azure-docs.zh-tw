---
title: Azure Migrate 設備
description: 提供伺服器評估和遷移中使用的 Azure 遷移設備的概述。
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 1bb3372467919f1471fa9577cd60e9cecaf1750d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336924"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 設備

本文總結了 Azure 遷移設備的先決條件和支援要求。 

## <a name="deployment-scenarios"></a>部署案例

Azure 遷移設備用於以下方案。

**案例** | **工具** | **用於** 
--- | --- | ---
**VMware VM 評估** | Azure 遷移：伺服器評估 | 發現 VMware VM<br/><br/> 發現電腦應用和依賴項<br/><br/> 收集機器中繼資料和性能中繼資料進行評估。
**VMware VM 無代理遷移** | Azure 遷移：伺服器遷移 | 發現 VMware VM <br/><br/> 使用無代理遷移複製 VMware VM。
**超 V VM 評估** | Azure 遷移：伺服器評估 | 發現超虛擬機器<br/><br/> 收集機器中繼資料和性能中繼資料進行評估。
**物理機器評估** |  Azure 遷移：伺服器評估 |  發現物理伺服器（或您視為物理伺服器的 VM）。<br/><br/> 收集機器中繼資料和性能中繼資料進行評估。

## <a name="appliance---vmware"></a>設備 - VMware 

下表總結了 VMware 的 Azure 遷移設備要求。

**要求** | **Vmware** 
--- | ---
**設備元件** | 產品具有以下元件：<br/><br/> - **管理應用**：這是一個 Web 應用程式，用於在設備部署期間使用者輸入。 用於評估電腦以遷移到 Azure 時使用。<br/> - **發現代理**：代理收集電腦配置資料。 用於評估電腦以遷移到 Azure 時使用。<br/>- **評估代理**：代理收集效能資料。 用於評估電腦以遷移到 Azure 時使用。<br/>- **自動更新服務**：更新設備元件（每 24 小時運行一次）。<br/>- **DRA 代理**：協調 VM 複製，並協調複製的電腦和 Azure 之間的通信。 僅在使用無代理遷移將 VMware VM 複製到 Azure 時使用。<br/>- **閘道**：向 Azure 發送複製的資料。 僅在使用無代理遷移將 VMware VM 複製到 Azure 時使用。
**支援部署** | 使用 OVA 範本部署為 VMware VM。<br/><br/> 使用 PowerShell 安裝腳本部署為 VMware VM 或物理電腦。
**專案支援** |  設備可以與單個專案關聯。 <br/> 任意數量的設備都可以與單個專案關聯。<br/> 
**發現限制** | 設備可以在 vCenter 伺服器上發現多達 10，000 個 VMware VM。<br/> 設備可以連接到單個 vCenter 伺服器。
**OVA 範本** | 從門戶或 從https://aka.ms/migrate/appliance/vmware下載。<br/><br/> 下載大小為 11.2 GB。<br/><br/> 下載的設備範本附帶 Windows Server 2016 評估許可證，有效期為 180 天。 如果評估期接近到期，我們建議您下載並部署新設備，或者啟動設備 VM 的作業系統許可證。
**電源外殼腳本** | 腳本[下載](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 
**軟體/硬體** |  設備應在具有 Windows Server 2016、32 GB RAM、8 個 vCPU、大約 80 GB 磁片存儲和外部虛擬交換器的電腦上運行。<br/> 本產品需要直接或通過代理進行互聯網接入。<br/><br/> 如果在 VMware VM 上運行設備，則在 vCenter 伺服器上需要足夠的資源來分配滿足要求的 VM。<br/><br/> 如果在物理電腦上運行設備，請確保它運行 Windows Server 2016，並滿足硬體要求。 
**VMware 要求** | 如果將設備部署為 VMware VM，則必須將其部署在運行版本 5.5 或更高版本的 ESXi 主機上。<br/><br/> vCenter 伺服器運行 5.5、6.0、6.5 或 6.7。
**VDDK（無代理遷移）** | 如果將設備部署為 VMware VM，並且正在運行無代理遷移，則必須在設備 VM 上安裝 VMware vSphere VDDK。
**雜湊值-OVA** | [驗證](tutorial-assess-vmware.md#verify-security)OVA 範本雜湊值。
**雜湊值-電源外殼腳本** | [驗證](deploy-appliance-script.md#verify-file-security)PowerShell 腳本雜湊值。




## <a name="appliance---hyper-v"></a>設備 - 超 V

**要求** | **Hyper-V** 
--- | ---
**設備元件** | 產品具有以下元件：<br/><br/>- **管理應用**：這是一個 Web 應用程式，用於在設備部署期間使用者輸入。 用於評估電腦以遷移到 Azure 時使用。<br/> - **發現代理**：代理收集電腦配置資料。 用於評估電腦以遷移到 Azure 時使用。<br/>- **評估代理**：代理收集效能資料。 用於評估電腦以遷移到 Azure 時使用。<br/>- **自動更新服務**：更新設備元件（每 24 小時運行一次）。
**支援部署** | 使用 VHD 範本部署為 Hyper-V VM。<br/><br/> 使用 PowerShell 安裝腳本部署為 Hyper-V VM 或物理電腦。
**專案支援** |  設備可以與單個專案關聯。 <br/> 任意數量的設備都可以與單個專案關聯。<br/> 
**發現限制** | 設備可以發現多達 5000 個超 V VM。<br/> 設備可連接到多達 300 台 Hyper-V 主機。
**VHD 範本** | 壓縮資料夾，包括 VHD。 從門戶或 從https://aka.ms/migrate/appliance/hyperv下載。<br/><br/> 下載大小為 10 GB。<br/><br/> 下載的設備範本附帶 Windows Server 2016 評估許可證，有效期為 180 天。 如果評估期接近到期，我們建議您下載並部署新設備，或者啟動設備 VM 的作業系統許可證。
**電源外殼腳本** | 腳本[下載](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 
**軟體/硬體***   |  設備應在具有 Windows Server 2016、32 GB RAM、8 個 vCPU、大約 80 GB 磁片存儲和外部虛擬交換器的電腦上運行。<br/> 設備需要靜態或動態 IP 位址，並且需要直接或通過代理進行 Internet 訪問。<br/><br/> 如果將設備作為 Hyper-V VM 運行，則 Hyper-V 主機上需要足夠的資源來分配 16 GB RAM、8 個 vCPU、大約 80 GB 的存儲空間以及設備 VM 的外部交換器。<br/><br/> 如果在物理電腦上運行設備，請確保它運行 Windows Server 2016，並滿足硬體要求。 
**超 V 要求** | 如果使用 VHD 範本部署設備，Azure 遷移提供的設備 VM 是 Hyper-V VM 版本 5.0。<br/><br/> Hyper-V 主機必須運行 Windows Server 2012 R2 或更高版本。 
**雜湊值-VHD** | [驗證](tutorial-assess-hyper-v.md#verify-security)VHD 範本雜湊值。
**雜湊值-電源外殼腳本** | [驗證](deploy-appliance-script.md#verify-file-security)PowerShell 腳本雜湊值。


## <a name="appliance---physical"></a>設備 - 物理

**要求** | **實體** 
--- | ---
**設備元件** | 產品具有以下元件： <br/><br/> - **管理應用**：這是一個 Web 應用程式，用於在設備部署期間使用者輸入。 用於評估電腦以遷移到 Azure 時使用。<br/> - **發現代理**：代理收集電腦配置資料。 用於評估電腦以遷移到 Azure 時使用。<br/>- **評估代理**：代理收集效能資料。 用於評估電腦以遷移到 Azure 時使用。<br/>- **自動更新服務**：更新設備元件（每 24 小時運行一次）。
**支援部署** | 使用 PowerShell 安裝腳本部署為專用物理電腦或 VM。
**專案支援** |  設備可以與單個專案關聯。 <br/> 任意數量的設備都可以與單個專案關聯。<br/> 
**發現限制** | 設備可以發現多達 250 台物理伺服器。
**電源外殼腳本** | 從門戶將腳本 （AzureMigrate 安裝程式.ps1） 下載到壓縮資料夾中。 [深入了解](tutorial-assess-physical.md#set-up-the-appliance)。 或者，[直接下載](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 下載大小為 59.7 MB。
**軟體/硬體** |  設備應在具有 Windows Server 2016、32 GB RAM、8 個 vCPU、大約 80 GB 磁片存儲和外部虛擬交換器的電腦上運行。<br/> 設備需要靜態或動態 IP 位址，並且需要直接或通過代理進行 Internet 訪問。<br/><br/> 如果在物理電腦上運行設備，請確保它運行 Windows Server 2016，並滿足硬體要求。 
**雜湊值** | [驗證](deploy-appliance-script.md#verify-file-security)PowerShell 腳本雜湊值。

## <a name="url-access"></a>URL 存取

Azure 遷移設備需要連接到 Internet。

- 部署設備時，Azure 遷移對下表中總結的 URL 執行連接檢查。
- 如果您使用的是基於 URL 的代理連接到 Internet，則允許訪問這些 URL，確保代理解析在查找 URL 時收到的任何 CNAME 記錄。

**Url** | **詳細資料**  
--- | --- |
*.portal.azure.com  | 瀏覽至 Azure 入口網站。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | 登入您的 Azure 訂用帳戶。
*.microsoftonline.com <br/> *.microsoftonline-p.com | 為設備創建活動目錄應用，以便與 Azure 遷移通信。
management.azure.com | 為設備創建活動目錄應用，以便與 Azure 遷移服務進行通信。
dc.services.visualstudio.com | 上傳用於內部監視的應用日誌。
*.vault.azure.net | 在 Azure 金鑰保存庫中管理機密。
aka.ms/* | 允許訪問也稱為連結。 用於 Azure 遷移設備更新。
download.microsoft.com/download | 允許從微軟下載下載。
*.servicebus.windows.net | 設備和 Azure 遷移服務之間的通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | 連接到 Azure 遷移服務 URL。
*.hypervrecoverymanager.windowsazure.com | **用於無 VMware 無代理遷移**<br/><br/> 連接到 Azure 遷移服務 URL。
*.blob.core.windows.net |  **用於無 VMware 無代理遷移**<br/><br/>將資料上載到存儲以進行遷移。




## <a name="collected-data---vmware"></a>收集的資料 - VMware

設備收集中繼資料、效能資料和依賴關係分析資料（如果使用無代理[依賴關係分析](concepts-dependency-visualization.md)）。

### <a name="metadata"></a>中繼資料

Azure 遷移應用發現中繼資料可説明您確定電腦和應用是否已準備好遷移到 Azure、大小合適的電腦和應用、規劃成本並分析應用程式依賴項。 Microsoft 不會在任何許可證合規性審核中使用此資料。

下面是設備收集和發送到 Azure 的 VMware VM 中繼資料的完整清單。

**資料** | **計數器**
--- | --- 
**機器詳細資訊** | 
VM 識別碼 | vm.Config.InstanceUuid 
VM 名稱 | vm.Config.Name
vCenter Server 識別碼 | VMwareClient.實例.Uuid
VM 描述 | vm.Summary.Config.Annotation
授權產品名稱 | vm.Client.ServiceContent.About.LicenseProductName
作業系統類型 | Vm。摘要配置.來賓全名
開機類型 | vm.Config.Firmware
核心數目 | vm.Config.Hardware.NumCPU
記憶體 (MB) | vm.Config.Hardware.MemoryMB
磁碟數量 | Vm。配置.硬體.設備.清單。）查找全部（x =>是虛擬磁片）。
磁碟大小清單 | Vm。配置.硬體.設備.清單。）查找全部（x =>是虛擬磁片）
網路介面卡清單 | Vm。配置.硬體.設備.清單。）查找全部（x =>是虛擬乙太網）。
CPU 使用率 | cpu.usage.average
記憶體使用量 |mem.usage.average
**每個磁片詳細資訊** | 
磁碟機碼值 | disk.Key
迪庫奈特編號 | disk.UnitNumber
磁碟控制器機碼值 | disk.ControllerKey.Value
已佈建的 GB 數 | virtualDisk.DeviceInfo.Summary
磁碟名稱 | 使用磁片生成的值。單位編號，磁片。鑰匙，磁片控制器鍵.VAlue
每秒的讀取作業數 | virtualDisk.numberReadAveraged.average
每秒的寫入作業數 | virtualDisk.numberReadAveraged.average
讀取輸送量（每秒 MB） | virtualDisk.read.average
寫入輸送量（每秒 MB） | virtualDisk.write.average
**每個 NIC 詳細資訊** | 
網路介面卡名稱 | nic.Key
MAC 位址 | ((VirtualEthernetCard)nic).MacAddress
IPv4 位址 | vm.Guest.Net
IPv6 位址 | vm.Guest.Net
讀取輸送量（每秒 MB） | net.received.average
寫入輸送量（每秒 MB） | net.transmitted.average
**庫存路徑詳細資訊** | 
名稱 | container.GetType().Name
子物件的類型 | container.ChildType
參考詳細資料 | container.MoRef
父系詳細資料 | Container.Parent
每個 VM 的資料夾詳細資訊 | ((Folder)container).ChildEntity.Type
每個 VM 的資料中心詳細資訊 | ((Datacenter)container).VmFolder
每個主機資料夾的資料中心詳細資訊 | ((Datacenter)container).HostFolder
每個主機的群集詳細資訊 | （（群集計算資源）容器）。主機
每個 VM 的主機詳細資訊 | （（主機系統）容器）。Vm

### <a name="performance-data"></a>效能資料


以下是設備收集和發送到 Azure 的 VMware VM 效能資料。

**資料** | **計數器** | **評定影響**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 推薦的 VM 大小/成本
記憶體使用量 | mem.usage.average | 推薦的 VM 大小/成本
磁片讀取輸送量（MB/秒） | virtualDisk.read.average | 計算磁片大小、存儲成本、VM 大小
磁片寫入輸送量（MB/秒） | virtualDisk.write.average | 計算磁片大小、存儲成本、VM 大小
每秒磁片讀取操作 | virtualDisk.numberReadAveraged.average | 計算磁片大小、存儲成本、VM 大小
每秒磁片寫入操作 | virtualDisk.numberReadAveraged.average  | 計算磁片大小、存儲成本、VM 大小
NIC 讀取輸送量（MB/秒） | net.received.average | 計算 VM 大小
NIC 寫入輸送量（MB/秒） | net.transmitted.average  |計算 VM 大小

### <a name="app-dependencies-metadata"></a>應用依賴項中繼資料

無代理依賴項分析收集連接和處理資料。

#### <a name="connection-data"></a>連接資料

以下是設備從啟用用於無代理依賴項分析的每個 VM 中收集的連接資料。 此資料將發送到 Azure。

**資料** | **使用的命令** 
--- | --- 
本機連接埠 | netstat
本機 IP 位址 | netstat
遠端連接埠 | netstat
遠端 IP 位址 | netstat
TCP 連接狀態 | netstat
處理序識別碼 | netstat
否。 活動連接 | netstat

#### <a name="process-data"></a>處理資料
以下是設備從啟用的無代理依賴項分析的每個 VM 收集的進程資料。 此資料將發送到 Azure。

**資料** | **WMI 類別** | **WMI 類屬性**
--- | --- | ---
程序名稱 | Win32_Process | 可執行路徑
進程參數 | Win32_Process | CommandLine
應用程式名稱 | Win32_Process | 可執行路徑屬性的版本資訊.產品名稱參數

#### <a name="linux-vm-data"></a>Linux VM 資料

以下是設備從啟用的無代理依賴項分析的每個 Linux VM 中收集的連接和處理資料。 此資料將發送到 Azure。

**資料** | **使用的命令** 
--- | ---
本機連接埠 | netstat 
本機 IP 位址 | netstat 
遠端連接埠 | netstat 
遠端 IP 位址 | netstat 
TCP 連接狀態 | netstat 
否。 活動連接 | netstat
處理序識別碼  | netstat 
程序名稱 | ps
進程參數 | ps
應用程式名稱 | dpkg 或 rpm



## <a name="collected-data---hyper-v"></a>收集的資料 - 超 V

設備收集中繼資料、效能資料和依賴關係分析資料（如果使用無代理[依賴關係分析](concepts-dependency-visualization.md)）。

### <a name="metadata"></a>中繼資料
Azure 遷移應用發現中繼資料可説明您確定電腦和應用是否已準備好遷移到 Azure、大小合適的電腦和應用、規劃成本並分析應用程式依賴項。 Microsoft 不會在任何許可證合規性審核中使用此資料。

下面是設備收集和發送到 Azure 的 Hyper-V VM 中繼資料的完整清單。

**資料* | **WMI CLASS** | **WMI CLASS 屬性**
--- | --- | ---
**機器詳細資訊** | 
BIOS 的序號 = Msvm_BIOSElement | BIOS序列編號
VM 類型（第 1 代或第 2 代） | Msvm_VirtualSystemSettingData | 虛擬系統子類型
VM 顯示名稱 | Msvm_VirtualSystemSettingData | ElementName
VM 版本 | Msvm_ProcessorSettingData | 虛擬數量
記憶體（位元組） | Msvm_MemorySettingData | 虛擬數量
VM 可使用的最大記憶體 | Msvm_MemorySettingData | 限制
啟用動態記憶體 | Msvm_MemorySettingData | DynamicMemoryEnabled
作業系統名稱/版本/FQDN | Msvm_KvpExchangeComponent | 來賓內部交換專案名稱資料
VM 電源狀態 | Msvm_ComputerSystem | EnabledState
**每個磁片詳細資訊** | 
磁片識別碼 | Msvm_VirtualHardDiskSettingData | 虛擬磁片Id
虛擬硬碟類型 | Msvm_VirtualHardDiskSettingData | 類型
虛擬硬碟大小 | Msvm_VirtualHardDiskSettingData | 最大內部大小
虛擬硬碟父級 | Msvm_VirtualHardDiskSettingData | 父路徑
**每個 NIC 詳細資訊** | 
IP 位址（合成 NIC） | Msvm_GuestNetworkAdapterConfiguration | IP 位址
啟用 DHCP（合成 NIC） | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID（合成 NIC） | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 位址（合成 NIC） | Msvm_SyntheticEthernetPortSettingData | 位址
NIC ID（舊 NIC） | Ms類比乙太網埠設置資料 | InstanceID
NIC MAC ID（舊 NIC） | Ms類比乙太網埠設置資料 | 位址

### <a name="performance-data"></a>效能資料

以下是設備收集和發送到 Azure 的 Hyper VM 效能資料。

**效能計數器類** | **計數器** | **評定影響**
--- | --- | ---
超V虛擬機器管理程式虛擬處理器 | 百分比來賓執行時間 | 推薦的 VM 大小/成本
超 V 動態記憶體 VM | 電流壓力 （%）<br/> 訪客可見實體記憶體 （MB） | 推薦的 VM 大小/成本
超V虛擬存放裝置 | 讀取位元組/秒 | 計算磁片大小、存儲成本、VM 大小
超V虛擬存放裝置 | 寫入位元組/秒 | 計算磁片大小、存儲成本、VM 大小
Hyper-V 虛擬網路介面卡 | 接收位元組/秒 | 計算 VM 大小
Hyper-V 虛擬網路介面卡 | 傳送的位元組/秒 | 計算 VM 大小

- CPU 利用率是連接到 VM 的所有虛擬處理器的所有使用方式的總和。
- 記憶體利用率為（當前壓力 = 來賓可見實體記憶體）/ 100。
- 磁片和網路利用率值從列出的 Hyper-V 效能計數器收集。

## <a name="appliance-upgrades"></a>設備升級

隨著在設備上運行的 Azure 遷移代理更新，設備將升級。 這是自動發生的，因為預設情況下在設備上啟用了自動更新。 您可以更改此預設設置以手動更新代理。

- **關閉自動更新**：通過將HKEY_LOCAL_MACHINE_SOFTWARE_Microsoft_AzureAppliance"自動更新"鍵設置為 0 （DWORD）來關閉註冊表中的自動更新。 如果您決定使用手動更新，請務必同時更新設備上的所有代理，使用設備上每個過期代理的 **"更新**"按鈕。
- **手動更新**：對於手動更新，請確保使用設備上每個過期代理的 **"更新**"按鈕更新設備上的所有代理。 您可以隨時將更新設置切換回自動更新。

![自動更新設備](./media/migrate-appliance/autoupdate.png)

## <a name="next-steps"></a>後續步驟

- [瞭解如何](how-to-set-up-appliance-vmware.md)為 VMware 設置設備。
- [瞭解如何](how-to-set-up-appliance-hyper-v.md)為 Hyper-V 設置產品。
- [瞭解如何](how-to-set-up-appliance-physical.md)為物理伺服器設置設備。

