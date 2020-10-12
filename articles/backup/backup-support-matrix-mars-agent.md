---
title: MARS 代理程式的支援矩陣
description: 本文摘要說明當您備份執行 Microsoft Azure 復原服務 (MARS) 代理程式之電腦的 Azure 備份支援。
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: b11a2e3ec2fdf3a46b324dcc0f95d4666a84c179
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332673"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>使用 Microsoft Azure 復原服務 (MARS) 代理程式進行備份的支援矩陣

您可以使用 [Azure 備份服務](backup-overview.md) 來備份內部部署機器和應用程式，以及備份 (vm) 的 Azure 虛擬機器。 本文將摘要說明使用 Microsoft Azure 復原服務 (MARS) 代理程式來備份電腦時的支援設定和限制。

## <a name="the-mars-agent"></a>MARS 代理程式

Azure 備份使用 MARS 代理程式，將資料從內部部署機器和 Azure Vm 備份至 Azure 中的備份復原服務保存庫。 MARS 代理程式可以：

- 在內部部署 Windows 機器上執行，使其可以直接備份至 Azure 中的備份復原服務保存庫。
- 在 Windows Vm 上執行，使其可以直接備份至保存庫。
- 在 Microsoft Azure 備份 Server (MABS) 或 System Center Data Protection Manager (DPM) Server 上執行。 在此案例中，機器和工作負載會備份至 MABS 或 DPM 服務器。 MARS 代理程式接著會將此伺服器備份到 Azure 中的保存庫。

> [!NOTE]
>Azure 備份不支援針對日光節約時間的時鐘自動調整 (DST) 。 修改原則，以確保會將日光節約納入考慮，以防止實際時間和排程備份時間之間的差異。

