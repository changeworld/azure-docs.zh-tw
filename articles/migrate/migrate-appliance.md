---
title: Azure Migrate 設備
description: 提供伺服器評估和移轉中所使用 Azure Migrate 設備的概觀。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: a313d7b964e6ea849acb5b034ed55975b8c5b524
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927492"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 設備

此文章摘要說明 Azure Migrate 設備的先決條件與支援需求。 

## <a name="deployment-scenarios"></a>部署案例

在下列案例中會使用 Azure Migrate 設備。

**案例** | **工具** | **用於** 
--- | --- | ---
**VMware VM 評估** | Azure Migrate：伺服器評量 | 探索 VMware VM<br/><br/> 探索機器應用程式和相依性<br/><br/> 收集機器中繼資料和效能中繼資料以進行評量。
**VMware VM 無代理程式移轉** | Azure Migrate：伺服器移轉 | 探索 VMware VM <br/><br/> 使用無代理程式移轉來複寫 VMware VM。
**Hyper-V VM 評估** | Azure Migrate：伺服器評量 | 探索 Hyper-V VM<br/><br/> 收集機器中繼資料和效能中繼資料以進行評量。
**實體機器評量** |  Azure Migrate：伺服器評量 |  探索實體伺服器 (或您視為實體伺服器的 VM)。<br/><br/> 收集機器中繼資料和效能中繼資料以進行評量。

## <a name="deployment-methods"></a>部署方法

您可以使用幾種方法來部署設備：

- 您可以使用適用於 VMware VM 和 Hyper-V VM 的範本來部署設備 (適用於 VMware 的 OVA 範本或適用於 Hyper-V 的 VHD)。
- 如果您不想要使用範本，您可以使用 PowerShell 指令碼來部署適用於 VMware 或 Hyper-V 的設備。
- 在 Azure Government 中，您應該使用指令碼來部署設備。
- 針對實體伺服器，您一律會使用指令碼來部署設備。
- 下表提供下載連結。


## <a name="appliance---vmware"></a>設備 - VMware 

下表摘要說明 VMware 的 Azure Migrate 設備需求。

