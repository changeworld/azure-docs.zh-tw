---
title: MABS & System Center DPM 支援矩陣
description: 本文摘要說明當您使用 Microsoft Azure 備份 Server (MABS) 或 System Center DPM 來備份內部部署和 Azure VM 資源時的 Azure 備份支援。
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 011e115c7f3cc94b03ffd9ad2467406c60738033
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332690"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>使用 Microsoft Azure 備份 Server 或 System Center DPM 進行備份的支援矩陣

您可使用 [Azure 備份服務](backup-overview.md)來備份內部部署機器與工作負載，以及 Azure 虛擬機器 (VM)。 本文摘要說明如何使用 Microsoft Azure 備份 Server (MABS) 或 System Center Data Protection Manager (DPM) 和 Azure 備份，來備份電腦的支援設定和限制。

## <a name="about-dpmmabs"></a>關於 DPM/MABS

[System CENTER DPM](/system-center/dpm/dpm-overview) 是企業解決方案，可設定、促進及管理企業電腦和資料的備份和復原。 它是 [System Center](https://www.microsoft.com/system-center/pricing) 產品套件的一部分。

MABS 是一種伺服器產品，可用來備份內部部署實體伺服器、Vm，以及在其上執行的應用程式。

MABS 是以 System Center DPM 為基礎，並提供類似的功能，但有一些差異：

- MABS 不需要 System Center 授權即可執行。
- 針對 MABS 和 DPM，Azure 提供長期備份儲存體。 此外，DPM 可讓您將資料備份在磁帶上進行長期儲存。 MABS 未提供這項功能。
- [您可以使用次要 dpm 伺服器來備份主要 dpm 伺服器](/system-center/dpm/back-up-the-dpm-server)。 次要伺服器會保護主要伺服器資料庫，和儲存在主要伺服器上的資料來源複本。 當主要伺服器失敗時，次要伺服器將可繼續保護主要伺服器所保護的工作負載，直到主要伺服器恢復可用性為止。  MABS 未提供這項功能。

您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57520)下載 MABS。 它可以在內部部署或在 Azure VM 上執行。

DPM 和 MABS 皆提供備份多種不同應用程式和伺服器與用戶端作業系統的支援。 它們適用於多種備份案例：

- 您可以使用系統狀態或裸機備份在機器層級進行備份。
- 您可以備份特定磁碟區、共用、資料夾和檔案。
- 您可以使用優化的應用程式感知設定來備份特定的應用程式。

## <a name="dpmmabs-backup"></a>DPM/MABS 備份

使用 DPM/MABS 和 Azure 備份進行備份的運作方式如下：

1. DPM/MABS 保護代理程式安裝在將備份的每部電腦上。
1. 電腦和應用程式會備份至 DPM/MABS 上的本機儲存體。
1. 在 DPM 伺服器/MABS 上安裝 Microsoft Azure 復原服務 (MARS) 代理程式。
1. MARS 代理程式會使用 Azure 備份，將 DPM/MABS 磁片備份到 Azure 中的備份復原服務保存庫。

其他資訊：

