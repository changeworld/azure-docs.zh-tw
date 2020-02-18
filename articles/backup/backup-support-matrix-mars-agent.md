---
title: MARS 代理程式的支援矩陣
description: 本文摘要說明當您備份執行 Microsoft Azure 復原服務（MARS）代理程式的電腦時的 Azure 備份支援。
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 8f5ce33b5057b11caa33c0ae80cf72e1b13da5d0
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425012"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>使用 Microsoft Azure 復原服務 (MARS) 代理程式進行備份的支援矩陣

您可以使用[Azure 備份服務](backup-overview.md)來備份內部部署機器和應用程式，以及備份 Azure 虛擬機器（vm）。 本文摘要說明當您使用 Microsoft Azure 復原服務（MARS）代理程式來備份電腦時的支援設定和限制。

## <a name="the-mars-agent"></a>MARS 代理程式

Azure 備份使用 MARS 代理程式，將資料從內部部署機器和 Azure Vm 備份至 Azure 中的備份復原服務保存庫。 MARS 代理程式可以：

- 在內部部署 Windows 機器上執行，使其可以直接備份至 Azure 中的備份復原服務保存庫。
- 在 Windows Vm 上執行，讓它們可以直接備份到保存庫。
- 在 Microsoft Azure 備份 Server （MABS）或 System Center Data Protection Manager （DPM）伺服器上執行。 在此案例中，機器和工作負載會備份至 MABS 或 DPM 服務器。 MARS 代理程式接著會將此伺服器備份至 Azure 中的保存庫。

> [!NOTE]
>Azure 備份不支援自動調整日光節約時間（DST）的時鐘。 修改原則以確保將日光節約納入考慮，以避免實際時間和排程備份時間之間的差異。