**需求** | **VMware** 
--- | ---
**權限** | 若要在本機或遠端存取設備 Web 應用程式，您必須是網域系統管理員，或設備機器上的本機系統管理員。
**設備元件** | 設備具有下列元件：<br/><br/> - **管理應用程式**：這是在設備部署期間用於使用者輸入的 Web 應用程式。 在評估要移轉至 Azure 的機器時會使用。<br/> - **探索代理程式**：代理程式會收集機器設定資料。 在評估要移轉至 Azure 的機器時會使用。<br/>- **收集器代理程式**：代理程式會收集效能資料。 在評估要移轉至 Azure 的機器時會使用。<br/>- **自動更新服務**：更新設備元件 (每隔 24 小時執行一次)。<br/>- **DRA 代理程式**：協調 VM 複寫，並協調複寫機器與 Azure 之間的通訊。 只有在使用無代理程式移轉將 VMware VM 複寫至 Azure 時才使用。<br/>- **閘道**：將複寫的資料傳送至 Azure。 只有在使用無代理程式移轉將 VMware VM 複寫至 Azure 時才使用。
**支援的部署** | 使用 OVA 範本部署為 VMware VM。<br/><br/> 使用 PowerShell 安裝指令碼部署為 VMware VM 或實體機器。
**專案支援** |  設備可以與單一專案相關聯。 <br/> 任意數目的設備可以與單一專案相關聯。<br/> 
**探索限制** | 設備可以在 vCenter Server 上探索最多 10,000 個 VMware VM。<br/> 設備可以連線到單一 vCenter Server。
**OVA 範本** | 從入口網站或從[這裡](https://go.microsoft.com/fwlink/?linkid=2140333)下載<br/><br/> 下載大小為 11.6 GB。<br/><br/> 下載的設備範本隨附 Windows Server 2016 評估授權，其有效期為 180 天。 如果評估期接近到期日，建議您下載並部署新的設備，或啟用設備 VM 的作業系統授權。
**PowerShell 指令碼** | 請參閱這 [篇文章](./deploy-appliance-script.md#set-up-the-appliance-for-vmware)。<br/><br/> 
**軟體/硬體** |  設備應該在具有 Windows Server 2016、32 GB RAM、8 個 vCPU、約 80 GB 磁碟儲存體和外部虛擬交換器的機器上執行。<br/> 設備需要存取網際網路 (直接或透過 Proxy)。<br/><br/> 如果您在 VMware VM 上執行設備，您需要 vCenter Server 的足夠資源，才能配置符合需求的 VM。<br/><br/> 如果您在實體機器上執行設備，請確定其正在執行 Windows Server 2016，且符合硬體需求。
**VMware 需求** | 如果您將設備部署為 VMware VM，則必須將其部署在執行 5.5 版或更新版本的 ESXi 主機上。<br/><br/> 執行 5.5、6.0、6.5 或 6.7 的 vCenter Server。
**VDDK (無代理程式移轉)** | 如果您將設備部署為 VMware VM，而且您正在執行無代理程式移轉，則必須在設備 VM 上安裝 VMware vSphere VDDK。
**雜湊值 OVA** | [驗證](tutorial-assess-vmware.md#verify-security) OVA 範本雜湊值。
**雜湊值 PowerShell 指令碼** | [驗證](deploy-appliance-script.md#verify-file-security) PowerShell 指令碼雜湊值。




## <a name="appliance---hyper-v"></a>設備 - Hyper-v

**需求** | **Hyper-V** 
--- | ---
**權限** | 若要在本機或遠端存取設備 Web 應用程式，您必須是網域系統管理員，或設備機器上的本機系統管理員。
**設備元件** | 設備具有下列元件：<br/><br/>- **管理應用程式**：這是在設備部署期間用於使用者輸入的 Web 應用程式。 在評估要移轉至 Azure 的機器時會使用。<br/> - **探索代理程式**：代理程式會收集機器設定資料。 在評估要移轉至 Azure 的機器時會使用。<br/>- **收集器代理程式**：代理程式會收集效能資料。 在評估要移轉至 Azure 的機器時會使用。<br/>- **自動更新服務**：更新設備元件 (每隔 24 小時執行一次)。
**支援的部署** | 使用 VHD 範本部署為 Hyper-V VM。<br/><br/> 使用 PowerShell 安裝指令碼部署為 Hyper-V VM 或實體機器。
**專案支援** |  設備可以與單一專案相關聯。 <br/> 任意數目的設備可以與單一專案相關聯。<br/> 
**探索限制** | 設備可以探索最多 5000 部 Hyper-V VM。<br/> 設備最多可以連線到 300 部 Hyper-V 主機。
**VHD 範本** | 包含 VHD 的 ZIP 資料夾。 從入口網站或從 [這裡](https://go.microsoft.com/fwlink/?linkid=2140422)下載。<br/><br/> 下載大小為 10.4 GB。<br/><br/> 下載的設備範本隨附 Windows Server 2016 評估授權，其有效期為 180 天。 如果評估期接近到期日，建議您下載並部署新的設備，或啟用設備 VM 的作業系統授權。
**PowerShell 指令碼** | 請參閱這 [篇文章](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v)。<br/><br/> 
**軟體/硬體***   |  設備應該在具有 Windows Server 2016、16 GB RAM、8個 vcpu、大約 80 GB 磁片儲存體和外部虛擬交換器的電腦上執行。<br/> 設備需要靜態或動態 IP 位址，且需要存取網際網路 (直接或透過 Proxy)。<br/><br/> 如果您以 Hyper-V VM 的身分執行設備，則在 Hyper-V 主機上需要足夠的資源來配置 16 GB RAM、8 個 vCPU，大約 80 GB 的儲存空間，以及適用於設備 VM 的外部交換器。<br/><br/> 如果您在實體機器上執行設備，請確定其正在執行 Windows Server 2016，且符合硬體需求。 
**Hyper-V 需求** | 部署如果您使用 VHD 範本來部署設備，Azure Migrate 提供的設備 VM 就是 Hyper-V VM 5.0 版。<br/><br/> Hyper-V 主機必須執行 Windows Server 2012 R2 或更新版本。 
**雜湊值 VHD** | [驗證](tutorial-assess-hyper-v.md#verify-security) VHD 範本雜湊值。
**雜湊值 PowerShell 指令碼** | [驗證](deploy-appliance-script.md#verify-file-security) PowerShell 指令碼雜湊值。


## <a name="appliance---physical"></a>設備 - 實體

**需求** | **實體** 
--- | ---
**權限** | 若要在本機或遠端存取設備 Web 應用程式，您必須是網域系統管理員，或設備機器上的本機系統管理員。
**設備元件** | 設備具有下列元件： <br/><br/> - **管理應用程式**：這是在設備部署期間用於使用者輸入的 Web 應用程式。 在評估要移轉至 Azure 的機器時會使用。<br/> - **探索代理程式**：代理程式會收集機器設定資料。 在評估要移轉至 Azure 的機器時會使用。<br/>- **收集器代理程式**：代理程式會收集效能資料。 在評估要移轉至 Azure 的機器時會使用。<br/>- **自動更新服務**：更新設備元件 (每隔 24 小時執行一次)。
**支援的部署** | 使用 PowerShell 安裝指令碼，部署為專用的實體機器或 VM。 您可以從入口網站下載此指令碼。
**專案支援** |  設備可以與單一專案相關聯。 <br/> 任意數目的設備可以與單一專案相關聯。<br/> 
**探索限制** | 設備最多可探索1000部實體伺服器。
**PowerShell 指令碼** | 從入口網站或從 [這裡](https://go.microsoft.com/fwlink/?linkid=2140334)下載腳本 ( # A0) 壓縮資料夾中。 [深入了解](tutorial-assess-physical.md#set-up-the-azure-migrate-appliance)。<br/><br/> 下載大小為 85 MB。
**軟體/硬體** |  設備應該在具有 Windows Server 2016、16 GB RAM、8個 vcpu、大約 80 GB 磁片儲存體和外部虛擬交換器的電腦上執行。<br/> 設備需要靜態或動態 IP 位址，且需要存取網際網路 (直接或透過 Proxy)。<br/><br/> 如果您在實體機器上執行設備，請確定其正在執行 Windows Server 2016，且符合硬體需求。<br/> 不支援在使用 Windows Server 2019 的電腦上執行設備。
**雜湊值** | [驗證](deploy-appliance-script.md#verify-file-security) PowerShell 指令碼雜湊值。

## <a name="url-access"></a>URL 存取

Azure Migrate 設備需要對網際網路的連線能力。

- 當您部署設備時，Azure Migrate 會對所需的 URL 進行連線能力檢查。
- 您必須允許存取清單中的所有 URL。 如果您只是進行評量，可以略過標示為僅 VMware 無代理程式移轉需要的 URL。
-  如果您使用 URL 型 Proxy 來連線到網際網路，請確定 Proxy 會解析查詢 URL 時所收到的任何 CNAME 記錄。

### <a name="public-cloud-urls"></a>公用雲端 URL

**URL** | **詳細資料**  
--- | --- |
*.portal.azure.com  | 瀏覽至 Azure 入口網站。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | 登入您的 Azure 訂用帳戶。
*.microsoftonline.com <br/> *.microsoftonline-p.com | 建立 Azure Active Directory (AD) 應用程式，以便設備與 Azure Migrate 通訊。
management.azure.com | 建立 Azure AD 應用程式，以便設備與 Azure Migrate 服務通訊。
*.services.visualstudio.com | 上傳用於內部監視的應用程式記錄。
*.vault.azure.net | 管理 Azure Key Vault 中的祕密。 注意:請確定要複寫的機器可以存取此項目。
aka.ms/* | 允許存取 aka 連結。 用於 Azure Migrate 設備更新。
download.microsoft.com/download | 允許來自 Microsoft 下載的下載項目。
*.servicebus.windows.net | 設備與 Azure Migrate 服務之間的通訊。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | 連線到 Azure Migrate 服務 URL。
*.hypervrecoverymanager.windowsazure.com | **用於 VMware 無代理程式移轉**<br/><br/> 連線到 Azure Migrate 服務 URL。
*.blob.core.windows.net |  **用於 VMware 無代理程式移轉**<br/><br/>將資料上傳至儲存體以進行移轉。

### <a name="government-cloud-urls"></a>政府雲端 URL

**URL** | **詳細資料**  
--- | --- |
*.portal.azure.us  | 瀏覽至 Azure 入口網站。
graph.windows.net | 登入您的 Azure 訂用帳戶。
login.microsoftonline.us  | 建立 Azure Active Directory (AD) 應用程式，以便設備與 Azure Migrate 通訊。
management.usgovcloudapi.net | 建立 Azure AD 應用程式，以便設備與 Azure Migrate 服務通訊。
dc.services.visualstudio.com | 上傳用於內部監視的應用程式記錄。
*.vault.usgovcloudapi.net | 管理 Azure Key Vault 中的祕密。
aka.ms/* | 允許存取 aka 連結。 用於 Azure Migrate 設備更新。
download.microsoft.com/download | 允許來自 Microsoft 下載的下載項目。
*.servicebus.usgovcloudapi.net  | 設備與 Azure Migrate 服務之間的通訊。
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | 連線到 Azure Migrate 服務 URL。
*.hypervrecoverymanager.windowsazure.us | **用於 VMware 無代理程式移轉**<br/><br/> 連線到 Azure Migrate 服務 URL。
*.blob.core.usgovcloudapi.net  |  **用於 VMware 無代理程式移轉**<br/><br/>將資料上傳至儲存體以進行移轉。
*.applicationinsights.us | 上傳用於內部監視的應用程式記錄。





## <a name="collected-data---vmware"></a>已收集的資料 - VMware

設備會收集中繼資料、效能資料和相依性分析資料 (如果使用無代理程式[相依性分析](concepts-dependency-visualization.md))。

### <a name="metadata"></a>中繼資料

Azure Migrate 設備所探索到的中繼資料，可協助您了解機器和應用程式是否已準備好移轉至 Azure、適當的機器和應用程式大小、規劃成本，以及分析應用程式相依性。 Microsoft 不會在任何授權合規性稽核中使用此資料。

以下是設備收集並傳送至 Azure 的 VMware VM 中繼資料完整清單。

**資料** | **計數器**
--- | --- 
**機器詳細資料** | 
VM 識別碼 | vm.Config.InstanceUuid 
VM 名稱 | vm.Config.Name
vCenter Server 識別碼 | VMwareClient.Instance.Uuid
VM 描述 | vm.Summary.Config.Annotation
授權產品名稱 | vm.Client.ServiceContent.About.LicenseProductName
作業系統類型 | vm.SummaryConfig.GuestFullName
開機類型 | vm.Config.Firmware
核心數目 | vm.Config.Hardware.NumCPU
記憶體 (MB) | vm.Config.Hardware.MemoryMB
磁碟數量 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
磁碟大小清單 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
網路介面卡清單 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU 使用率 | cpu.usage.average
記憶體使用量 |mem.usage.average
**每個磁碟詳細資料** | 
磁碟機碼值 | disk.Key
Dikunit 數字 | disk.UnitNumber
磁碟控制器機碼值 | disk.ControllerKey.Value
已佈建的 GB 數 | virtualDisk.DeviceInfo.Summary
磁碟名稱 | 使用 disk.UnitNumber、disk.Key 和 disk.ControllerKey.Value 來產生這個值
每秒的讀取作業數 | virtualDisk.numberReadAveraged.average
每秒的寫入作業數 | virtualDisk.numberReadAveraged.average
讀取輸送量 (MB 每秒) | virtualDisk.read.average
寫入輸送量 (MB 每秒) | virtualDisk.write.average
**每個 NIC 的詳細資料** | 
網路介面卡名稱 | nic.Key
MAC 位址 | ((VirtualEthernetCard)nic).MacAddress
IPv4 位址 | vm.Guest.Net
IPv6 位址 | vm.Guest.Net
讀取輸送量 (MB 每秒) | net.received.average
寫入輸送量 (MB 每秒) | net.transmitted.average
**清查路徑詳細資料** | 
名稱 | container.GetType().Name
子物件的類型 | container.ChildType
參考詳細資料 | container.MoRef
父系詳細資料 | Container.Parent
每個 VM 的資料夾詳細資料 | ((Folder)container).ChildEntity.Type
每個 VM 的資料中心詳細資料 | ((Datacenter)container).VmFolder
每個主機資料夾的資料中心詳細資料 | ((Datacenter)container).HostFolder
每個主機的叢集詳細資料 | ((ClusterComputeResource)container).Host
每個 VM 的主機詳細資料 | ((HostSystem)container).VM

### <a name="performance-data"></a>效能資料


以下是設備收集並傳送至 Azure 的 VMware VM 效能資料。

**Data** | **計數器** | **評量影響**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 建議的 VM 大小/成本
記憶體使用量 | mem.usage.average | 建議的 VM 大小/成本
磁碟讀取輸送量 (MB 每秒) | virtualDisk.read.average | 磁碟大小、儲存體成本、VM 大小的計算
磁碟寫入輸送量 (MB 每秒) | virtualDisk.write.average | 磁碟大小、儲存體成本、VM 大小的計算
每秒的磁碟讀取作業數 | virtualDisk.numberReadAveraged.average | 磁碟大小、儲存體成本、VM 大小的計算
每秒的磁碟寫入作業數 | virtualDisk.numberReadAveraged.average  | 磁碟大小、儲存體成本、VM 大小的計算
NIC 讀取輸送量 (MB 每秒) | net.received.average | VM 大小的計算
NIC 寫入輸送量 (MB 每秒) | net.transmitted.average  |VM 大小的計算


### <a name="installed-apps-metadata"></a>已安裝的應用程式中繼資料

應用程式探索會收集已安裝的應用程式和作業系統資料。

#### <a name="windows-vm-apps-data"></a>Windows VM 應用程式資料

以下是設備從每個啟用應用程式探索的 VM 收集到的已安裝應用程式資料。 此資料會傳送至 Azure。

**Data** | **登錄位置** | **索引鍵**
--- | --- | ---
應用程式名稱  | HKLM： \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM： \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
版本  | HKLM： \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM： \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
提供者  | HKLM： \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM： \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-vm-features-data"></a>Windows VM 功能資料

以下是設備從每個啟用應用程式探索的 VM 收集的功能資料。 此資料會傳送至 Azure。

**Data**  | **PowerShell Cmdlet** | **屬性**
--- | --- | ---
名稱  | Get-help  | 名稱
特徵類型 | Get-help  | FeatureType
父系  | Get-help  | 父系

#### <a name="windows-vm-sql-server-metadata"></a>Windows VM SQL Server 中繼資料

以下是設備從執行 Microsoft SQL server 以進行應用程式探索的 Vm 收集的 SQL server 中繼資料。 此資料會傳送至 Azure。

**Data**  | **登錄位置**  | **索引鍵**
--- | --- | ---
名稱  | HKLM： \ SOFTWARE\Microsoft\Microsoft SQL 伺服器 \ 實例 Names\SQL  | installedInstance
版本  | HKLM： \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | 版本 
Service Pack  | HKLM： \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | SP
版本  | HKLM： \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | 版本 

#### <a name="windows-vm-operating-system-data"></a>Windows VM 作業系統資料

以下是設備收集為應用程式探索啟用的每個 VM 的作業系統資料。 此資料會傳送至 Azure。

資料  | WMI 類別  | WMI 類別屬性
--- | --- | ---
名稱  | Win32_operatingsystem  | Caption
版本  | Win32_operatingsystem  | 版本
架構  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-vm-apps-data"></a>Linux VM 應用程式資料

以下是設備從每個啟用應用程式探索的 VM 收集到的已安裝應用程式資料。 根據 VM 的作業系統，會執行一或多個命令。 此資料會傳送至 Azure。

資料  | Command
--- | --- 
名稱 | rpm、dpkg-查詢、貼齊
版本 | rpm、dpkg-查詢、貼齊
提供者 | rpm、dpkg-查詢、貼齊

#### <a name="linux-vm-operating-system-data"></a>Linux VM 作業系統資料

以下是設備收集為應用程式探索啟用的每個 VM 的作業系統資料。 此資料會傳送至 Azure。

**Data**  | **命令** 
--- | --- | ---
名稱 <br/> version | 從下列一或多個檔案收集：<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
架構 | uname


### <a name="app-dependencies-metadata"></a>應用程式相依性中繼資料

無代理程式相依性分析會收集連線和處理序資料。

#### <a name="windows-vm-app-dependencies-data"></a>Windows VM 應用程式相依性資料

以下是設備針對無代理程式相依性分析從每個 VM 收集到的連線資料。 此資料會傳送至 Azure。

**Data** | **使用的命令** 
--- | --- 
本機連接埠 | netstat
本機 IP 位址 | netstat
遠端連接埠 | netstat
遠端 IP 位址 | netstat
TCP 連線狀態 | netstat
處理序識別碼 | netstat
使用中連線的數目 | netstat


以下是設備針對無代理程式相依性分析從每個 VM 收集到的處理序資料。 此資料會傳送至 Azure。

**Data** | **WMI 類別** | **WMI 類別屬性**
--- | --- | ---
程序名稱 | Win32_Process | ExecutablePath
處理序引數 | Win32_Process | CommandLine
應用程式名稱 | Win32_Process | ExecutablePath 屬性的 VersionInfo.ProductName 參數

#### <a name="linux-vm-app-dependencies-data"></a>Linux VM 應用程式相依性資料

以下是設備針對無代理程式相依性分析從每個 Linux VM 收集到的連線和處理序資料。 此資料會傳送至 Azure。

**Data** | **使用的命令** 
--- | ---
本機連接埠 | netstat 
本機 IP 位址 | netstat 
遠端連接埠 | netstat 
遠端 IP 位址 | netstat 
TCP 連線狀態 | netstat 
使用中連線的數目 | netstat
處理序識別碼  | netstat 
程序名稱 | ps
處理序引數 | ps
應用程式名稱 | dpkg 或 rpm



## <a name="collected-data---hyper-v"></a>已收集的資料 - Hyper-V

設備會收集中繼資料、效能資料和相依性分析資料 (如果使用無代理程式[相依性分析](concepts-dependency-visualization.md))。

### <a name="metadata"></a>中繼資料
Azure Migrate 設備所探索到的中繼資料，可協助您了解機器和應用程式是否已準備好移轉至 Azure、適當的機器和應用程式大小、規劃成本，以及分析應用程式相依性。 Microsoft 不會在任何授權合規性稽核中使用此資料。

以下是設備收集並傳送至 Azure 的 Hyper-V VM 中繼資料完整清單。

**資料** | **WMI 類別** | **WMI 類別屬性**
--- | --- | ---
**機器詳細資料** | 
BIOS _ Msvm_BIOSElement 的序號 | BIOSSerialNumber
VM 類型 (Gen 1 或 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM 顯示名稱 | Msvm_VirtualSystemSettingData | ElementName
VM 版本 | Msvm_ProcessorSettingData | VirtualQuantity
記憶體 (位元組) | Msvm_MemorySettingData | VirtualQuantity
VM 可以取用的記憶體上限 | Msvm_MemorySettingData | 限制
已啟用動態記憶體 | Msvm_MemorySettingData | DynamicMemoryEnabled
作業系統名稱/版本/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems 名稱資料
VM 電源狀態 | Msvm_ComputerSystem | EnabledState
**每個磁碟詳細資料** | 
磁碟識別碼 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
虛擬硬碟類型 | Msvm_VirtualHardDiskSettingData | 類型
虛擬硬碟大小 | Msvm_VirtualHardDiskSettingData | MaxInternalSize
虛擬硬碟父系 | Msvm_VirtualHardDiskSettingData | ParentPath
**每個 NIC 的詳細資料** | 
IP 位址 (綜合 NIC) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP 已啟用 (綜合 NIC) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC 識別碼 (綜合 NIC) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 位址 (綜合 NIC) | Msvm_SyntheticEthernetPortSettingData | 位址
NIC 識別碼 (傳統 NIC) | MsvmEmulatedEthernetPortSetting Data | InstanceID
NIC MAC 識別碼 (傳統 NIC) | MsvmEmulatedEthernetPortSetting Data | 位址

### <a name="performance-data"></a>效能資料

以下是設備收集並傳送至 Azure 的 Hyper VM 效能資料。

**效能計數器類別** | **計數器** | **評量影響**
--- | --- | ---
Hyper-V Hypervisor 虛擬處理器 | % 客體執行階段 | 建議的 VM 大小/成本
Hyper-V 動態記憶體 VM | 目前壓力 (%)<br/> 客體可見的實體記憶體 (MB) | 建議的 VM 大小/成本
Hyper-V 虛擬存放裝置 | 讀取位元組數/秒 | 磁碟大小、儲存體成本、VM 大小的計算
Hyper-V 虛擬存放裝置 | 寫入位元組數/秒 | 磁碟大小、儲存體成本、VM 大小的計算
Hyper-V 虛擬網路介面卡 | 接收位元組數/秒 | VM 大小的計算
Hyper-V 虛擬網路介面卡 | 傳送位元組數/秒 | VM 大小的計算

- CPU 使用率是所有連結至 VM 的虛擬處理器所有使用量的總和。
- 記憶體使用率為 (目前壓力 * 客體可見的實體記憶體) / 100。
- 磁碟和網路使用值是從列出的 Hyper-V 效能計數器收集而來。


## <a name="collected-data---physical"></a>已收集的資料 - 實體

設備會收集中繼資料、效能資料和相依性分析資料 (如果使用無代理程式[相依性分析](concepts-dependency-visualization.md))。

### <a name="windows-metadata"></a>Windows 中繼資料

Azure Migrate 設備所探索到的中繼資料，可協助您了解機器和應用程式是否已準備好移轉至 Azure、適當的機器和應用程式大小、規劃成本，以及分析應用程式相依性。 Microsoft 不會在任何授權合規性稽核中使用此資料。

以下是設備收集並傳送至 Azure 的 Windows 伺服器中繼資料完整清單。

**資料** | **WMI 類別** | **WMI 類別屬性**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domain、Name、PartOfDomain
處理器核心計數 | Win32_PRocessor | NumberOfCores
已配置的記憶體 | Win32_ComputerSystem | TotalPhysicalMemory
BIOS 序號 | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS GUID | Win32_ComputerSystemProduct | UUID
開機類型 | Win32_DiskPartition | 針對 EFI/BIOS 使用 Type = **GPT:System** 來檢查分割區
OS 名稱 | Win32_OperatingSystem | Caption
作業系統版本 |Win32_OperatingSystem | 版本
OS 架構 | Win32_OperatingSystem | OSArchitecture
磁碟計數 | Win32_DiskDrive | Model、Size、DeviceID、MediaType、Name
磁碟大小 | Win32_DiskDrive | 大小
NIC 清單 | Win32_NetworkAdapterConfiguration | Description、Index
NIC IP 位址 | Win32_NetworkAdapterConfiguration | IPAddress
NIC MAC 位址 | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Linux 中繼資料

以下是設備收集並傳送至 Azure 的 Linux 伺服器中繼資料完整清單。

**資料** | **LINUX** 
--- | --- 
FQDN | cat /proc/sys/kernel/hostname, hostname -f
處理器核心計數 |  /proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
已配置的記憶體 | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
BIOS 序號 | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
BIOS GUID | cat /sys/class/dmi/id/product_uuid
開機類型 | [ -d /sys/firmware/efi ] && echo EFI \|\| echo BIOS
OS 名稱/版本 | 我們會針對 OS 版本和名稱存取這些檔案：<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
OS 架構 | Uname -m
磁碟計數 | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
開機磁碟 | df /boot \| sed -n 2p \| awk '{print $1}'
磁碟大小 | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
NIC 清單 | ip -o -4 addr show \| awk '{print $2}'
NIC IP 位址 | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
NIC MAC 位址 | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Windows 效能資料

以下是設備收集並傳送至 Azure 的 Windows 伺服器效能資料。

**Data** | **WMI 類別** | **WMI 類別屬性**
--- | --- | ---
CPU 使用率 | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
記憶體使用量 | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
NIC 計數 | Win32_PerfFormattedData_Tcpip_NetworkInterface | 取得網路裝置計數。
每個 NIC 收到的資料 | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
每個 NIC 傳輸的資料 | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
磁碟計數 | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | 磁碟計數
磁碟詳細資料 | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec、DiskWriteBytesPerSec、DiskReadsPerSec、DiskReadBytesPerSec。

### <a name="linux-performance-data"></a>Linux 效能資料

以下是設備收集並傳送至 Azure 的 Linux 伺服器效能資料。

**Data** | **Linux** 
--- | --- 
CPU 使用率 | cat /proc/stat/| grep 'cpu' /proc/stat
記憶體使用量 | free \| grep Mem \| awk '{print $3/$2 * 100.0}'
NIC 計數 | lshw -class network \| grep eth[0-60] \| wc -l
每個 NIC 收到的資料 | cat /sys/class/net/eth$nic/statistics/rx_bytes
每個 NIC 傳輸的資料 | cat /sys/class/net/eth$nic/statistics/tx_bytes
磁碟計數 | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
磁碟詳細資料 | cat /proc/diskstats


## <a name="appliance-upgrades"></a>設備升級

設備會隨著應用在裝置上執行的 Azure Migrate 代理程式更新而升級。 這會自動發生，因為在設備上，根據預設已啟用自動更新。 您可以變更此預設設定，以手動更新設備服務。

### <a name="turn-off-auto-update"></a>關閉自動更新

1. 在執行設備的機器上，開啟 [登錄編輯程式]。
2. 瀏覽至 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**。
3. 若要關閉自動更新，請建立包含 DWORD 值為 0 的 **AutoUpdate** 登錄機碼。

    ![設定登錄機碼](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>開啟自動更新

您可以使用下列其中一種方法來開啟自動更新：

- 將 AutoUpdate 登錄機碼從 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance 刪除。
- 從 [**設定必要條件**] 面板中的 [最新的更新檢查] 按一下 [**查看設備服務**]，開啟自動更新。

若要刪除登錄機碼：

1. 在執行設備的機器上，開啟 [登錄編輯程式]。
2. 瀏覽至 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**。
3. 刪除先前建立用來關閉自動更新的登錄機碼 **AutoUpdate**。

若要在探索完成後從設備組態管理員開啟：

1. 在設備設定管理員上，移至 **設定必要條件** 面板
2. 在最新的更新檢查中，按一下 [ **查看設備服務** ]，然後按一下連結以開啟自動更新。

    ![開啟自動更新](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>檢查設備服務版本

您可以使用這些方法的其中一種來檢查設備服務版本：

- 在 [設備設定管理員] 中，移至 [ **設定必要條件** ] 面板。
- 在設備機器上，於 [控制台]  > [程式和功能] 中。

若要簽入設備設定管理員：

1. 在設備設定管理員上，移至 **設定必要條件** 面板
2. 在最新的更新檢查中，按一下 [ **查看設備服務**]。

    ![檢查版本](./media/migrate-appliance/versions.png)

在 [控制台] 中檢查：

1. 在設備上，按一下 [開始] > [控制台] > [程式和功能]
2. 檢查清單中的設備服務版本。

    ![在 [控制台] 中檢查版本](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>手動更新舊版本

如果您執行任何元件的舊版，您必須將服務解除安裝，然後手動更新為最新版本。

1. 若要檢查最新設備服務版本，請[下載](https://aka.ms/latestapplianceservices) LatestComponents.json 檔案。
2.    下載之後，在 [記事本] 中開啟 LatestComponents.json 檔案。
3. 在檔案中尋找最新服務版本，以及其下載連結。 例如：

    "Name":"ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi", "Version":"6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.    使用檔案中的下載連結，下載過時服務的最新版本。
5. 下載之後，請在系統管理員命令視窗中執行下列命令，以確認所下載 MSI 的完整性。

    例如 ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ```：C:\>CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. 檢查命令輸出是否符合檔案中服務的雜湊值項目 (例如，上述的 MD5 雜湊值)。
6. 現在，執行 MSI 以安裝服務。 這是無訊息安裝，而且安裝視窗會在完成後關閉。
7. 安裝完成之後，請在 [控制台] > [程式和功能] 中檢查服務的版本。 服務版本現在應該會升級為 JSON 檔案中顯示的最新版本。



## <a name="next-steps"></a>後續步驟

- [了解如何](how-to-set-up-appliance-vmware.md)設定 VMware 的設備。
- [了解如何](how-to-set-up-appliance-hyper-v.md)設定 Hyper-V 的設備。
- [了解如何](how-to-set-up-appliance-physical.md)設定實體伺服器的設備。

