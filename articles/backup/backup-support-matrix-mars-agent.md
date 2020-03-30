---
title: MARS 代理的支援矩陣
description: 本文總結了備份運行 Microsoft Azure 恢復服務 （MARS） 代理的電腦時支援的 Azure 備份支援。
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 6085bc647c06b5907282460a2d8706b8549e1bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247861"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>使用 Microsoft Azure 復原服務 (MARS) 代理程式進行備份的支援矩陣

可以使用 Azure[備份服務](backup-overview.md)備份本地電腦和應用，並備份 Azure 虛擬機器 （VM）。 本文總結了使用 Microsoft Azure 恢復服務 （MARS） 代理備份電腦時的支援設置和限制。

## <a name="the-mars-agent"></a>MARS 代理

Azure 備份使用 MARS 代理將資料從本地電腦和 Azure VM 備份到 Azure 中的備份恢復服務保存庫。 MARS 代理可以：

- 在本地 Windows 電腦上運行，以便它們可以直接備份到 Azure 中的備份恢復服務保存庫。
- 在 Windows VM 上運行，以便可以直接備份到保存庫。
- 在 Microsoft Azure 備份伺服器 （MABS） 或系統中心資料保護管理器 （DPM） 伺服器上運行。 在這種情況下，電腦和工作負載將備份到 MABS 或 DPM 服務器。 然後，MARS 代理將此伺服器備份到 Azure 中的保存庫。

> [!NOTE]
>Azure 備份不支援自動調整夏令時 （DST） 的時鐘。 修改策略以確保將夏令時考慮在內，以防止實際時間和計畫備份時間之間的差異。

