---
title: MABS &系統中心 DPM 支援矩陣
description: 本文總結了使用 Microsoft Azure 備份伺服器 （MABS） 或系統中心 DPM 備份本地和 Azure VM 資源時 Azure 備份支援。
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6664f7b226b75b364fd1c83f2abc56b5a275eff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582648"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>支援使用 Microsoft Azure 備份伺服器或系統中心 DPM 進行備份

可以使用 Azure[備份服務](backup-overview.md)備份本地電腦和工作負荷以及 Azure 虛擬機器 （VM）。 本文總結了使用 Microsoft Azure 備份伺服器 （MABS） 或系統中心資料保護管理器 （DPM） 和 Azure 備份來備份電腦的支援設置和限制。

## <a name="about-dpmmabs"></a>關於 DPM/MABS

[系統中心 DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807)是一種企業解決方案，用於配置、促進和管理企業電腦和資料的備份和恢復。 它是 [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) 產品套件的一部分。

MABS 是一種伺服器產品，可用於備份在其上運行的本地物理伺服器、VM 和應用。

MABS 基於系統中心 DPM，提供類似的功能，有一些區別：

- MABS 不需要 System Center 授權即可執行。
- 對於 MABS 和 DPM，Azure 提供長期備份存儲。 此外，DPM 可讓您將資料備份在磁帶上進行長期儲存。 MABS 未提供這項功能。
- 您可以使用輔助 DPM 服務器備份主 DPM 服務器。 次要伺服器會保護主要伺服器資料庫，和儲存在主要伺服器上的資料來源複本。 當主要伺服器失敗時，次要伺服器將可繼續保護主要伺服器所保護的工作負載，直到主要伺服器恢復可用性為止。  MABS 未提供這項功能。