您的備份選項取決於代理程式的安裝位置。 如需詳細資訊，請參閱 [使用 MARS 代理程式 Azure 備份架構](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。 如需有關 MABS 和 DPM 備份架構的詳細資訊，請參閱 [備份至 dpm 或 MABS](backup-architecture.md#architecture-back-up-to-dpmmabs)。 另請參閱備份架構的 [需求](backup-support-matrix-mabs-dpm.md) 。

**安裝** | **詳細資料**
--- | ---
下載最新的 MARS 代理程式 | 您可以從保存庫或[從這裡直接下載](https://aka.ms/azurebackup_agent)最新版本的代理程式。
直接在機器上安裝 | 您可以直接在內部部署 Windows server 或在執行任何 [支援之作業系統](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)的 windows VM 上安裝 MARS 代理程式。
在備份伺服器上安裝 | 當您將 DPM 或 MABS 設定為備份至 Azure 時，您可以在伺服器上下載並安裝 MARS 代理程式。 您可以在備份伺服器支援矩陣中的 [支援作業系統](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) 上安裝代理程式。

> [!NOTE]
> 根據預設，已啟用備份的 Azure Vm 會安裝 Azure 備份延伸模組。 此擴充功能會備份整個 VM。 如果您想要備份特定的資料夾和檔案，而不是完整 VM，您可以在 Azure VM 上安裝 MARS 代理程式，讓其與擴充功能一起執行。
> 當您在 Azure VM 上執行 MARS 代理程式時，它會備份 VM 上暫存儲存體中的檔案或資料夾。 如果檔案或資料夾已從暫存儲存體中移除，或已移除暫存儲存體，則備份會失敗。

## <a name="cache-folder-support"></a>快取資料夾支援

使用 MARS 代理程式來備份資料時，代理程式會取得資料的快照集，並將資料儲存在本機快取資料夾中，然後再將資料傳送至 Azure。 快取 (臨時) 資料夾有幾個需求：

**Cache** | **詳細資料**
--- | ---
大小 |  快取資料夾中的可用空間應該至少為備份資料的整體大小5到10%。
Location | 快取資料夾必須在要備份的機器上儲存在本機，而且必須在線上。 快取資料夾不應位於網路共用、卸載式媒體或離線磁片區上。
資料夾 | 快取資料夾不應該在重復資料刪除磁片區或壓縮的資料夾中加密，也就是稀疏或有重新分析點。
位置變更 | 您可以藉由停止備份引擎 (`net stop bengine`) ，並將快取資料夾複製到新的磁片磁碟機，來變更快取位置。  (確定新磁片磁碟機有足夠的空間。 ) 接著在 **HKLM\SOFTWARE\Microsoft\Windows Azure 備份** (**Config/ScratchLocation** 和 **Config/CloudBackupProvider/ScratchLocation**) 更新兩個登錄專案，然後重新開機引擎。

## <a name="networking-and-access-support"></a>網路功能與存取支援

### <a name="url-and-ip-access"></a>URL 和 IP 存取

MARS 伺服器需要存取這些 URL：

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

以及這些 IP 位址：

- 20.190.128.0/18
- 40.126.0.0/18

存取上述所有 Url 和 IP 位址時，會使用埠443上的 HTTPS 通訊協定。

使用 MARS 代理程式從 Azure Vm 備份檔案和資料夾時，也需要設定 Azure 虛擬網路以允許存取。 如果您使用網路安全性群組 (NSG)，請使用 AzureBackup 服務標籤，以允許對 Azure 備份進行輸出存取。 除了 Azure 備份標籤之外，您還需要為 Azure AD (*AzureActiveDirectory*) 和 Azure 儲存體 (儲存體) 建立類似的 [NSG 規則](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview#service-tags)，以允許用於驗證和資料傳輸的連線。 下列步驟說明建立 Azure 備份標籤規則的程序：

1. 在 [所有服務] 中，移至 [網路安全性群組]，然後選取網路安全性群組。
2. 選取 [設定] 底下的 [輸出安全性規則]。
3. 選取 [新增]。 輸入可供用於建立新規則的所有必要詳細資料，如[安全性規則設定](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)中所述。 請確定 [目的地] 選項已設定為 [服務標籤]，且 [目的地服務標籤] 已設定為 [AzureBackup]。
4. 選取 [ **新增** ] 以儲存新建立的輸出安全性規則。

同樣地，您也可以建立 Azure 儲存體和 Azure AD 的 NSG 輸出安全性規則。 如需服務標記的詳細資訊，請參閱這篇[文章](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)。

### <a name="azure-expressroute-support"></a>Azure ExpressRoute 支援

您可以透過具有公用對等互連的 Azure ExpressRoute 備份資料 (適用于舊線路) 和 Microsoft 對等互連。 不支援透過私人對等互連進行備份。

使用公用對等互連：確定存取下列網域/位址：

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

使用 Microsoft 對等互連時，請選取下列服務/區域和相關的群組值：

- 根據您的復原服務保存庫位置 Azure 備份 () 
- Azure Active Directory (12076:5060)
- 根據您的復原服務保存庫位置 Azure 儲存體 () 

如需詳細資訊，請參閱 [ExpressRoute 路由需求](../expressroute/expressroute-routing.md#bgp)。

>[!NOTE]
>新電路的公用對等互連已被取代。

### <a name="private-endpoint-support"></a>私人端點支援

您現在可以使用私人端點，將您的資料從伺服器安全地備份到復原服務保存庫。 由於 Azure Active Directory 目前不支援私人端點，因此 Azure Active Directory 所需的 Ip 和 Fqdn 都必須個別允許輸出存取。

使用 MARS 代理程式來備份您的內部部署資源時，請確定您的內部部署網路 (包含要備份的資源) 與包含保存庫私人端點的 Azure VNet 對等互連。 然後，您可以繼續安裝 MARS 代理程式，並設定備份。 不過，您必須確保所有進行備份的通訊只會透過對等互連網路進行。

如果您在註冊 MARS 代理程式之後移除保存庫的私人端點，您必須使用保存庫重新註冊容器。 您不需要停止保護它們。

深入瞭解 [Azure 備份的私人端點](private-endpoints.md)。

### <a name="throttling-support"></a>節流支援

**功能** | **詳細資料**
--- | ---
頻寬控制 | 支援。 在 MARS 代理程式中，使用 [ **變更屬性** ] 來調整頻寬。
網路節流設定 | 適用于執行 Windows Server 2008 R2、Windows Server 2008 SP2 或 Windows 7 的備份機器。

## <a name="supported-operating-systems"></a>支援的作業系統

>[!NOTE]
> MARS 代理程式不支援 Windows Server Core Sku。

您可以使用 MARS 代理程式，直接備份到下列執行的作業系統上的 Azure：

1. 內部部署 Windows Server
2. 執行 Windows 的 Azure VM

作業系統必須是64位，而且應該執行最新的服務套件和更新。 下表摘要說明這些作業系統：

**作業系統** | **檔案/資料夾** | **系統狀態** | **軟體/模組需求**
--- | --- | --- | ---
Windows 10 (企業版、專業版、家用版) | 是 | 否 |  檢查對應的伺服器版本是否有軟體/模組需求
Windows 8.1 (企業版、專業版)| 是 |否 | 檢查對應的伺服器版本是否有軟體/模組需求
Windows 8 (企業版、專業版) | 是 | 否 | 檢查對應的伺服器版本是否有軟體/模組需求
Windows Server 2016 (Standard、Datacenter、Essentials) | 是 | 是 | -.NET 4。5 <br> -Windows PowerShell <br> -最新相容的 Microsoft VC + + 可轉散發套件 <br> -Microsoft Management Console (MMC) 3。0
Windows Server 2012 R2(Standard、Datacenter、Essentials) | 是 | 是 | -.NET 4。5 <br> -Windows PowerShell <br> -最新相容的 Microsoft VC + + 可轉散發套件 <br> -Microsoft Management Console (MMC) 3。0
Windows Server 2012 (Standard、Datacenter、Essentials) | 是 | 是 |-.NET 4。5 <br> -Windows PowerShell <br> -最新相容的 Microsoft VC + + 可轉散發套件 <br> -Microsoft Management Console (MMC) 3。0 <br> -部署映射服務與管理 ( # A0) 
Windows Storage Server 2016/2012 R2/2012 (Standard，Workgroup)  | 是 | 否 | -.NET 4。5 <br> -Windows PowerShell <br> -最新相容的 Microsoft VC + + 可轉散發套件 <br> -Microsoft Management Console (MMC) 3。0
Windows Server 2019 (Standard、Datacenter、Essentials) | 是 | 是 | -.NET 4。5 <br> -Windows PowerShell <br> -最新相容的 Microsoft VC + + 可轉散發套件 <br> -Microsoft Management Console (MMC) 3。0

如需詳細資訊，請參閱 [支援的 MABS 和 DPM 作業系統](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)。

### <a name="operating-systems-at-end-of-support"></a>終止支援時的作業系統

下列作業系統是在終止支援的時候，強烈建議您升級作業系統，以繼續保持受保護。

如果現有的承諾可防止升級作業系統，請考慮將 Windows 伺服器遷移至 Azure Vm，並利用 Azure VM 備份來繼續保持受保護。 如需有關遷移 Windows server 的詳細資訊，請流覽此處的 [ [遷移] 頁面](https://azure.microsoft.com/migration/windows-server/) 。

若為內部部署或裝載環境，您無法將作業系統升級或遷移至 Azure，請為電腦啟用延伸安全性更新，以繼續保持受保護且受支援。 請注意，只有特定版本符合延伸安全性更新的資格。 若要深入瞭解，請流覽 [常見問題頁面](https://www.microsoft.com/windows-server/extended-security-updates) 。

| **作業系統**                                       | **檔案/資料夾** | **系統狀態** | **軟體/模組需求**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (旗艦版、Enterprise 版、專業版、Home Premium/Basic、Starter)  | 是               | 否                 | 檢查對應的伺服器版本是否有軟體/模組需求 |
| Windows Server 2008 R2 (Standard、Enterprise、Datacenter、Foundation)  | 是               | 是                | -.NET 3.5、.NET 4。5 <br>  -Windows PowerShell <br>  -相容的 Microsoft VC + + 可轉散發套件 <br>  -Microsoft Management Console (MMC) 3。0 <br>  -部署映射服務與管理 ( # A0)  |
| Windows Server 2008 SP2 (Standard、Datacenter、Foundation)   | 是               | 否                 | -.NET 3.5、.NET 4。5 <br>  -Windows PowerShell <br>  -相容的 Microsoft VC + + 可轉散發套件 <br>  -Microsoft Management Console (MMC) 3。0 <br>  -部署映射服務與管理 ( # A0)  <br>  -Virtual Server 2005 base + KB KB948515 |

## <a name="backup-limits"></a>備份限制

### <a name="size-limits"></a>大小限制

Azure 備份限制可備份的檔案或資料夾資料來源的大小。 您從單一磁片區備份的專案不能超過此表中摘要說明的大小：

**作業系統** | **大小限制**
--- | ---
Windows Server 2012 或更新版本 |54,400 GB
Windows Server 2008 R2 SP1 |1,700 GB
Windows Server 2008 SP2| 1,700 GB
Windows 8 或更新版本| 54,400 GB
Windows 7| 1,700 GB

### <a name="other-limitations"></a>其他限制

- MARS 不支援對單一保存庫使用相同名稱的多部電腦進行保護。

## <a name="supported-file-types-for-backup"></a>支援的備份檔案類型

**型別** | **支援**
--- | ---
加密<sup>*</sup>| 支援。
Compressed | 支援。
疏鬆 | 支援。
已壓縮和疏鬆 |支援。
硬式連結| 不支援。 跳。
重新分析點| 不支援。 跳。
加密和疏鬆 |不支援。 跳。
壓縮資料流| 不支援。 跳。
疏鬆資料流| 不支援。 跳。
OneDrive (同步處理的檔案是稀疏資料流程) | 不支援。
已啟用 DFS 複寫的資料夾 | 不支援。

\* 確定 MARS 代理程式可以存取所需的憑證，以存取加密的檔案。 將略過無法存取的檔案。

## <a name="supported-drives-or-volumes-for-backup"></a>備份所支援的磁片磁碟機或磁片區

**磁碟機/磁碟區** | **支援** | **詳細資料**
--- | --- | ---
唯讀磁碟區| 不支援 | 磁片區陰影複製服務 (VSS) 只有在磁片區可寫入時才能運作。
離線磁碟區| 不支援 |只有當磁片區在線上時，VSS 才能運作。
網路共用| 不支援 |磁片區必須是伺服器上的本機磁片區。
BitLocker 鎖定的磁片區| 不支援 |磁片區必須先解除鎖定，才能開始備份。
檔案系統識別| 不支援 |僅支援 NTFS。
卸除式媒體| 不支援 |所有備份專案來源都必須有 *固定* 狀態。
刪除重複資料的磁碟機 | 支援 | Azure 備份會將刪除重複資料的資料轉換成一般資料。 它會將資料優化、加密、儲存，並將資料傳送至保存庫。

## <a name="support-for-initial-offline-backup"></a>支援初始離線備份

Azure 備份支援 *離線植* 入，以使用磁片將初始備份資料傳輸至 Azure。 如果您的初始備份可能在 tb (Tb) 的大小範圍內，這項支援就很有説明。 支援離線備份的狀況：

- 在執行 MARS 代理程式的內部部署機器上直接備份檔案和資料夾。
- 從 DPM 伺服器或 MABS 備份工作負載和檔案。

離線備份無法用於系統狀態檔案。

## <a name="support-for-data-restoration"></a>支援資料還原

使用 Azure 備份的「 [立即還原](backup-instant-restore-capability.md) 」功能時，您可以在將資料複製到保存庫之前，先將資料還原。 您要備份的電腦必須執行 .NET Framework 4.5.2 或更高版本。

備份無法還原到執行舊版作業系統的目的電腦。 例如，從執行 Windows 7 的電腦建立的備份可以在 Windows 8 或更新版本上還原。 但是，在執行 Windows 7 的電腦上，從執行 Windows 8 的電腦上所建立的備份無法還原。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [使用 MARS 代理程式的備份架構](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
- 瞭解當您 [在 MABS 或 DPM 服務器上執行 MARS 代理程式](backup-support-matrix-mabs-dpm.md)時所支援的功能。