- [深入了解](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS 架構。
- [複習](backup-support-matrix-mars-agent.md) MARS 代理程式支援的功能。

## <a name="supported-scenarios"></a>支援的案例

**案例** | **代理程式** | **位置**
--- | --- | ---
**備份內部部署機器/工作負載** | DPM/MABS 保護代理程式會在您想要備份的機器上執行。<br/><br/> DPM/MABS 伺服器上的 MARS 代理程式。<br/> 啟用這項功能所需的 Microsoft Azure 復原服務代理程式或 Azure 備份代理程式的最低版本是 2.0.8719.0。  | DPM/MABS 必須在內部部署環境中執行。

## <a name="supported-deployments"></a>支援的部署

DPM/MABS 可依下表的摘要進行部署。

**部署** | **支援** | **詳細資料**
--- | --- | ---
**部署於內部部署** | 實體伺服器<br/><br/>Hyper-V VM<br/><br/> VMware VM | 如果 DPM/MABS 安裝為 VMware VM，它只會備份在這些 Vm 上執行的 VMware Vm 和工作負載。
**部署為 Azure Stack VM** | 僅限 MABS | DPM 無法用來備份 Azure Stack VM。
**部署為 Azure VM** | 保護在這些 Vm 上執行的 Azure Vm 和工作負載 | 在 Azure 中執行的 DPM/MABS 無法備份內部部署機器。

## <a name="supported-mabs-and-dpm-operating-systems"></a>支援的 MABS 和 DPM 作業系統

Azure 備份可以備份正在執行下列任何作業系統的 DPM/MABS 實例。 作業系統應執行最新的服務套件和更新。

**案例** | **DPM/MABS**
--- | ---
**Azure VM 上的 MABS** |  Windows 2016 Datacenter。<br/><br/> Windows 2019 Datacenter。<br/><br/> 建議您從 marketplace 中的映射開始。<br/><br/> 具有四個核心和 8 GB RAM 的最小 Standard_A4_v2。
**Azure VM 上的 DPM** | System Center 2012 R2 (含 Update 3 或更新版本)。<br/><br/> 符合 [System Center 需求](/system-center/dpm/prepare-environment-for-dpm#dpm-server)的 Windows 作業系統。<br/><br/> 建議您從 marketplace 中的映射開始。<br/><br/> 具有四個核心和 8 GB RAM 的最小 Standard_A4_v2。
**內部部署 MABS** |  MABS v3 和更新版本： Windows Server 2016 或 Windows Server 2019
**內部部署 DPM** | 實體伺服器/Hyper-v VM： System Center 2012 SP1 或更新版本。<br/><br/> VMware VM： System Center 2012 R2 Update 5 或更新版本。

>[!NOTE]
>Windows Server Core 或 Microsoft Hyper-V Server 不支援安裝 Azure 備份伺服器。

## <a name="management-support"></a>管理支援

**問題** | **詳細資料**
--- | ---
**安裝** | 在單一用途的電腦上安裝 DPM/MABS。<br/><br/> 請勿在網域控制站上、執行 Microsoft Exchange Server 或 System Center Operations Manager 的電腦上，或在叢集節點上安裝 DPM/MABS。<br/><br/> [檢查所有 DPM 系統需求](/system-center/dpm/prepare-environment-for-dpm#dpm-server)。
**網域** | DPM/MABS 應該加入網域。 請先安裝 DPM/MABS，再將其加入網域。 不支援在部署之後將 DPM/MABS 移至新網域。
**Storage** | DPM 2016/MABS v2 和更新版本支援新式備份儲存體 (MB) 。 MBS 不適用於 MABS v1。
**MABS 升級** | 您可以直接安裝 MABS v3，或是從 MABS v2 升級至 MABS v3。 [深入了解](backup-azure-microsoft-azure-backup.md#upgrade-mabs)。
**移動 MABS** | 使用 MABS 時，支援將 MABS 移至新伺服器，同時保留儲存體。<br/><br/> 伺服器必須與原始伺服器同名。 如果您想要保留相同的儲存體集區，並使用相同的 MABS 資料庫來儲存資料復原點，則不可變更名稱。<br/><br/> 您將需要 MABS 資料庫的備份，因為您需要加以還原。

## <a name="mabs-support-on-azure-stack"></a>Azure Stack 上的 MABS 支援

您可以將 MABS 部署在 Azure Stack VM 上，以便從單一位置管理 Azure Stack VM 和工作負載的備份。

**元件** | **詳細資料**
--- | ---
**Azure Stack VM 上的 MABS** | 大小至少為 A2。 建議您從 Azure Marketplace 開始使用 Windows Server 2012 R2 或 Windows Server 2016 映射。<br/><br/> 請勿在 MABS VM 上安裝其他任何東西。
**MABS 儲存體** | 針對 MABS VM 使用不同的儲存體帳戶。 在 MABS 上執行的 MARS 代理程式需要快取位置的暫存儲存體，以及保存從雲端還原的資料。
**MABS 儲存體集區** | MABS 存放集區的大小取決於連接至 MABS VM 的磁片數目和大小。 每個 Azure Stack VM 大小皆有磁碟數目上限。 例如，A2 是四個磁碟。
**MABS 保留期** | 請勿將備份的資料保留在本機 MABS 磁片上超過五天。
**MABS 相應增加** | 若要相應增加您的部署，您可以增加 MABS VM 的大小。 例如，您可以從 A 變更為 D 系列。<br/><br/> 您也可以確保定期將資料卸載至 Azure 備份。 如有必要，您可以部署其他 MABS 伺服器。
**MABS 上的 .NET Framework** | MABS VM 需要安裝 .NET Framework 3.3 SP1 或更新版本。
**MABS 網域** | MABS VM 必須加入網域。 具有管理員權限的網域使用者必須在 VM 上安裝 MABS。
**Azure Stack VM 資料備份** | 您可以備份檔案、資料夾和應用程式。
**支援的備份** | 您要備份的 Vm 支援下列作業系統：<br/><br/> Windows Server Semi-Annual Channel (Datacenter、Enterprise、Standard) <br/><br/> Windows Server 2016、Windows Server 2012 R2、Windows Server 2008 R2
**Azure Stack Vm 的 SQL Server 支援** | 備份 SQL Server 2016、SQL Server 2014、SQL Server 2012 SP1。<br/><br/> 備份和復原資料庫。
**Azure Stack VM 的 SharePoint 支援** | SharePoint 2016、SharePoint 2013、SharePoint 2010。<br/><br/> 備份和復原伺服器陣列、資料庫、前端和網頁伺服器。
**已備份 VM 的網路需求** | Azure Stack 工作負載中的所有 Vm 都必須屬於相同的虛擬網路，而且屬於相同的訂用帳戶。

## <a name="dpmmabs-networking-support"></a>DPM/MABS 網路支援

### <a name="url-access"></a>URL 存取

DPM 伺服器/MABS 需要存取下列 URL：

- `http://www.msftncsi.com/ncsi.txt`
- `*.Microsoft.com`
- `*.WindowsAzure.com`
- `*.microsoftonline.com`
- `*.windows.net`

### <a name="azure-expressroute-support"></a>Azure ExpressRoute 支援

您可以透過具有公用對等互連的 Azure ExpressRoute 備份資料 (適用于舊線路) 和 Microsoft 對等互連。 不支援透過私人對等互連進行備份。

使用公用對等互連：確定存取下列網域/位址：

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

使用 Microsoft 對等互連時，請選取下列服務/區域和相關的群組值：

- Azure Active Directory (12076:5060)
- 根據復原服務保存庫的位置 (Microsoft Azure 區域) 
- 根據您的復原服務保存庫位置 Azure 儲存體 () 

如需詳細資訊，請參閱 [ExpressRoute 路由需求](../expressroute/expressroute-routing.md)。

>[!NOTE]
>新電路的公用對等互連已被取代。

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM/MABS 與 Azure 備份的連線

必須連線至 Azure 備份服務才能正常執行備份，且 Azure 訂用帳戶必須作用中。 下表說明不符合這兩個條件時的行為。

**MABS 對 Azure** | **訂用帳戶** | **備份/還原**
--- | --- | ---
連線 | Active | 備份至 DPM/MABS 磁片。<br/><br/> 備份至 Azure。<br/><br/> 從磁片還原。<br/><br/> 從 Azure 還原。
已連線 | 已過期/取消佈建 | 不會備份至磁碟或 Azure。<br/><br/> 如果訂用帳戶已過期，您可以從磁片或 Azure 還原。<br/><br/> 如果訂用帳戶已解除委任，您就無法從磁片或 Azure 還原。 Azure 復原點已刪除。
未連線超過 15 天 | 使用中 | 不會備份至磁碟或 Azure。<br/><br/> 您可以從磁碟或 Azure 還原。
未連線超過 15 天 | 已過期/取消佈建 | 不會備份至磁碟或 Azure。<br/><br/> 如果訂用帳戶已過期，您可以從磁片或 Azure 還原。<br/><br/> 如果訂用帳戶已解除委任，您就無法從磁片或 Azure 還原。 Azure 復原點已刪除。

## <a name="domain-and-domain-trusts-support"></a>網域和網域信任支援

|需求 |詳細資料 |
|---------|---------|
|Domain    | DPM/MABS 伺服器應該位於 Windows Server 2019、Windows Server 2016、Windows Server 2012 R2、Windows Server 2012 網域中。        |
|網域信任   |  只要您在不同的樹系之間建立樹系層級的雙向信任，DPM/MABS 就支援跨樹系的資料保護。   <BR><BR>   在與 DPM/MABS 伺服器網域具有雙向信任關係的樹系中，DPM/MABS 可以保護跨網域的伺服器和工作站。 若要保護工作組或不受信任網域中的電腦，請參閱 [備份和還原工作組和不受信任網域中的工作負載。](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains)  |

## <a name="dpmmabs-storage-support"></a>DPM/MABS 儲存體支援

備份至 DPM/MABS 的資料會儲存在本機磁片儲存體上。

**Storage** | **詳細資料**
--- | ---
**MBS** | DPM 2016/MABS v2 和更新版本支援新式備份儲存體 (MB) 。 MBS 不適用於 MABS v1。
**Azure VM 上的 MABS 儲存體** | 資料會儲存在連接至 DPM/MABS VM 的 Azure 磁片上，並在 DPM/MABS 中進行管理。 可用於 DPM/MABS 存放集區的磁片數目受限於 VM 的大小。<br/><br/> A2 VM：4個磁片;A3 VM：8個磁片;A4 VM：16個磁片，每個磁片的大小上限為 1 TB。 這會決定可用的備份存放集區總數。<br/><br/> 您可以備份的資料量取決於連結的磁碟數目和大小。
**Azure VM 上的 MABS 資料保留** | 建議您將資料保留在 DPM/MABS Azure 磁片上一天，並從 DPM/MABS 備份至保存庫，以延長保留時間。 如此一來，您就可以藉由將資料卸載至 Azure 備份來保護更大量的資料。

### <a name="modern-backup-storage-mbs"></a>新式備份儲存體 (MBS)

從 DPM 2016/MABS v2 (在 Windows Server 2016) 和更新版本上執行，您可以利用新式備份儲存體 (MB) 。

- MBS 備份會儲存在復原檔案系統 (ReFS) 磁碟上。
- MB 會使用 ReFS 區塊複製以加快備份的速度，並更有效率地使用儲存空間。
- 當您將磁片區新增至本機 DPM/MABS 存放集區時，您會使用磁碟機號來設定它們。 隨後，您可以在不同的磁碟區上設定工作負載儲存體。
- 當您建立用來將資料備份至 DPM/MABS 的保護群組時，您可以選取所要使用的磁碟機。 例如，您可以將 SQL 或其他高 IOPS 工作負載的備份儲存在高效能磁片磁碟機上，並將備份頻率較低的工作負載儲存在較低的效能磁片磁碟機上。

## <a name="supported-backups-to-mabs"></a>MABS 支援的備份

如需您可以使用 Azure 備份伺服器保護之各種伺服器和工作負載的相關資訊，請參閱 [Azure 備份伺服器 Protection 對照表](./backup-mabs-protection-matrix.md#protection-support-matrix)。

## <a name="supported-backups-to-dpm"></a>DPM 支援的備份

如需您可以使用 Data Protection Manager 保護之各種伺服器和工作負載的相關資訊，請參閱「 [DPM 可以備份哪些專案？」一](/system-center/dpm/dpm-protection-matrix)文。

- DPM/MABS 備份的叢集工作負載應位於與 DPM/MABS 相同的網域中，或位於子域/受信任的網域中。
- 您可以使用 NTLM/憑證驗證，在不受信任的網域或工作群組中備份資料。

## <a name="next-steps"></a>後續步驟

- [深入了解](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS 架構。
- [檢閱](backup-support-matrix-mars-agent.md) MARS 代理程式支援的作業。
- [設定](backup-azure-microsoft-azure-backup.md) MABS 伺服器。
- [設定 DPM](/system-center/dpm/install-dpm)。
