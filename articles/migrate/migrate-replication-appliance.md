---
title: Azure Migrate 複寫設備
description: 瞭解以代理程式為基礎的 VMWare 遷移的 Azure Migrate 複寫設備。
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 85641f514fc4367f02901eb1dd394cfa204c3ec4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535208"
---
# <a name="replication-appliance"></a>複寫設備

本文說明使用以代理程式為基礎的遷移，將 VMware Vm、實體機器和私人/公用雲端 Vm 遷移至 Azure 時， [Azure Migrate：伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具所使用的複寫設備。 


## <a name="overview"></a>總覽

當您設定以代理程式為基礎的 VMware Vm 或實體伺服器的遷移時，會部署複寫應用裝置。 它會部署為單一內部部署機器，以作為 VMware VM 或實體伺服器。 它會執行：

- 複寫**設備**：複寫設備會協調內部部署 VMware vm 和實體伺服器複寫至 Azure 的通訊及管理資料複寫。
- **進程伺服器**：依預設會安裝在複寫應用裝置上的進程伺服器，並執行下列動作：
    - 複寫**閘道**：它會作為複寫閘道。 它會從啟用複寫的機器接收復寫資料。 它會使用快取、壓縮和加密來優化複寫資料，並將其傳送至 Azure。
    - **代理程式安裝程式**：執行行動服務的推入安裝。 這項服務必須安裝在您想要複寫以進行遷移的每部內部部署機器上執行。

## <a name="appliance-deployment"></a>設備部署

**用途** | **詳細資料**
--- |  ---
**以 VMware VM 代理程式為基礎的遷移** | 您可以從 Azure Migrate 中樞下載 OVA 範本，並匯入至 vCenter Server 以建立設備 VM。
**以實體機器代理程式為基礎的遷移** | 如果您沒有 VMware 基礎結構，或如果您無法使用 OVA 範本建立 VMware VM，請從 Azure Migrate 中樞下載軟體安裝程式，然後執行它來設定設備機器。

> [!NOTE]
> 如果您是在 Azure Government 中部署，請使用安裝檔案來部署複寫應用裝置。

## <a name="appliance-requirements"></a>設備需求

當您使用 Azure Migrate 中樞提供的 OVA 範本來設定複寫設備時，設備會執行 Windows Server 2016，並符合支援需求。 如果您在實體伺服器上手動設定複寫設備，請確定它符合需求。

**元件** | **需求**
--- | ---
 | **VMware VM 應用裝置**
PowerCLI | 如果複寫設備在 VMware VM 上執行，則應該安裝[PowerCLI 6.0 版](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)。
NIC 類型 | VMXNET3 （如果設備是 VMware VM）
 | **硬體設定**
CPU 核心 | 8
RAM | 16 GB
磁碟數量 | 三： OS 磁片、進程伺服器快取磁片和保留磁片磁碟機。
可用磁碟空間（快取） | 600 GB
可用磁碟空間 (保留磁碟) | 600 GB
**軟體設定** |
作業系統 | Windows Server 2016 或 Windows Server 2012 R2
授權 | 設備隨附 Windows Server 2016 評估授權，其有效期為180天。<br/><br/> 如果評估期接近到期日，建議您下載並部署新的應用裝置，或啟用設備 VM 的作業系統授權。
作業系統地區設定 | 英文 (en-us)
TLS | 應啟用 TLS 1.2。
.NET Framework | 應該在電腦上安裝 .NET Framework 4.6 或更新版本（啟用強式密碼編譯）。
MySQL | MySQL 應該安裝在設備上。<br/> 應該安裝 MySQL。 您可以手動安裝，或 Site Recovery 可以在應用裝置部署期間進行安裝。
其他應用程式 | 不要在複寫設備上執行其他應用程式。
Windows Server 角色 | 請勿啟用這些角色： <br> - Active Directory Domain Services <br>- 網際網路資訊服務 <br> - Hyper-V
群組原則 | 請勿啟用這些群組原則： <br> - 防止存取命令提示字元。 <br> - 防止存取登錄編輯工具。 <br> - 檔案附件的信任邏輯。 <br> - 開啟指令碼執行。 <br> [深入了解](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 沒有預先存在的預設網站 <br> - 沒有預先存在的網站/應用程式接聽連接埠 443 <br>- 啟用[匿名驗證](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 啟用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定
**網路設定** |
IP 位址類型 | 靜態
連接埠 | 443 (控制通道協調流程)<br>9443 (資料傳輸)
NIC 類型 | VMXNET3

## <a name="mysql-installation"></a>MySQL 安裝 

MySQL 必須安裝在複寫設備機器上。 您可以使用其中一種方法來安裝它。

**方法** | **詳細資料**
--- | ---
手動下載並安裝 | 下載 MySQL 應用程式 & 將它放在 C:\Temp\ASRSetup 資料夾中，然後手動安裝。<br/> 當您設定應用裝置時，MySQL 會顯示為已安裝。
不需要線上下載 | 將 MySQL installer 應用程式放在資料夾 C:\temp\asrsetup 中。中 當您安裝設備並按一下以下載並安裝 MySQL 時，安裝程式將會使用您新增的安裝程式。
在 Azure Migrate 中下載並安裝 | 當您安裝設備，並會提示您提供 MySQL 時，請選取 [**下載並安裝**]。

## <a name="url-access"></a>URL 存取

複寫設備需要在 Azure 公用雲端中存取這些 Url。

**URL** | **詳細資料**
--- | ---
\*.backup.windowsazure.com | 用於所複寫資料的轉送和協調
\*.store.core.windows.net | 用於所複寫資料的轉送和協調
\*.blob.core.windows.net | 用於存取儲存體帳戶來儲存複寫的資料
\*.hypervrecoverymanager.windowsazure.com | 用於複寫管理作業和協調
https:\//management.azure.com | 用於複寫管理作業和協調
*.services.visualstudio.com | 用於遙測目的 (此為選擇性項目)
time.windows.com | 用於檢查系統時間與通用時間之間的時間同步處理。
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> HTTPs：\//login.live.com <br/> HTTPs：\//graph.windows.net <br/> https:\//login.windows.net <br/> HTTPs：\//www.live.com <br/> HTTPs：\//www.microsoft.com  | 設備安裝程式需要存取這些 Url。 其可供 Azure Active Directory 用於管理存取控制和身分識別
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | 以完成 MySQL 下載。 在幾個區域中，下載可能會重新導向至 CDN URL。 請確定如果有需要，也允許 CDN URL。


## <a name="azure-government-url-access"></a>Azure Government URL 存取

在 Azure Government 中，複寫設備需要存取這些 Url。

**URL** | **詳細資料**
--- | ---
\*.backup.windowsazure.us | 用於所複寫資料的轉送和協調
\*.store.core.windows.net | 用於所複寫資料的轉送和協調
\*.blob.core.windows.net | 用於存取儲存體帳戶來儲存複寫的資料
\*.hypervrecoverymanager.windowsazure.us | 用於複寫管理作業和協調
https:\//management.usgovcloudapi.net | 用於複寫管理作業和協調
*.services.visualstudio.com | 用於遙測目的 (此為選擇性項目)
time.nist.gov | 用於檢查系統時間與通用時間之間的時間同步處理。
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> HTTPs：\//login.live.com <br/> HTTPs：\//graph.windows.net <br/> https:\//login.windows.net <br/> HTTPs：\//www.live.com <br/> HTTPs：\//www.microsoft.com  | 具有 OVA 的設備設定需要存取這些 Url。 它們用於 Azure Active Directory 的存取控制和身分識別管理。
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | 以完成 MySQL 下載。 在幾個區域中，下載可能會重新導向至 CDN URL。 請確定如果有需要，也允許 CDN URL。

## <a name="port-access"></a>埠存取

**裝置** | **建立**
--- | ---
VM | 在 Vm 上執行的行動服務會與埠 HTTPS 443 輸入上的內部部署複寫設備（設定伺服器）進行通訊，以進行複寫管理。<br/><br/> VM 會透過輸入連接埠 HTTPS 9443 將複寫資料傳送至處理伺服器 (在設定伺服器電腦上執行)。 您可以修改此連接埠。
複寫設備 | 複寫設備會透過埠 HTTPS 443 輸出來協調與 Azure 的複寫。
處理序伺服器 | 進程伺服器會接收復寫資料、將其優化並加以加密，並透過埠443輸出將它傳送至 Azure 儲存體。<br/> 根據預設，進程伺服器會在複寫設備上執行。


## <a name="replication-process"></a>複寫程序

1. 當您啟用 VM 複寫時，首先會使用指定的複寫原則開始複寫至 Azure 儲存體。 
2. 流量會透過網際網路複寫到 Azure 儲存體的公用端點。 不支援從內部部署網站透過站對站虛擬私人網路 (VPN) 將流量複寫至 Azure。
3. 初始複寫完成後，就會開始進行差異複寫。 系統會記錄機器的追蹤變更。
4. 進行通訊的過程如下：
    - Vm 會與埠 HTTPS 443 輸入上的複寫設備進行通訊，以進行複寫管理。
    - 複寫設備會透過埠 HTTPS 443 輸出來協調與 Azure 的複寫。
    - Vm 會將複寫資料傳送至埠 HTTPS 9443 傳入的進程伺服器（在複寫應用裝置上執行）。 您可以修改此連接埠。
    - 進程伺服器會接收復寫資料、將其優化並加以加密，並透過埠443輸出將它傳送至 Azure 儲存體。
5. 複寫資料記錄會先在 Azure 中的快取儲存體帳戶中進行。 系統會處理這些記錄，並將資料儲存在 Azure 受控磁片中。

![架構](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>設備升級

設備會從 Azure Migrate 中樞手動升級。 我們建議您一律執行最新版本。

1. 在 Azure Migrate > 伺服器 > Azure Migrate：伺服器評估、基礎結構伺服器，請按一下 [設定**伺服器**]。
2. 在 [設定**伺服器**] 中，當有新版本的複寫設備可供使用時，會在**代理程式版本**中出現連結。 
3. 將安裝程式下載至複寫設備機器，並安裝升級。 安裝程式會偵測設備上目前正在執行的版本。
 
## <a name="next-steps"></a>後續步驟

- [瞭解如何](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance)為以代理程式為基礎的 VMware VM 遷移設定複寫設備。
- [瞭解如何](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)設定實體伺服器的複寫設備。
