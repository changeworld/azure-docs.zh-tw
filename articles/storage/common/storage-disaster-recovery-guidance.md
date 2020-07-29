---
title: 災害復原和儲存體帳戶容錯移轉
titleSuffix: Azure Storage
description: Azure 儲存體針對異地多餘的儲存體帳戶支援帳戶容錯移轉。 透過帳戶容錯移轉，您可以在主要端點無法使用時，為您的儲存體帳戶起始容錯移轉程序。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: e9bd2db8bcc427118a76f87e49ade422a74a11c1
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87276919"
---
# <a name="disaster-recovery-and-storage-account-failover"></a>災害復原和儲存體帳戶容錯移轉

Microsoft 致力於確保 Azure 服務皆能持續可用。 不過仍然可能會發生計畫外的服務中斷。 如果您的應用程式需要復原功能，Microsoft 建議使用異地多餘的儲存體，以便將您的資料複製到第二個區域。 此外，客戶應該要建立災害復原計畫來處理區域服務中斷。 災害復原計畫相當重要的一部分，便是準備在主要端點無法使用的情況下容錯移轉到次要端點。

Azure 儲存體針對異地多餘的儲存體帳戶支援帳戶容錯移轉。 透過帳戶容錯移轉，您可以在主要端點無法使用時，為您的儲存體帳戶起始容錯移轉程序。 容錯移轉會更新次要端點以成為儲存體帳戶的主要端點。 容錯移轉完成後，用戶端便可以開始寫入到新的主要端點。

帳戶容錯移轉適用于一般用途 v1、一般用途 v2，以及具有 Azure Resource Manager 部署的 Blob 儲存體帳戶類型。 所有公用區域都支援帳戶容錯移轉，但目前無法在主權或國家雲端中使用。

本文說明涉及帳戶容錯移轉的概念和程序，並討論如何準備儲存體帳戶，以在對客戶造成最少影響的前提下進行復原。 若要瞭解如何在 Azure 入口網站或 PowerShell 中起始帳戶容錯移轉，請參閱[起始帳戶容錯移轉](storage-initiate-account-failover.md)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>選擇正確的備援選項

Azure 儲存體會維護儲存體帳戶的多個複本，以確保持久性和高可用性。 您針對帳戶所選擇的備援選項，取決於您所需的復原程度。 針對區域中斷的保護，請設定您的帳戶以使用異地多餘的儲存體，不論次要地區是否有讀取權限：  

**異地冗余儲存體（GRS）或異地區域冗余儲存體（切換）** 會在兩個地理區域中以非同步方式複製您的資料，且兩者之間至少有數百英里的距離。 如果主要區域發生中斷，次要區域便會成為您資料的備援來源。 您可以起始容錯移轉，以將次要端點轉換為主要端點。

**讀取權限異地多餘儲存體（ra-GRS）或讀取權限異地區域-多餘儲存體（ra-切換）** 提供異地多餘的儲存體，並具有次要端點讀取權限的額外優點。 如果主要端點發生中斷，針對次要端點的讀取權限設定的應用程式會繼續從次要端點讀取，並針對高可用性而設計。 Microsoft 建議切換以取得應用程式的最高可用性和持久性。

如需 Azure 儲存體中冗余的詳細資訊，請參閱[Azure 儲存體冗余](storage-redundancy.md)。

> [!WARNING]
> 異地備援儲存體具有資料遺失的風險。 資料會以非同步方式複製到次要區域，這表示寫入主要區域的資料寫入次要區域的時間之間會有延遲。 發生中斷時，對尚未複製到次要端點的主要端點進行的寫入作業將會遺失。

## <a name="design-for-high-availability"></a>高可用性設計

您應該從一開始便針對高可用性來設計您的應用程式。 請參考這些 Azure 資源來取得設計應用程式並針對災害復原進行規劃的指引：

- [設計適用于 azure 的復原應用程式](/azure/architecture/framework/resiliency/app-design)：概述在 azure 中架構高可用性應用程式的重要概念。
- [復原檢查清單](/azure/architecture/checklist/resiliency-per-service)：驗證您的應用程式是否會針對高可用性實行最佳設計做法的檢查清單。
- [使用異地冗余來設計高可用性應用程式](geo-redundant-design.md)：建立應用程式以利用異地多餘儲存體的設計指引。
- [教學課程：建立具有 Blob 儲存體的高可用性應用程式](../blobs/storage-create-geo-redundant-storage.md)：此教學課程示範如何建立高可用性應用程式，以在失敗時自動切換端點，並模擬復原。 