您的備份選項取決於代理程式的安裝位置。 如需詳細資訊，請參閱[使用 MARS 代理程式 Azure 備份架構](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。 如需有關 MABS 和 DPM 備份架構的詳細資訊，請參閱[備份至 dpm 或 MABS](backup-architecture.md#architecture-back-up-to-dpmmabs)。 另請參閱備份架構的[需求](backup-support-matrix-mabs-dpm.md)。

**安裝** | **詳細資料**
--- | ---
下載最新的 MARS 代理程式 | 您可以從保存庫或[從這裡直接下載](https://aka.ms/azurebackup_agent)最新版本的代理程式。
直接在電腦上安裝 | 您可以直接在內部部署 Windows 伺服器或執行任何[支援作業系統](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)的 windows VM 上安裝 MARS 代理程式。
在備份伺服器上安裝 | 當您將 DPM 或 MABS 設定為備份至 Azure 時，您可以在伺服器上下載並安裝 MARS 代理程式。 您可以在備份伺服器支援矩陣中，將代理程式安裝在[支援的作業系統](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)上。

> [!NOTE]
> 根據預設，已啟用備份的 Azure Vm 會安裝 Azure 備份延伸模組。 此擴充功能會備份整個 VM。 如果您想要備份特定的資料夾和檔案，而不是完整 VM，您可以在 Azure VM 上安裝 MARS 代理程式，讓其與擴充功能一起執行。
> 當您在 Azure VM 上執行 MARS 代理程式時，它會備份 VM 上暫存儲存體中的檔案或資料夾。 如果從暫存儲存體中移除檔案或資料夾，或已移除暫存儲存體，則備份會失敗。

## <a name="cache-folder-support"></a>快取資料夾支援

當您使用 MARS 代理程式來備份資料時，代理程式會取得資料的快照集，並將它儲存在本機快取資料夾中，然後再將資料傳送至 Azure。 快取（臨時）資料夾有數個需求：

**Cache** | **詳細資料**
--- | ---
大小 |  快取資料夾中的可用空間至少應為備份資料整體大小的5% 到10%。
Location | 快取資料夾必須存放在要備份的電腦上，而且必須在線上。 快取資料夾不可以在網路共用、卸載式媒體或離線磁片區上。
資料夾 | 快取資料夾不應在已重復資料刪除的磁片區上，或在已壓縮、稀疏或具有重新分析點的資料夾中進行加密。
位置變更 | 您可以藉由停止備份引擎（`net stop bengine`），並將快取資料夾複製到新的磁片磁碟機，來變更快取位置。 （請確認新的磁片磁碟機具有足夠的空間。）然後將**HKLM\SOFTWARE\Microsoft\Windows Azure 備份**（**config/ScratchLocation**和**config/CloudBackupProvider/ScratchLocation**）下的兩個登錄專案更新為新位置，然後重新開機引擎。

## <a name="networking-and-access-support"></a>網路功能與存取支援

### <a name="url-and-ip-access"></a>URL 和 IP 存取

MARS 伺服器需要存取這些 URL：

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

和這些 IP 位址：

- 20.190.128.0/18
- 40.126.0.0/18

對上述所有 Url 和 IP 位址的存取，都會使用埠443上的 HTTPS 通訊協定。

### <a name="throttling-support"></a>節流支援

**功能** | **詳細資料**
--- | ---
頻寬控制 | 支援。 在 MARS 代理程式中，使用 [**變更屬性**] 來調整頻寬。
網路節流 | 不適用於執行 Windows Server 2008 R2、Windows Server 2008 SP2 或 Windows 7 的備份電腦。

## <a name="support-for-direct-backups"></a>直接備份的支援

>[!NOTE]
> MARS 代理程式不支援 Windows Server Core Sku。

在下列執行的作業系統上，您可以使用 MARS 代理程式直接備份至 Azure：

1. 內部部署 Windows 伺服器
2. 執行 Windows 的 Azure VM

作業系統必須是64位，且應執行最新的服務套件和更新。 下表摘要說明這些作業系統：

**作業系統** | **檔案/資料夾** | **系統狀態** | **軟體/模組需求**
--- | --- | --- | ---
Windows 10 (企業版、專業版、家用版) | 是 | 否 |  檢查對應的伺服器版本是否有軟體/模組需求
Windows 8.1 (企業版、專業版)| 是 |否 | 檢查對應的伺服器版本是否有軟體/模組需求
Windows 8 (企業版、專業版) | 是 | 否 | 檢查對應的伺服器版本是否有軟體/模組需求
Windows 7 (Ultimate、企業版、專業版、家用進階版/基本版、入門版) | 是 | 否 | 檢查對應的伺服器版本是否有軟體/模組需求
Windows Server 2016 (Standard、Datacenter、Essentials) | 是 | 是 | -.NET 4。5 <br> -Windows PowerShell <br> -最新相容的 Microsoft VC + + 可轉散發套件 <br> -Microsoft Management Console （MMC）3。0
Windows Server 2012 R2(Standard、Datacenter、Essentials) | 是 | 是 | -.NET 4。5 <br> -Windows PowerShell <br> -最新相容的 Microsoft VC + + 可轉散發套件 <br> -Microsoft Management Console （MMC）3。0
Windows Server 2012 (Standard、Datacenter、Essentials) | 是 | 是 |-.NET 4。5 <br> -Windows PowerShell <br> -最新相容的 Microsoft VC + + 可轉散發套件 <br> -Microsoft Management Console （MMC）3。0 <br> -部署映射服務與管理（DISM.EXE）
Windows Storage Server 2016/2012 R2/2012 （Standard、Workgroup） | 是 | 否 | -.NET 4。5 <br> -Windows PowerShell <br> -最新相容的 Microsoft VC + + 可轉散發套件 <br> -Microsoft Management Console （MMC）3。0
Windows Server 2019 (Standard、Datacenter、Essentials) | 是 | 是 | -.NET 4。5 <br> -Windows PowerShell <br> -最新相容的 Microsoft VC + + 可轉散發套件 <br> -Microsoft Management Console （MMC）3。0

如需詳細資訊，請參閱[支援的 MABS 和 DPM 作業系統](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)。

## <a name="backup-limits"></a>備份限制

### <a name="size-limits"></a>大小限制

Azure 備份限制可以備份的檔案或資料夾資料來源的大小。 您從單一磁片區備份的專案不能超過此表中摘要說明的大小：

**作業系統** | **大小限制**
--- | ---
Windows Server 2012 或更新版本 |54,400 GB
Windows Server 2008 R2 SP1 |1700 GB
Windows Server 2008 SP2| 1700 GB
Windows 8 或更新版本| 54,400 GB
Windows 7| 1700 GB

### <a name="other-limitations"></a>其他限制

- MARS 不支援以相同名稱對單一保存庫的多部電腦進行保護。

## <a name="supported-file-types-for-backup"></a>支援的備份檔案類型

**型別** | **支援**
--- | ---
已加密| 支援。
Compressed | 支援。
疏鬆 | 支援。
已壓縮和疏鬆 |支援。
永久連結| 不支援。 略.
重新分析點| 不支援。 略.
加密和疏鬆 |不支援。 略.
壓縮資料流| 不支援。 略.
疏鬆資料流| 不支援。 略.
OneDrive （已同步處理的檔案為稀疏資料流程）| 不支援。
已啟用 DFS 複寫的資料夾 | 不支援。

## <a name="supported-drives-or-volumes-for-backup"></a>支援備份的磁片磁碟機或磁片區

**磁碟機/磁碟區** | **支援** | **詳細資料**
--- | --- | ---
唯讀磁碟區| 不支援 | 磁片區複製陰影服務（VSS）只有在磁片區可寫入時才會運作。
離線磁碟區| 不支援 |只有當磁片區在線上時，VSS 才會運作。
網路共用| 不支援 |磁片區必須是伺服器上的本機。
BitLocker 鎖定的磁片區| 不支援 |必須先解除鎖定磁片區，才能開始備份。
檔案系統識別| 不支援 |僅支援 NTFS。
卸除式媒體| 不支援 |所有備份專案來源都必須具有*固定*狀態。
刪除重複資料的磁碟機 | 支援 | Azure 備份會將刪除重複資料的資料轉換成一般資料。 它會將資料優化、加密、儲存和傳送至保存庫。

## <a name="support-for-initial-offline-backup"></a>支援初始離線備份

Azure 備份支援*離線植*入，以使用磁片將初始備份資料傳輸到 Azure。 如果您的初始備份可能是大小範圍為 tb （Tb），這項支援將會很有説明。 支援離線備份的狀況：

- 直接備份執行 MARS 代理程式之內部部署電腦上的檔案和資料夾。
- 從 DPM 伺服器或 MABS 備份工作負載和檔案。

離線備份無法用於系統狀態檔案。

## <a name="support-for-data-restoration"></a>資料還原的支援

藉由使用 Azure 備份的「[立即還原](backup-instant-restore-capability.md)」功能，您可以在將資料複製到保存庫之前先將它還原。 您要備份的電腦必須執行 .NET Framework 4.5.2 或更高版本。

備份無法還原到執行舊版作業系統的目的電腦。 例如，從執行 Windows 7 的電腦建立的備份可以在 Windows 8 或更新版本上還原。 但是，從執行 Windows 8 的電腦建立的備份無法在執行 Windows 7 的電腦上還原。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[使用 MARS 代理程式的備份架構](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
- 瞭解當您[在 MABS 或 DPM 服務器上執行 MARS 代理程式](backup-support-matrix-mabs-dpm.md)時，所支援的功能。
