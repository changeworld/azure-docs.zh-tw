---
title: Microsoft Azure 備份伺服器的新功能
description: Microsoft Azure 備份伺服器可提供您經過強化的備份功能，讓您保護 VM、檔案和資料夾以及工作負載等項目。
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 39050d0f658e29b82f270f1fe53026e2fb80bfa1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332758"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>Microsoft Azure 備份 Server (MABS 的新功能) 

## <a name="whats-new-in-mabs-v3-ur1"></a>MABS V3 UR1 的新功能

Microsoft Azure 備份 Server (MABS) 第3版 UR1 是最新的更新，其中包含重大錯誤修正和其他功能和增強功能。 若要查看已修正的錯誤清單和 MABS V3 UR1 的安裝指示，請參閱知識庫文章 [4534062](https://support.microsoft.com/help/4534062)。

>[!NOTE]
>32位保護代理程式的支援已被 MABS v3 UR1 取代。 請參閱 [32 位保護代理程式](#32-bit-protection-agent-deprecation)淘汰。

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>使用 Ssd 以分層式儲存體進行更快速的備份

MABS V2 引進了 [Modern Backup Storage](backup-mabs-add-storage.md) (mb) ，以改善儲存使用量和效能。 MBS 使用 ReFS 作為基礎的檔案系統，MBS 旨在利用混合式儲存體 (例如階層式儲存體)。

為了達到規模和效能（MB），我們建議使用 MABS V3 UR1 作為階層式磁片區的磁片區，在快閃儲存體 (SSD) 的整體儲存體) 中使用一 (小部分，以搭配 DPM HDD 儲存體。 具有階層式儲存體的 MABS V3 UR1 可提供50-70% 的備份速度。 如需設定階層式存放裝置的步驟，請參閱 DPM 文章以 [分層式儲存體設定 mb](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage) 。

### <a name="support-for-refs-volumes"></a>ReFS 磁碟區支援

您可以使用 MABS V3 UR1 來備份 refs 磁片區，以及部署在 ReFS 磁片區上的工作負載。 您可以備份部署在 ReFS 磁片區上的下列工作負載：

* 作業系統 (64 位元) ：Windows Server 2019、2016、2012 R2、2012。
* SQL Server：SQL Server 2019、SQL Server 2017、2016。
* Exchange：Exchange 2019、2016。
* SharePoint：具有最新 SP 的 SharePoint 2019、2016。

>[!NOTE]
> MABS V3 支援儲存在 ReFS 磁片區上的 Hyper-v Vm 備份

>須知我們發現備份重復資料刪除 ReFS 磁片區有幾個問題。 我們正在修正這些問題，並會在有可用的修正時立即更新此區段。 在那之前，我們將從 MABSv3 UR1 中移除重復資料刪除 ReFS 磁片區的備份支援。

### <a name="azure-vmware-solution-protection-support"></a>Azure VMware 解決方案保護支援

使用 MABS v3 UR1，您現在可以保護部署在 [Azure VMware 解決方案](../azure-vmware/index.yml)中的虛擬機器。

### <a name="vmware-parallel-backups"></a>VMWare 平行備份

使用 MABS V3 UR1，單一保護群組中的所有 VMware Vm 備份都是平行的，導致 VM 備份的速度加快25%。
使用舊版的 MABS 時，只會在保護群組上執行平行備份。 使用 MABS V3 UR1，VMware 差異複寫作業會以平行方式執行。 依預設，要平行執行的工作數目會設定為8。 深入瞭解 [VMware 平行備份](backup-azure-backup-server-vmware.md#vmware-parallel-backups)。

### <a name="disk-exclusion-for-vmware-vm-backup"></a>VMware VM 備份的磁碟排除

使用 MABS V3 UR1，您可以從 VMware VM 備份中排除特定磁片。 深入瞭解如何 [從 VMWARE VM 備份中排除磁片](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup)。  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>支援額外的驗證層以刪除線上備份

使用 MABS V3 UR1，會為重要作業新增額外的驗證層。 當您執行「 **停止保護」與「刪除資料** 」作業時，系統會提示您輸入安全性 PIN 碼。

### <a name="offline-backup-improvements"></a>離線備份改進

MABS v3 UR1 改善使用 Azure 匯入/匯出服務進行離線備份的體驗。 如需詳細資訊，請參閱 [此處](./backup-azure-backup-server-import-export.md)的更新步驟。

>[!NOTE]
>此更新也會在 MABS 中使用 Azure 資料箱，來提供離線備份的預覽。 [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com)若要深入瞭解，請聯絡。

### <a name="new-cmdlet-parameter"></a>新 Cmdlet 參數

MABS V3 UR1 包含新的參數 **[-CheckReplicaFragmentation]**。 新的參數會計算複本的片段百分比，並且包含在 **DPMDatasourceReplica 指令程式** 中。

### <a name="32-bit-protection-agent-deprecation"></a>32-位保護代理程式淘汰

使用 MABS v3 UR1，不再支援32位保護代理程式的支援。 將 MABS v3 伺服器升級至 UR1 之後，您將無法保護32位的工作負載。 任何現有的32位保護代理程式都會處於停用狀態，且已排程的備份將會失敗，並出現 **代理程式已停** 用的錯誤。 如果您想要保留這些代理程式的備份資料，可以使用 [保留資料] 選項停止保護。 否則，可以移除保護代理程式。

>[!NOTE]
>請參閱 [更新的保護對照表](./backup-mabs-protection-matrix.md) ，以瞭解支援的工作負載，以使用 MABS UR 1 進行保護。

## <a name="whats-new-in-mabs-v3-rtm"></a>MABS V3 RTM 的新功能

Microsoft Azure 備份 Server 第3版 (MABS V3) 包括重大錯誤修正、Windows Server 2019 支援、SQL 2017 支援，以及其他功能和增強功能。 若要檢視已修正的錯誤清單及 MABS V3 的安裝指示，請參閱 KB 文章 [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3)。

MABS V3 包含下列功能︰

### <a name="volume-to-volume-migration"></a>磁碟區對磁碟區移轉

藉由 MABS V2 中的 Modern Backup Storage (MBS) 功能，我們已推出工作負載感知儲存體，您可以在其中設定特定工作負載，使其根據儲存體屬性來備份到特定儲存體。 不過，完成設定後，您可能會需要將特定資料來源的備份移至其他儲存體，以最佳化資源使用率。 MABS V3 提供您遷移備份的功能，並將其設定為以 [三個步驟](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842)儲存至不同的磁片區。

### <a name="prevent-unexpected-data-loss"></a>避免非預期的資料遺失

在企業中，MABS 會由一組系統管理員來管理。 雖然有備份適用的儲存體指導方針，但如果提供錯誤磁碟區給 MABS 作為備份儲存體，則可能會遺失重要資料。 透過 MABS V3，您可以使用 [這些 PowerShell Cmdlet](./backup-mabs-add-storage.md)將這些磁片區設定為無法儲存的磁片區，以防止這類案例。

### <a name="custom-size-allocation"></a>自訂大小配置

Modern Backup Storage (MBS) 耗用的儲存體很少，並且只在有需要時取用儲存體。 若要這樣做，MABS 會計算在設定保護時所要備份的資料大小。 不過，如果要同時備份許多檔案和資料夾 (例如備份檔案伺服器)，計算大小可能需要很長的時間。 使用 MABS V3，您可以將 MABS 設定為依預設接受磁片區大小，而不是計算每個檔案的大小，這樣可節省時間。

### <a name="optimized-cc-for-rct-vms"></a>針對 RCT VM 最佳化的 CC

MABS 會使用 RCT (Hyper-V 中的原生變更追蹤)，以降低 VM 損毀時需花時間檢查一致性的需求。 RCT 提供的復原優於 VSS 快照集備份提供的變更追蹤。 在任何一致性檢查期間，MABS V3 只會傳送變更的資料，藉此進一步最佳化網路和儲存體耗用量。

### <a name="support-to-tls-12"></a>支援 TLS 1.2

TLS 1.2 是 Microsoft 建議的安全通訊方式，具有最佳加密技術。 MABS 現在支援在 MABS 和受保護的伺服器間使用 TLS 1.2，以進行憑證型驗證和雲端備份。

### <a name="vmware-vm-protection-support"></a>VMware VM 保護支援

生產環境部署現在支援 VMware VM 備份。 MABS V3 提供下列功能來保護 VMware VM：

* 支援 vCenter 和 ESXi 6.5 (也支援 5.5 和 6.0)。
* VMware VM 到雲端的自動保護。 如果將新的 VMware Vm 新增至受保護的資料夾，系統就會自動將它們保護到磁片和雲端。
* VMware 替代位置復原的復原效率改善。

### <a name="sql-2017-support"></a>SQL 2017 支援

MABS V3 可以透過以 SQL 2017 作為 MABS 資料庫的方式來安裝。 您可以將 SQL 伺服器從 SQL 2016 升級至 SQL 2017，或進行全新安裝。 您也可以使用 MABS V3 在叢集和非叢集環境中備份 SQL 2017 工作負載。

### <a name="windows-server-2019-support"></a>Windows Server 2019 支援

MABS V3 可安裝在 Windows Server 2019 上。 若要搭配 WS2019 使用 MABS V3，您可以在安裝/升級至 MABS V3 之前將您的 OS 升級至 WS2019，或在 WS2016 上安裝/升級 V3 之後升級作業系統。

MABS V3 是完整版本，可以安裝在 Windows Server 2016、Windows Server 2019，或直接從 MABS V2 升級。 在安裝或升級為備份伺服器 v3 之前，請先閱讀安裝必要條件。
您可以在[此處](./backup-azure-microsoft-azure-backup.md#software-package)找到有關 MABS 安裝/升級步驟的詳細資訊。

> [!NOTE]
>
> MABS 具有和 System Center Data Protection Manager 一樣的程式碼基底。 MABS v3 相當於 Data Protection Manager 1807。 MABS v3 UR1 相當於 Data Protection Manager 2019 UR1。

## <a name="next-steps"></a>後續步驟

了解如何準備您的伺服器或開始保護工作負載：

* [準備備份伺服器工作負載](backup-azure-microsoft-azure-backup.md)
* [使用備份伺服器來備份 VMware 伺服器](backup-azure-backup-server-vmware.md)
* [使用備份伺服器來備份 SQL Server](backup-azure-sql-mabs.md)
* [在備份伺服器中使用新式備份儲存體](backup-mabs-add-storage.md)