此外，請記住這些適用於維護 Azure 儲存體資料之高可用性的最佳做法：

- **磁片：** 使用[Azure 備份](https://azure.microsoft.com/services/backup/)來備份 Azure 虛擬機器所使用的 VM 磁片。 也請考慮使用 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 來保護 VM 不受區域災害的影響。
- **區塊 blob：** 開啟虛[刪除](../blobs/storage-blob-soft-delete.md)來防止物件層級的刪除和覆寫，或使用[AzCopy](storage-use-azcopy.md)、 [Azure PowerShell](/powershell/module/az.storage/)或[Azure 資料手機連結庫](storage-use-data-movement-library.md)，將區塊 blob 複製到不同區域中的另一個儲存體帳戶。
- 檔案 **：** 使用[AzCopy](storage-use-azcopy.md)或[Azure PowerShell](/powershell/module/az.storage/) ，將您的檔案複製到不同區域中的另一個儲存體帳戶。
- **資料表：** 使用 [AzCopy](storage-use-azcopy.md) 將資料表資料匯出到位於不同區域的其他儲存體帳戶。

## <a name="track-outages"></a>追蹤中斷

客戶可以訂閱 [Azure 服務健康狀態儀表板](https://azure.microsoft.com/status/)來追蹤 Azure 儲存體和其他 Azure 服務的健康情況和狀態。

Microsoft 也建議您將應用程式設計成可以因應可能的寫入失敗。 您的應用程式應該以能警告您主要區域可能會發生中斷的方式公開寫入失敗。

## <a name="understand-the-account-failover-process"></a>了解帳戶容錯移轉程序

如果主要區域因任何原因而無法使用，客戶管理的帳戶容錯移轉可讓您將整個儲存體帳戶容錯移轉至次要地區。 當您強制對次要區域進行容錯移轉時，客戶端便可以在容錯移轉完成時，開始將資料寫入到次要端點。 容錯移轉通常需要花費一小時的時間。

### <a name="how-an-account-failover-works"></a>帳戶容錯移轉的運作方式

在正常情況下，用戶端會將資料寫入主要區域中的 Azure 儲存體帳戶，而該資料會以非同步方式複製到次要區域。 下圖顯示出主要區域可用的案例：

![用戶端會將資料寫入到主要區域中的儲存體帳戶](media/storage-disaster-recovery-guidance/primary-available.png)

如果主要端點因任何原因而無法使用，用戶端將無法繼續寫入到儲存體帳戶。 下圖顯示出主要端點變成無法使用，但尚未發生任何復原的案例：

![主要端點無法使用，因此用戶端無法寫入資料](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

客戶針對次要端點起始帳戶容錯移轉。 容錯移轉程序會更新由 Azure 儲存體提供的 DNS 項目，使次要端點變成您儲存體帳戶的新主要端點，如下圖所示：

![客戶針對次要端點起始帳戶容錯移轉](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

當 DNS 專案已更新，而且要求被導向至新的主要端點之後，就會還原異地多餘帳戶的寫入權限。 Blob、資料表、佇列及檔案的現有儲存體服務端點，在容錯移轉後會保持相同。

> [!IMPORTANT]
> 容錯移轉完成之後，儲存體帳戶會在新的主要端點中被設定為本地備援。 若要繼續複寫到新的次要資料庫，請重新設定該帳戶的地理位置冗余。
>
> 請記住，轉換 LRS 帳戶以使用異地冗余會產生成本。 這項成本適用于在容錯移轉後更新新主要區域中的儲存體帳戶。  

### <a name="anticipate-data-loss"></a>預期資料遺失

> [!CAUTION]
> 帳戶容錯移轉通常會產生一些資料遺失。 請務必了解起始帳戶容錯移轉的含意。  

由於資料是以非同步方式從主要區域寫入到次要區域，因此在主要區域的寫入複製到次要區域之前，一律會有延遲。 如果主要區域變得無法使用，最近的寫入可能尚未複製到次要區域。

當您強制進行容錯移轉時，隨著次要區域變成新的主要區域，且儲存體帳戶被設定為本地備援，舊主要區域中的所有資料都會遺失。 當容錯移轉發生時，已複製到次要資料庫的所有資料都會維持不變。 不過，寫入主要複本且尚未複製到次要複本的任何資料，都會永久遺失。

[上次同步時間]**** 屬性能指出來自主要區域的資料保證已寫入次要區域的最近時間。 在 [上次同步時間] 之前所寫入的所有資料都會存在於次要區域上，但在 [上次同步時間] 之後所寫入的資料則有可能尚未寫入次要區域，並可能會遺失。 在發生中斷時，請使用此屬性來估計起始帳戶容錯移轉可能會導致的資料遺失程度。

作為最佳做法，請將應用程式設計成可以使用 [上次同步時間] 來評估預期的資料遺失。 例如，如果您記錄所有寫入作業，則您可以比較最後一次的寫入作業與 [上次同步時間]，來判斷哪些寫入尚未被同步到次要區域。

如需有關檢查 [**上次同步時間**] 屬性的詳細資訊，請參閱[檢查儲存體帳戶的上次同步處理時間屬性](last-sync-time-get.md)。

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>容錯回復到原始主要區域時請小心

在您從主要區域容錯移轉到次要區域之後，您的儲存體帳戶在新的主要區域中會被設定為本地備援。 接著，您可以再次設定此帳戶以進行異地冗余。 當帳戶在容錯移轉之後再次設定為異地冗余時，新的主要區域會立即開始將資料複製到新的次要區域，也就是原始容錯移轉之前的主要區域。 不過，可能需要一些時間，主要複本中的現有資料才會完整複製到新的次要資料庫。

在儲存體帳戶被重新設定為異地備援之後，您可以從新的主要區域針對新的次要區域起始另一個容錯移轉。 在此情況下，容錯移轉前的原始主要區域將會再次變成主要區域，並被設定為本地備援。 這會使所有位於容錯移轉前主要區域 (原始的次要區域) 中的所有資料都遺失。 如果在容錯回復之前，儲存體帳戶中的大部分資料都尚未複製到新的次要資料庫，您可能會受到重大資料遺失的影響。

若要避免重大資料遺失，請在容錯回復之前檢查 [上次同步時間]**** 屬性。 比較 [上次同步時間] 與上次資料寫入到新主要區域的時間，以評估預期的資料遺失。 

## <a name="initiate-an-account-failover"></a>初始化帳戶容錯移轉

您可以從 Azure 入口網站、PowerShell、Azure CLI 或 Azure 儲存體資源提供者 API 來起始帳戶容錯移轉。 如需如何起始容錯移轉的詳細資訊，請參閱[起始帳戶容錯移轉](storage-initiate-account-failover.md)。

## <a name="additional-considerations"></a>其他考量

請參閱本節所述的其他考慮，以瞭解當您強制執行容錯移轉時，您的應用程式和服務可能會受到什麼影響。

### <a name="storage-account-containing-archived-blobs"></a>包含已封存 blob 的儲存體帳戶

包含封存 blob 的儲存體帳戶支援帳戶容錯移轉。 完成容錯移轉之後，所有封存的 blob 都必須解除凍結到線上層，帳戶才能設定為異地冗余。

### <a name="storage-resource-provider"></a>儲存體資源提供者

在容錯移轉完成之後，用戶端可以再次讀取及寫入新主要區域中 Azure 儲存體資料。 不過，Azure 儲存體資源提供者不會故障，因此資源管理作業仍然必須在主要區域中進行。 如果主要區域無法使用，您將無法在儲存體帳戶上執行管理作業。

由於 Azure 儲存體資源提供者不會容錯移轉，因此在容錯移轉完成之後， [Location](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location)屬性會傳回原始的主要位置。

### <a name="azure-virtual-machines"></a>Azure 虛擬機器

Azure 虛擬機器 (VM) 不會隨著帳戶容錯移轉一起容錯移轉。 如果主要區域變得無法使用，且您容錯移轉到次要區域，您必須在容錯移轉後重新建立所有 VM。 此外，也有可能與帳戶容錯移轉相關聯的資料遺失。 Microsoft 建議您針對 Azure 中的虛擬機器提供下列[高可用性和嚴重](../../virtual-machines/windows/manage-availability.md)損壞[修復](../../virtual-machines/windows/backup-recovery.md)指引。

### <a name="azure-unmanaged-disks"></a>Azure 非受控磁碟

作為最佳做法，Microsoft 建議將非受控磁碟轉換成受控磁碟。 不過，如果您需要容錯移轉包含附加至 Azure VM 之非受控磁碟的帳戶，您必須在起始容錯移轉之前關閉該 VM。

非受控磁碟會以分頁 Blob 的形式儲存在 Azure 儲存體中。 當 VM 在 Azure 中執行時，附加至該 VM 的所有非受控磁碟都會被租用。 帳戶容錯移轉無法在 Blob 上存在租用的情況下繼續進行。 若要執行容錯移轉，請按照下列步驟進行：

1. 在開始前，請記下所有非受控磁碟的名稱、其邏輯單元編號 (LUN)，以及其所附加至的 VM。 這麼做將會使於容錯移轉後重新附加磁碟的工作變得較為輕鬆。
2. 關閉 VM。
3. 刪除 VM，但保留非受控磁碟的 VHD 檔案。 請記下您刪除 VM 的時間。
4. 等候 [上次同步時間]**** 更新，並且是比您刪除 VM 更晚的時間。 此步驟很重要，因為在發生容錯移轉時，如果次要端點尚未搭配 VHD 檔案完全更新，則 VM 在新的主要區域中可能無法正確執行。
5. 起始帳戶容錯移轉。
6. 等候帳戶容錯移轉完成，且次要區域已成為新的主要區域。
7. 在新的主要區域中建立 VM，並重新附加 VHD。
8. 啟動新的 VM。

請記住，在 VM 關閉時，儲存在暫時磁碟中的所有資料都會遺失。

## <a name="unsupported-features-and-services"></a>不支援的功能和服務

帳戶容錯移轉不支援下列功能和服務：

- Azure 檔案同步不支援儲存體帳戶容錯移轉。 不應該容錯移轉包含在 Azure 檔案同步中作為雲端端點使用之 Azure 檔案共用的儲存體帳戶。 這麼做將導致同步停止運作，且可能會在新分層的檔案中產生未預期的資料遺失。
- 目前不支援 ADLS Gen2 儲存體帳戶（已啟用階層命名空間的帳戶）。
- 無法容錯移轉包含進階區塊 Blob 的儲存體帳戶。 支援進階區塊 Blob 的儲存體帳戶目前不支援異地備援。
- 包含已啟用任何 WORM 不存在[原則](../blobs/storage-blob-immutable-storage.md)之容器的儲存體帳戶，無法進行故障切換。 已解除鎖定/鎖定以時間為基礎的保留或合法保存原則會防止容錯移轉，以維持合規性。

## <a name="copying-data-as-an-alternative-to-failover"></a>將複製資料作為容錯移轉的替代項目

如果您的儲存體帳戶已設定為對次要複本的讀取存取權，則您可以將應用程式設計為從次要端點讀取。 如果您不想在主要區域發生中斷的情況下進行容錯移轉，您可以使用 [AzCopy](storage-use-azcopy.md)、[Azure PowerShell](/powershell/module/az.storage/) 或 [Azure Data Movement 程式庫](../common/storage-use-data-movement-library.md) \(英文\) 等工具，將次要區域中的資料從儲存體帳戶複製到位於未受影響區域中的另一個儲存體帳戶。 您接著可以將應用程式指向該儲存體帳戶，以取得讀取和寫入上的可用性。

> [!CAUTION]
> 帳戶容錯移轉不應當做資料移轉策略的一部分使用。

## <a name="microsoft-managed-failover"></a>Microsoft 管理的容錯移轉

在區域因嚴重災害而遺失的極端情況下，Microsoft 可能會起始區域容錯移轉。 在此情況下，您不需要採取任何動作。 在 Microsoft 管理的容錯移轉完成之前，您將無法取得儲存體帳戶的寫入權限。 如果您的儲存體帳戶設定為 GRS 或 RA-切換，您的應用程式可以從次要區域讀取。

## <a name="see-also"></a>另請參閱

- [使用異地備援來設計高可用性的應用程式](geo-redundant-design.md)
- [初始化帳戶容錯移轉](storage-initiate-account-failover.md)
- [檢查儲存體帳戶的上次同步時間屬性](last-sync-time-get.md)
- [教學課程：建置採用 Blob 儲存體的高可用性應用程式](../blobs/storage-create-geo-redundant-storage.md)