備份選項取決於代理的安裝位置。 有關詳細資訊，請參閱使用[MARS 代理 的 Azure 備份體系結構](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。 有關 MABS 和 DPM 備份體系結構的資訊，請參閱[備份到 DPM 或 MABS](backup-architecture.md#architecture-back-up-to-dpmmabs)。 另請參閱備份體系結構[的要求](backup-support-matrix-mabs-dpm.md)。

**安裝** | **詳細資料**
--- | ---
下載最新的 MARS 代理 | 您可以從保存庫或[從這裡直接下載](https://aka.ms/azurebackup_agent)最新版本的代理程式。
直接在機器上安裝 | 可以直接在本地 Windows 伺服器或運行任何[受支援的作業系統](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)的 Windows VM 上安裝 MARS 代理。
在備份伺服器上安裝 | 當您將 DPM 或 MABS 設定為備份至 Azure 時，您可以在伺服器上下載並安裝 MARS 代理程式。 您可以在備份伺服器支援矩陣中[支援](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)作業系統上安裝代理。

> [!NOTE]
> 預設情況下，為備份啟用的 Azure VM 具有 Azure 備份擴展程式安裝。 此擴充功能會備份整個 VM。 如果您想要備份特定的資料夾和檔案，而不是完整 VM，您可以在 Azure VM 上安裝 MARS 代理程式，讓其與擴充功能一起執行。
> 在 Azure VM 上運行 MARS 代理時，它會備份 VM 上臨時存儲中的檔或資料夾。 如果檔或資料夾從臨時存儲中刪除或臨時存儲被刪除，則備份將失敗。

## <a name="cache-folder-support"></a>快取資料夾支援

使用 MARS 代理備份資料時，代理會獲取資料的快照，並將其存儲在本機快取資料夾中，然後再將資料發送到 Azure。 緩存（臨時）資料夾有幾個要求：

**快取** | **詳細資料**
--- | ---
大小 |  快取檔案夾中的可用空間應至少為備份資料總大小的 5% 到 10%。
Location | 快取檔案夾必須本機存放區在正在備份的電腦上，並且必須連線。 快取檔案夾不應位於網路共用、卸除式媒體或離線卷上。
資料夾 | 快取檔案夾不應在重復資料副本的卷上或壓縮的資料夾中加密，該資料夾是稀疏的，或者具有重解析點。
位置更改 | 您可以通過停止備份引擎 （`net stop bengine`） 並將快取檔案夾複製到新磁碟機來更改緩存位置。 （確保新磁碟機有足夠的空間。然後，將**HKLM_SOFTWARE_Microsoft_Windows Azure 備份**（**配置/暫存位置**和**配置/雲備份提供程式/暫存位置**）下的兩個登錄機碼更新到新位置並重新啟動引擎。

## <a name="networking-and-access-support"></a>網路功能與存取支援

### <a name="url-and-ip-access"></a>URL 和 IP 訪問

MARS 伺服器需要存取這些 URL：

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

以及這些 IP 位址：

- 20.190.128.0/18
- 40.126.0.0/18

訪問上面列出的所有 URL 和 IP 位址使用埠 443 上的 HTTPS 協定。

### <a name="azure-expressroute-support"></a>Azure 快速路由支援

您可以使用公共對等互連（可用於舊電路）和 Microsoft 對等互連，通過 Azure ExpressRoute 備份資料。 不支援通過私有對等互連進行備份。

使用公共對等互連：確保訪問以下域/位址：

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

使用 Microsoft 對等互連，請選擇以下服務/區域和相關社區價值觀：

- Azure 活動目錄 （12076：5060）
- 微軟 Azure 區域（根據恢復服務保存庫的位置）
- Azure 存儲（根據恢復服務保存庫的位置）

有關詳細資訊，請參閱[快速路由路由要求](https://docs.microsoft.com/azure/expressroute/expressroute-routing)。

>[!NOTE]
>公共對等互連被棄用用於新電路。

### <a name="throttling-support"></a>節流支援

**特徵** | **詳細資料**
--- | ---
頻寬控制 | 支援。 在 MARS 代理中，使用 **"更改屬性"** 調整頻寬。
網路節流 | 不適用於運行 Windows 伺服器 2008 R2、Windows 伺服器 2008 SP2 或 Windows 7 的備份電腦。

## <a name="supported-operating-systems"></a>支援的作業系統

>[!NOTE]
> MARS 代理不支援 Windows 伺服器核心 SKU。

可以使用 MARS 代理直接備份到下面列出的作業系統上的 Azure，

1. 本地 Windows 伺服器
2. 執行 Windows 的 Azure VM

作業系統必須為 64 位，並且應運行最新的服務包和更新。 下表總結了這些作業系統：

**作業系統** | **檔案/資料夾** | **系統狀態** | **軟體/模組要求**
--- | --- | --- | ---
Windows 10 (企業版、專業版、家用版) | 是 | 否 |  檢查相應的伺服器版本，瞭解軟體/模組要求
Windows 8.1 (企業版、專業版)| 是 |否 | 檢查相應的伺服器版本，瞭解軟體/模組要求
Windows 8 (企業版、專業版) | 是 | 否 | 檢查相應的伺服器版本，瞭解軟體/模組要求
Windows Server 2016 (Standard、Datacenter、Essentials) | 是 | 是 | - .NET 4.5 <br> - Windows 電源外殼 <br> - 最新相容微軟 VC++ 可再發行 <br> - 微軟管理主控台 （MMC） 3.0
Windows Server 2012 R2(Standard、Datacenter、Essentials) | 是 | 是 | - .NET 4.5 <br> - Windows 電源外殼 <br> - 最新相容微軟 VC++ 可再發行 <br> - 微軟管理主控台 （MMC） 3.0
Windows Server 2012 (Standard、Datacenter、Essentials) | 是 | 是 |- .NET 4.5 <br> -Windows PowerShell <br> - 最新相容微軟 VC++ 可再發行 <br> - 微軟管理主控台 （MMC） 3.0 <br> - 部署映像服務與管理 （DISM.exe）
Windows 存儲伺服器 2016/2012 R2/2012（標準工作組） | 是 | 否 | - .NET 4.5 <br> - Windows 電源外殼 <br> - 最新相容微軟 VC++ 可再發行 <br> - 微軟管理主控台 （MMC） 3.0
Windows Server 2019 (Standard、Datacenter、Essentials) | 是 | 是 | - .NET 4.5 <br> - Windows 電源外殼 <br> - 最新相容微軟 VC++ 可再發行 <br> - 微軟管理主控台 （MMC） 3.0

有關詳細資訊，請參閱支援的[MABS 和 DPM 作業系統](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)。

### <a name="operating-systems-at-end-of-support"></a>支援結束時的作業系統

以下作業系統處於支援狀態，強烈建議升級作業系統以繼續受到保護。

如果現有承諾阻止升級作業系統，請考慮將 Windows 伺服器遷移到 Azure VM，並利用 Azure VM 備份繼續受到保護。 有關遷移 Windows 伺服器的詳細資訊，請訪問[此處的遷移頁面](https://azure.microsoft.com/migration/windows-server/)。

對於無法升級作業系統或遷移到 Azure 的本地或託管環境，請啟動電腦的擴展安全更新，以便電腦繼續受到保護和支援。 請注意，只有特定版本才有資格獲得擴展安全更新。 請訪問[常見問題頁面](https://www.microsoft.com/cloud-platform/extended-security-updates)瞭解更多資訊。

| **作業系統**                                       | **檔案/資料夾** | **系統狀態** | **軟體/模組要求**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7（終極、企業版、專業版、家庭高級版/基本版、入門版） | 是               | 否                 | 檢查相應的伺服器版本，瞭解軟體/模組要求 |
| Windows 伺服器 2008 R2（標準、企業、資料中心、基礎） | 是               | 是                | - .NET 3.5， .NET 4.5 <br>  - Windows 電源外殼 <br>  - 相容的微軟 VC++ 可轉散布 <br>  - 微軟管理主控台 （MMC） 3.0 <br>  - 部署映像服務與管理 （DISM.exe） |
| Windows 伺服器 2008 SP2 （標準， 資料中心， 基礎）  | 是               | 否                 | - .NET 3.5， .NET 4.5 <br>  - Windows 電源外殼 <br>  - 相容的微軟 VC++ 可轉散布 <br>  - 微軟管理主控台 （MMC） 3.0 <br>  - 部署映像服務與管理 （DISM.exe） <br>  - 虛擬伺服器 2005 基礎 = KB948515 |

## <a name="backup-limits"></a>備份限制

### <a name="size-limits"></a>大小限制

Azure 備份限制可備份的檔或資料夾資料來源的大小。 從單個卷備份的專案不能超過下表中匯總的大小：

**作業系統** | **大小限制**
--- | ---
Windows Server 2012 或更新版本 |54,400 GB
Windows Server 2008 R2 SP1 |1，700 GB
Windows Server 2008 SP2| 1，700 GB
Windows 8 或更新版本| 54,400 GB
Windows 7| 1，700 GB

### <a name="other-limitations"></a>其他限制

- MARS 不支援對單個保存庫保護同名的多台電腦。

## <a name="supported-file-types-for-backup"></a>支援的備份檔案類型

**類型** | **支援**
--- | ---
加密<sup>*</sup>| 支援。
Compressed | 支援。
疏鬆 | 支援。
已壓縮和疏鬆 |支援。
永久連結| 不支援。 已略過。
重新分析點| 不支援。 已略過。
加密和疏鬆 |不支援。 已略過。
壓縮資料流| 不支援。 已略過。
疏鬆資料流| 不支援。 已略過。
OneDrive（同步檔是稀疏流）| 不支援。
啟用了 DFS 複製的資料夾 | 不支援。

\*確保 MARS 代理有權訪問訪問加密檔所需的證書。 將跳過無法訪問的檔。

## <a name="supported-drives-or-volumes-for-backup"></a>支援的磁碟機或卷用於備份

**磁碟機/磁碟區** | **支援** | **詳細資料**
--- | --- | ---
唯讀磁碟區| 不支援 | 僅當卷是可寫時，卷複製陰影服務 （VSS） 才起作用。
離線磁碟區| 不支援 |僅當卷處於線上狀態時，VSS 才工作。
網路共用| 不支援 |卷必須是伺服器上的本地卷。
位鎖鎖定卷| 不支援 |在啟動備份之前，必須解鎖卷。
檔案系統標識| 不支援 |僅支援 NTFS。
卸除式媒體| 不支援 |所有備份項源必須具有*固定*狀態。
刪除重複資料的磁碟機 | 支援 | Azure 備份會將刪除重複資料的資料轉換成一般資料。 它優化、加密、存儲資料並將資料發送到保存庫。

## <a name="support-for-initial-offline-backup"></a>支援初始離線備份

Azure 備份支援*離線種子設定*，以便使用磁片將初始備份資料傳輸到 Azure。 如果您的初始備份可能位於 TB （TB） 的大小範圍內，則此支援非常有用。 支援離線備份的狀況：

- 直接備份運行 MARS 代理的本地電腦上的檔和資料夾。
- 從 DPM 伺服器或 MABS 備份工作負載和檔案。

離線備份不能用於系統狀態檔。

## <a name="support-for-data-restoration"></a>支援資料恢復

通過使用 Azure 備份的[即時還原](backup-instant-restore-capability.md)功能，可以在將資料複製到保存庫之前還原資料。 要備份的電腦必須運行 .NET 框架 4.5.2 或更高版本。

無法將備份還原到運行早期作業系統版本的目的電腦。 例如，可以從運行 Windows 7 的電腦獲取的備份可以在 Windows 8 或更高版本中還原。 但是，在運行 Windows 7 的電腦上無法還原從運行 Windows 8 的電腦獲取的備份。

## <a name="next-steps"></a>後續步驟

- 詳細瞭解使用[MARS 代理的備份體系結構](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
- 瞭解在[MABS 或 DPM 服務器上運行 MARS 代理](backup-support-matrix-mabs-dpm.md)時受支援的內容。
