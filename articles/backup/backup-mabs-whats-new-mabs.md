---
title: Microsoft Azure 備份伺服器的新功能
description: Microsoft Azure 備份伺服器可提供您經過強化的備份功能，讓您保護 VM、檔案和資料夾以及工作負載等項目。 了解如何安裝或升級為 Azure 備份伺服器 V3。
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 61430ce06d3e441fcfe0443eaaf5de3755b04624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582801"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Microsoft Azure 備份伺服器的新功能

Microsoft Azure 備份伺服器版本 3 （MABS V3） 是最新的升級，包括嚴重錯誤修復、Windows Server 2019 支援、SQL 2017 支援以及其他功能和增強功能。 若要檢視已修正的錯誤清單及 MABS V3 的安裝指示，請參閱 KB 文章 [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3)。

MABS V3 包含下列功能︰

## <a name="volume-to-volume-migration"></a>磁碟區對磁碟區移轉

藉由 MABS V2 中的 Modern Backup Storage (MBS) 功能，我們已推出工作負載感知儲存體，您可以在其中設定特定工作負載，使其根據儲存體屬性來備份到特定儲存體。 不過，完成設定後，您可能會需要將特定資料來源的備份移至其他儲存體，以最佳化資源使用率。 MABS V3 使您能夠遷移備份，並將它們配置為在[三個步驟](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842)中存儲到不同的卷。

## <a name="prevent-unexpected-data-loss"></a>避免非預期的資料遺失

在企業中，MABS 會由一組系統管理員來管理。 雖然有備份適用的儲存體指導方針，但如果提供錯誤磁碟區給 MABS 作為備份儲存體，則可能會遺失重要資料。 透過 MABS V3，您可以使用[這些 PowerShell Cmdlet](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage) 將那些磁碟區設為不可作為儲存體的磁碟區，以避免這類情形。

## <a name="custom-size-allocation"></a>自訂大小分配

Modern Backup Storage (MBS) 耗用的儲存體很少，並且只在有需要時取用儲存體。 為達此目的，在為即將要備份的資料設定保護時，MABS 會計算該資料的大小。 不過，如果要同時備份許多檔案和資料夾 (例如備份檔案伺服器)，計算大小可能需要很長的時間。 透過 MABS V3，您可以將 MABS 設定為接受預設的磁碟區大小，而不是計算每個檔案的大小，藉此節省時間。

## <a name="optimized-cc-for-rct-vms"></a>針對 RCT VM 最佳化的 CC

MABS 會使用 RCT (Hyper-V 中的原生變更追蹤)，以降低 VM 損毀時需花時間檢查一致性的需求。 比起 VSS 快照集式備份提供的變更追蹤，RCT 提供更好的復原力。 在任何一致性檢查期間，MABS V3 只會傳送變更的資料，藉此進一步最佳化網路和儲存體耗用量。

## <a name="support-to-tls-12"></a>支援 TLS 1.2

TLS 1.2 是 Microsoft 建議的安全通訊方式，具有最佳加密技術。 MABS 現在支援在 MABS 和受保護的伺服器間使用 TLS 1.2，以進行憑證型驗證和雲端備份。

## <a name="vmware-vm-protection-support"></a>VMware VM 保護支援

生產環境部署現在支援 VMware VM 備份。 MABS V3 提供下列功能來保護 VMware VM：

- 支援 vCenter 和 ESXi 6.5 (也支援 5.5 和 6.0)。
- VMware VM 到雲端的自動保護。 如果將新的 VMware VM 新增至受保護的資料夾，這些 VMware VM 會自動在磁碟和雲端中受到保護。
- VMware 替代位置復原的復原效率改善。

## <a name="sql-2017-support"></a>SQL 2017 支援

MABS V3 可以透過以 SQL 2017 作為 MABS 資料庫的方式來安裝。 您可以將 SQL 伺服器從 SQL 2016 升級至 SQL 2017，或進行全新安裝。 您也可以使用 MABS V3 在叢集和非叢集環境中備份 SQL 2017 工作負載。

## <a name="windows-server-2019-support"></a>Windows Server 2019 支援

MABS V3 可安裝在 Windows Server 2019 上。 若要搭配使用 WS2019 和 MABS V3，您可以在安裝/升級到 MABS V3 之前，將作業系統升級到 WS2019，或是，您可以在 WS2016 上安裝/升級 V3 後，再升級作業系統。

MABS V3 是完整版本，可以安裝在 Windows Server 2016、Windows Server 2019，或直接從 MABS V2 升級。 在安裝或升級為備份伺服器 v3 之前，請先閱讀安裝必要條件。
您可以在[此處](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package)找到有關 MABS 安裝/升級步驟的詳細資訊。

> [!NOTE]
>
> MABS 具有和 System Center Data Protection Manager 一樣的程式碼基底。 MABS v3 相當於 Data Protection Manager 1807。

## <a name="next-steps"></a>後續步驟

了解如何準備您的伺服器或開始保護工作負載：

- [MABS V3 中的已知問題](backup-mabs-release-notes-v3.md)
- [準備備份伺服器工作負載](backup-azure-microsoft-azure-backup.md)
- [使用備份伺服器來備份 VMware 伺服器](backup-azure-backup-server-vmware.md)
- [使用備份伺服器來備份 SQL Server](backup-azure-sql-mabs.md)
- [在備份伺服器中使用新式備份儲存體](backup-mabs-add-storage.md)