您從[微軟下載中心](https://www.microsoft.com/download/details.aspx?id=57520)下載 MABS。 它可以在本地運行，也可以在 Azure VM 上運行。

DPM 和 MABS 皆提供備份多種不同應用程式和伺服器與用戶端作業系統的支援。 它們適用於多種備份案例：

- 您可以使用系統狀態或裸機備份在機器層級進行備份。
- 您可以備份特定磁碟區、共用、資料夾和檔案。
- 您可以使用優化的應用感知設置來備份特定應用。

## <a name="dpmmabs-backup"></a>DPM/MABS 備份

使用 DPM/MABS 和 Azure 備份的備份工作方式如下：

1. DPM/MABS 保護代理安裝在要備份的每台電腦上。
1. 機器和應用程式已備份到 DPM/MABS 上的本機存放區。
1. 在 DPM 伺服器/MABS 上安裝 Microsoft Azure 復原服務 (MARS) 代理程式。
1. MARS 代理通過使用 Azure 備份將 DPM/MABS 磁片備份到 Azure 中的備份恢復服務保存庫。

其他資訊：

- [深入了解](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS 架構。
- 查看 MARS 代理[支援的內容](backup-support-matrix-mars-agent.md)。

## <a name="supported-scenarios"></a>支援的案例

**案例** | **代理** | **位置**
--- | --- | ---
**備份內部部署機器/工作負載** | DPM/MABS 保護代理在要備份的電腦上運行。<br/><br/> DPM/MABS 伺服器上的 MARS 代理。<br/> 啟用這項功能所需的 Microsoft Azure 復原服務代理程式或 Azure 備份代理程式的最低版本是 2.0.8719.0。  | DPM/MABS 必須在本地運行。

## <a name="supported-deployments"></a>支援的部署

DPM/MABS 可以部署如下表中的總結。

**部署** | **支援** | **詳細資料**
--- | --- | ---
**部署於內部部署** | 實體伺服器<br/><br/>Hyper-V VM<br/><br/> VMware VM | 如果 DPM/MABS 作為 VMware VM 安裝，則它僅備份在這些 VM 上運行的 VMware VM 和工作負載。
**部署為 Azure Stack VM** | 僅限 MABS | DPM 無法用來備份 Azure Stack VM。
**部署為 Azure VM** | 保護在這些 VM 上運行的 Azure VM 和工作負荷 | 在 Azure 中運行的 DPM/MABS 無法備份本地電腦。

## <a name="supported-mabs-and-dpm-operating-systems"></a>支援的 MABS 和 DPM 作業系統

Azure 備份可以備份運行以下任何作業系統的 DPM/MABS 實例。 作業系統應執行最新的服務套件和更新。

**案例** | **DPM/MABS**
--- | ---
**Azure VM 上的 MABS** |  Windows 2016 資料中心。<br/><br/> 視窗 2019 資料中心。<br/><br/> 我們建議您從市場的圖像開始。<br/><br/> 至少Standard_A4_v2，具有四個內核和 8 GB RAM。
**Azure VM 上的 DPM** | System Center 2012 R2 (含 Update 3 或更新版本)。<br/><br/> 符合 [System Center 需求](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)的 Windows 作業系統。<br/><br/> 我們建議您從市場的圖像開始。<br/><br/> 至少Standard_A4_v2，具有四個內核和 8 GB RAM。
**內部部署 MABS** |  MABS v3 及更高版本：Windows 伺服器 2016 或 Windows 伺服器 2019
**內部部署 DPM** | 物理伺服器/Hyper-V VM：系統中心 2012 SP1 或更高版本。<br/><br/> VMware VM：系統中心 2012 R2，更新 5 或更高版本。

>[!NOTE]
>Windows 伺服器核心或 Microsoft Hyper-V 伺服器不支援安裝 Azure 備份伺服器。

## <a name="management-support"></a>管理支援

**問題** | **詳細資料**
--- | ---
**安裝** | 在一台專用電腦上安裝 DPM/MABS。<br/><br/> 不要在網域控制站、安裝應用程式伺服器角色的電腦上、運行 Microsoft Exchange 伺服器或系統中心操作管理器的電腦上安裝 DPM/MABS，或在叢集節點上安裝 DABS。<br/><br/> [查看所有 DPM 系統要求](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)。
**域** | DPM/MABS 應加入域。 請先安裝 DPM/MABS，再將其加入網域。 不支援在部署之後將 DPM/MABS 移至新網域。
**儲存空間** | DPM 2016/MABS v2 及更高版本支援現代備份存儲 （MBS）。 MBS 不適用於 MABS v1。
**MABS 升級** | 您可以直接安裝 MABS v3，或是從 MABS v2 升級至 MABS v3。 [深入了解](backup-azure-microsoft-azure-backup.md#upgrade-mabs)。
**移動 MABS** | 使用 MABS 時，支援將 MABS 移至新伺服器，同時保留儲存體。<br/><br/> 伺服器必須與原始伺服器同名。 如果您想要保留相同的儲存體集區，並使用相同的 MABS 資料庫來儲存資料復原點，則不可變更名稱。<br/><br/> 您將需要 MABS 資料庫的備份，因為您將必須加以還原。

## <a name="mabs-support-on-azure-stack"></a>Azure Stack 上的 MABS 支援

您可以將 MABS 部署在 Azure Stack VM 上，以便從單一位置管理 Azure Stack VM 和工作負載的備份。

**元件** | **詳細資料**
--- | ---
**Azure Stack VM 上的 MABS** | 至少大小 A2。 我們建議您從 Azure 應用商店中的 Windows 伺服器 2012 R2 或 Windows 伺服器 2016 映射開始。<br/><br/> 不要在 MABS VM 上安裝任何其他內容。
**MABS 儲存體** | 對 MABS VM 使用單獨的存儲帳戶。 在 MABS 上運行的 MARS 代理需要臨時存儲緩存位置，並保存從雲還原的資料。
**MABS 儲存體集區** | MABS 存儲池的大小由附加到 MABS VM 的磁片的數量和大小決定。 每個 Azure Stack VM 大小皆有磁碟數目上限。 例如，A2 是四個磁碟。
**MABS 保留期** | 不要在本地 MABS 磁片上保留備份的資料超過五天。
**MABS 相應增加** | 若要相應增加您的部署，您可以增加 MABS VM 的大小。 例如，可以從 A 更改為 D 系列。<br/><br/> 還可以確保定期將資料隨備份卸載到 Azure。 如有必要，可以部署其他 MABS 伺服器。
**MABS 上的 .NET Framework** | MABS VM 需要 .NET 框架 3.3 SP1 或更高版本安裝在它上。
**MABS 網域** | MABS VM 必須加入網域。 具有管理員權限的網域使用者必須在 VM 上安裝 MABS。
**Azure Stack VM 資料備份** | 您可以備份檔案、資料夾和應用程式。
**支援的備份** | 這些作業系統支援要備份的 VM：<br/><br/> Windows 伺服器半年頻道（資料中心、企業版、標準頻道）<br/><br/> 視窗伺服器 2016， Windows 伺服器 2012 R2， Windows 伺服器 2008 R2
**對 Azure 堆疊 VM 的 SQL 伺服器支援** | 備份 SQL Server 2016， SQL Server 2014， SQL Server 2012 SP1.<br/><br/> 備份和恢復資料庫。
**Azure Stack VM 的 SharePoint 支援** | SharePoint 2016， SharePoint 2013， 共用點 2010.<br/><br/> 備份和恢復伺服器場、資料庫、前端和 Web 服務器。
**已備份 VM 的網路需求** | Azure 堆疊工作負荷中的所有 VM 都必須屬於同一虛擬網路，並且屬於同一訂閱。

## <a name="dpmmabs-networking-support"></a>DPM/MABS 網路支援

### <a name="url-access"></a>URL 存取

DPM 伺服器/MABS 需要存取下列 URL：

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="azure-expressroute-support"></a>Azure 快速路由支援

您可以使用公共對等互連（可用於舊電路）和 Microsoft 對等互連，通過 Azure ExpressRoute 備份資料。 不支援通過私有對等互連進行備份。

使用公共對等互連：確保訪問以下域/位址：

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

使用 Microsoft 對等互連，請選擇以下服務/區域和相關社區價值觀：

* Azure 活動目錄 （12076：5060）
* 微軟 Azure 區域（根據恢復服務保存庫的位置）
* Azure 存儲（根據恢復服務保存庫的位置）

有關詳細資訊，請參閱[快速路由路由要求](https://docs.microsoft.com/azure/expressroute/expressroute-routing)。

>[!NOTE]
>公共對等互連被棄用用於新電路。

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM/MABS 與 Azure 備份的連線

必須連線至 Azure 備份服務才能正常執行備份，且 Azure 訂用帳戶必須作用中。 下表說明不符合這兩個條件時的行為。

**MABS 對 Azure** | **訂閱** | **備份/還原**
--- | --- | ---
連線 | Active | 備份到 DPM/MABS 磁片。<br/><br/> 返回 Azure。<br/><br/> 從磁片還原。<br/><br/> 從 Azure 還原。
連線 | 已過期/取消佈建 | 不會備份至磁碟或 Azure。<br/><br/> 如果訂閱已過期，則可以從磁片或 Azure 進行還原。<br/><br/> 如果訂閱已停用，則無法從磁片或 Azure 還原。 Azure 復原點已刪除。
未連線超過 15 天 | Active | 不會備份至磁碟或 Azure。<br/><br/> 您可以從磁碟或 Azure 還原。
未連線超過 15 天 | 已過期/取消佈建 | 不會備份至磁碟或 Azure。<br/><br/> 如果訂閱已過期，則可以從磁片或 Azure 進行還原。<br/><br/> 如果訂閱已停用，則無法從磁片或 Azure 還原。 Azure 復原點已刪除。

## <a name="dpmmabs-storage-support"></a>DPM/MABS 儲存體支援

備份到 DPM/MABS 的資料存儲在本地磁片存儲中。

**儲存空間** | **詳細資料**
--- | ---
**MBS** | DPM 2016/MABS v2 及更高版本支援現代備份存儲 （MBS）。 MBS 不適用於 MABS v1。
**Azure VM 上的 MABS 儲存體** | 資料存儲在連接到 DPM/MABS VM 的 Azure 磁片上，並在 DPM/MABS 中管理。 可用於 DPM/MABS 存儲池的磁片數受 VM 大小的限制。<br/><br/> A2 VM：4 個磁片;A3 VM：8 個磁片;A4 VM：16 個磁片，每個磁片的最大大小為 1 TB。 這將確定可用的備份存儲池總數。<br/><br/> 您可以備份的資料量取決於連結的磁碟數目和大小。
**Azure VM 上的 MABS 資料保留** | 我們建議您在 DPM/MABS Azure 磁片上保留資料一天，並從 DPM/MABS 備份到保存庫，以便延長保留時間。 藉此，您就可以藉由將資料卸載至 Azure 備份來保護較大量的資料。

### <a name="modern-backup-storage-mbs"></a>新式備份儲存體 (MBS)

從 DPM 2016/MABS v2（在 Windows 伺服器 2016 上運行）到更高版本，您可以利用現代備份存儲 （MBS）。

- MBS 備份會儲存在復原檔案系統 (ReFS) 磁碟上。
- MBS 使用 ReFS 塊克隆來加快備份速度並更有效地利用存儲空間。
- 將卷添加到本地 DPM/MABS 存儲池時，請使用磁碟機號配置卷。 隨後，您可以在不同的磁碟區上設定工作負載儲存體。
- 當您建立用來將資料備份至 DPM/MABS 的保護群組時，您可以選取所要使用的磁碟機。 例如，您可以將 SQL 或其他高 IOPS 工作負載的備份存儲在高性能磁碟機上，並存儲在性能較低的磁碟機上備份頻率較低的工作負載。

## <a name="supported-backups-to-mabs"></a>MABS 支援的備份

有關可以使用 Azure 備份伺服器保護的各種伺服器和工作負荷的資訊，請參閱[Azure 備份伺服器保護矩陣](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix)。

## <a name="supported-backups-to-dpm"></a>DPM 支援的備份

有關可以使用資料保護管理器保護的各種伺服器和工作負載的資訊，請參閱文章[DPM 可以備份什麼？](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019)

- 由 DPM/MABS 備份的群集工作負載應與 DPM/MABS 位於同一域或子/受信任域中。
- 您可以使用 NTLM/憑證驗證，在不受信任的網域或工作群組中備份資料。

## <a name="next-steps"></a>後續步驟

- [深入了解](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS 架構。
- [檢閱](backup-support-matrix-mars-agent.md) MARS 代理程式支援的作業。
- [設定](backup-azure-microsoft-azure-backup.md) MABS 伺服器。
- [設置 DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180)。
