---
title: Azure Migrate 複寫設備
description: 瞭解基於代理的 VMWare 遷移的 Azure 遷移複製應用。
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 4521fce6310b319d155a2f0c418cd934be7e2cb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245859"
---
# <a name="replication-appliance"></a>複製設備

本文介紹[Azure 遷移：伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具使用基於代理的遷移將 VMware VM、物理電腦和私有/公共雲 VM 遷移到 Azure 時使用的複製設備。 


## <a name="overview"></a>總覽

當您設置基於代理的 VMware VM 或物理伺服器的遷移時，將部署複製設備。 它作為單個本地電腦（VMware VM 或物理伺服器）進行部署。 它運行：

- **複製設備**：複製設備協調本地 VMware VM 和複製到 Azure 的物理伺服器的通信並管理資料複製。
- **進程伺服器**：進程伺服器，預設情況下安裝在複製設備上，並執行以下操作：
    - **複製閘道**：它充當複製閘道。 它從啟用用於複製的電腦接收復制資料。 它使用緩存、壓縮和加密優化複製資料，並將其發送到 Azure。
    - **代理安裝程式**：執行移動服務的推送安裝。 此服務必須在要複製的每個本地電腦上安裝和運行，以便進行遷移。

## <a name="appliance-deployment"></a>設備部署

**用於** | **詳細資料**
--- |  ---
基於 VMware VM 代理的遷移 | 從 Azure 遷移中心下載 OVA 範本，然後導入到 vCenter 伺服器以創建設備 VM。
基於物理電腦代理的遷移 | 如果您沒有 VMware 基礎結構，或者無法使用 OVA 範本創建 VMware VM，則從 Azure 遷移中心下載軟體安裝程式，然後運行它以設置設備電腦。

## <a name="appliance-requirements"></a>設備要求

使用 Azure 遷移中心提供的 OVA 範本設置複製設備時，該設備將運行 Windows Server 2016 並符合支援要求。 如果在物理伺服器上手動設置複製設備，請確保它符合要求。

**元件** | **要求**
--- | ---
 | **VMware VM 設備**
PowerCLI | 如果複製設備在 VMware VM 上運行，則應安裝[PowerCLI 版本 6.0。](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)
NIC 類型 | VMXNET3（如果設備是 VMware VM）
 | **硬體設置**
CPU 核心 | 8
RAM | 16 GB
磁碟數量 | 三：作業系統磁片、進程伺服器緩存磁片和保留磁碟機。
可用磁碟空間（緩存） | 600 GB
可用磁碟空間 (保留磁碟) | 600 GB
**軟體設置** |
作業系統 | Windows Server 2016 或 Windows Server 2012 R2
授權 | 該設備附帶 Windows Server 2016 評估許可證，有效期為 180 天。<br/><br/> 如果評估期接近到期，我們建議您下載並部署新設備，或者啟動設備 VM 的作業系統許可證。
作業系統地區設定 | 英文 (en-us)
TLS | 應啟用 TLS 1.2。
.NET Framework | .NET 框架 4.6 或更高版本應安裝在電腦上（啟用了增強式加密功能）。
MySQL | MySQL 應安裝在設備上。<br/> 應該安裝 MySQL。 您可以手動安裝，或者網站恢復可以在設備部署期間安裝它。
其他應用程式 | 不要在複製設備上運行其他應用。
Windows Server 角色 | 請勿啟用這些角色： <br> - Active Directory Domain Services <br>- 網際網路資訊服務 <br> - Hyper-V
群組原則 | 請勿啟用這些群組原則： <br> - 防止存取命令提示字元。 <br> - 防止存取登錄編輯工具。 <br> - 檔案附件的信任邏輯。 <br> - 開啟指令碼執行。 <br> [深入了解](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 沒有預先存在的預設網站 <br> - 沒有預先存在的網站/應用程式接聽連接埠 443 <br>- 啟用[匿名驗證](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 啟用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定
**網路設置** |
IP 位址類型 | 靜態
連接埠 | 443 (控制通道協調流程)<br>9443 (資料傳輸)
NIC 類型 | VMXNET3

## <a name="mysql-installation"></a>MySQL 安裝 

必須在複製設備電腦上安裝 MySQL。 可以使用這些方法之一安裝它。

**方法** | **詳細資料**
--- | ---
手動下載和安裝 | 下載 MySQL 應用程式&將其放在資料夾 C：\Temp_ASRSetup 中，然後手動安裝。<br/> 設置設備時，MySQL 將顯示為已安裝。
無需線上下載 | 將 MySQL 安裝程式應用程式放在資料夾 C：\Temp_ASRSetup 中。 安裝設備並按一下以下載並安裝 MySQL 時，安裝程式將使用您添加的安裝程式。
在 Azure 遷移中下載並安裝 | 當您安裝設備並提示使用 MySQL 時，請選擇 **"下載並安裝**"。

## <a name="url-access"></a>URL 存取

複製設備需要訪問這些 URL。

**Url** | **詳細資料**
--- | ---
\*.backup.windowsazure.com | 用於所複寫資料的轉送和協調
\*.store.core.windows.net | 用於所複寫資料的轉送和協調
\*.blob.core.windows.net | 用於存取儲存體帳戶來儲存複寫的資料
\*.hypervrecoverymanager.windowsazure.com | 用於複寫管理作業和協調
https:\//management.azure.com | 用於複寫管理作業和協調
*.services.visualstudio.com | 用於遙測目的 (此為選擇性項目)
time.nist.gov | 用於檢查系統時間與通用時間之間的時間同步處理。
time.windows.com | 用於檢查系統時間與通用時間之間的時間同步處理。
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> HTTPs：\//login.live.com <br/> HTTPs：\//graph.windows.net <br/> https:\//login.windows.net <br/> HTTPs：\//www.live.com <br/> HTTPs：\//www.microsoft.com  | OVF 設置需要訪問這些 URL。 其可供 Azure Active Directory 用於管理存取控制和身分識別
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | 完成 MySQL 下載

## <a name="port-access"></a>埠訪問

**裝置** | **連接**
--- | ---
VM | 在 VM 上運行的移動服務與埠 HTTPS 443 入站上的本地複製設備（佈建服務器）進行通信，以便進行複製管理。<br/><br/> VM 會透過輸入連接埠 HTTPS 9443 將複寫資料傳送至處理伺服器 (在設定伺服器電腦上執行)。 您可以修改此連接埠。
複製設備 | 複製設備通過埠 HTTPS 443 出站協調使用 Azure 進行複製。
處理序伺服器 | 進程伺服器接收復制資料、優化和加密資料，然後通過埠 443 出站將其發送到 Azure 存儲。<br/> 預設情況下，進程伺服器在複製設備上運行。


## <a name="replication-process"></a>複寫程序

1. 當您啟用 VM 複寫時，首先會使用指定的複寫原則開始複寫至 Azure 儲存體。 
2. 流量會透過網際網路複寫到 Azure 儲存體的公用端點。 不支援從內部部署網站透過站對站虛擬私人網路 (VPN) 將流量複寫至 Azure。
3. 初始複寫完成後，就會開始進行差異複寫。 記錄電腦的跟蹤更改。
4. 進行通訊的過程如下：
    - VM 與埠 HTTPS 443 入站上的複製設備進行通信，以便進行複製管理。
    - 複製設備通過埠 HTTPS 443 出站協調使用 Azure 進行複製。
    - VM 將複製資料發送到埠 HTTPS 9443 入站上的進程伺服器（在複製設備上運行）。 您可以修改此連接埠。
    - 處理伺服器會透過輸出連接埠 443 接收複寫資料、將其最佳化並加密，然後傳送至 Azure 儲存體。
5. 複製資料首先記錄在 Azure 中的緩存存儲帳戶中。 這些日誌將處理，並將資料存儲在 Azure 託管磁片中。

![架構](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>設備升級

設備從 Azure 遷移中心手動升級。 我們建議您始終運行最新版本。

1. 在 Azure 遷移>伺服器> Azure 遷移：伺服器評估、基礎結構伺服器，按一下 **"佈建服務器**"。
2. 在**佈建服務器**中，當複製設備的新版本可用時，**代理版本中**將顯示一個連結。 
3. 將安裝程式下載到複製設備電腦，然後安裝升級。 安裝程式檢測到設備上運行的版本電流。
 
## <a name="next-steps"></a>後續步驟

- [瞭解如何](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance)為基於代理的 VMware VM 遷移設置複製設備。
- [瞭解如何](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)為物理伺服器設置複製設備。
