---
title: Azure 磁碟儲存體概觀
description: Azure 受控磁片的總覽，可在您使用 Vm 時為您處理儲存體帳戶。
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: contperf-fy21q1
ms.openlocfilehash: 31fe62aabfff77342726158c3b72b61afb518682
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031516"
---
# <a name="introduction-to-azure-managed-disks"></a>Azure 受控磁碟簡介

Azure 受控磁碟是由 Azure 管理並與 Azure 虛擬機器搭配使用的區塊層級儲存體磁碟區。 受控磁碟就像是內部部署伺服器中虛擬化的實體磁碟。 使用受控磁碟時，您只需要指定磁碟大小、磁碟類型，以及物件磁碟。 佈建磁碟後，Azure 就會處理其餘事項。

這些可用的磁碟類型為 Ultra 磁碟、進階固態硬碟 (SSD)、標準 SSD 和標準硬碟 (HDD)。 如需每種個別磁碟類型的相關資訊，請參閱[選取適用於 IaaS VM 的磁碟類型](disks-types.md)。

## <a name="benefits-of-managed-disks"></a>受控磁碟的好處

讓我們來複習一下使用受控磁碟時的一些好處。

### <a name="highly-durable-and-available"></a>高耐久性及可用性

受控磁碟設計成確保可用性達 99.999%。 受控磁碟達到此目標的方式，是提供三個您資料的複本，而達到高持久性。 如果一個或甚至兩個複本遇到問題，其餘複本有助於確保資料的持續性，以及遇到失敗時的高容錯力。 此結構讓 Azure 針對以基礎結構即服務 (IaaS) 磁碟穩定地展現企業級持久性，提供領先界業的年度零失敗率。

### <a name="simple-and-scalable-vm-deployment"></a>簡單且可調整的 VM 部署

您可以使用受控磁碟在每個區域的一個訂用帳戶中建立最多 50,000 個同類型虛擬機器 **磁碟**，這可讓您在單一訂用帳戶中建立數千部 **虛擬機器**。 這項功能也可讓您使用 Marketplace 映像，在一個虛擬機器擴展集內建立最多 1,000 部虛擬機器，進一步增加[虛擬機器擴展集](../virtual-machine-scale-sets/overview.md)的延展性。

### <a name="integration-with-availability-sets"></a>整合可用性設定組

受控磁碟會與可用性設定組整合，以確定[可用性設定組中的虛擬機器](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)磁碟彼此充分隔離，以避免出現單一失敗點。 磁碟會自動放置在不同的儲存體縮放單位 (戳記)。 如果因為硬體或軟體失敗造成戳記失敗，則只有磁碟在這些戳記上的 VM 執行個體才會失敗。 例如，假設您的應用程式在五個 VM 上執行，且這些 VM 位於可用性設定組中。 這些磁碟的 VM 不會全部以相同的戳記儲存，因此，如果有一個戳記失效，應用程式的其他執行個體會繼續執行。

### <a name="integration-with-availability-zones"></a>整合可用性設定組

受控磁碟支援[可用性區域](../availability-zones/az-overview.md)，這是高可用性供應項目，可保護您的應用程式不受資料中心故障影響。 「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原，所有已啟用的地區中至少要有三個不同的區域。 使用「可用性區域」時，Azure 可提供業界最佳的 99.99% VM 執行時間 SLA。

### <a name="azure-backup-support"></a>Azure 備份支援

為了防止發生區域災難，可使用 [Azure 備份](../backup/backup-overview.md)透過時間型備份和備份保留原則，來建立備份作業。 這可讓您隨意執行 VM 或受控磁碟的還原。 目前 Azure 備份支援的磁碟大小上限為 32 TiB。 [深入了解](../backup/backup-support-matrix-iaas.md) Azure VM 備份支援。

### <a name="granular-access-control"></a>細微的存取控制

您可以使用 [Azure 角色型存取控制 (Azure RBAC)](../role-based-access-control/overview.md) 將受控磁碟的特定權限指派給一個或多個使用者。 受控磁碟公開各種不同的作業，包括讀取、寫入 (建立/更新)、刪除和擷取磁碟的[共用存取簽章 (SAS) URI](../storage/common/storage-sas-overview.md)。 您可以授權某人只能存取他份內工作所需的作業。 例如，如果您不想讓某人將受控磁碟複製到儲存體帳戶，您可以選擇不要授權存取該受控磁碟的匯出動作。 同樣地，如果您不想讓某人使用 SAS URI 來複製受控磁碟，您可以選擇不要授與有關受控磁碟的這種權限。

### <a name="upload-your-vhd"></a>上傳您的 vhd

直接上傳可讓您輕鬆地將 vhd 轉送至 Azure 受控磁碟。 之前，您必須遵循涉入更深的程序，其中包含將您的資料暫存在儲存體帳戶中。 現在，步驟比較少。 您可以更輕鬆地將內部部署 VM 上傳至 Azure、上傳至大型受控磁碟，以及簡化備份和還原程序。 它也可讓您直接將資料上傳至受控磁碟，而不需將其附加至 VM，藉此降低成本。 您可使用直接上傳來上傳大小高達 32 TiB 的 vhd。

若要了解如何將您的 vhd 轉送至 Azure，請參閱 [CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) 或 [PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md)文章。

## <a name="security"></a>安全性

### <a name="private-links"></a>Private Link

受控磁片的 Private Link 支援可以用來匯入或匯出網路內部的受控磁片。 Private Link 可讓您為未連結的受控磁碟和快照集產生時間繫結的共用存取簽章 (SAS) URI，以供您用來將資料匯出至其他區域進行區域擴充、災害復原和鑑識分析。 您也可以使用 SAS URI，直接將 VHD 從內部部署環境上傳至空白磁碟。 現在您可以利用 [Private Link](../private-link/private-link-overview.md) 來限制受控磁碟的匯出和匯入，使其只能在您的 Azure 虛擬網路內進行。 Private Link 可讓您確保資料只會在安全的 Microsoft 骨幹網路內移動。

若要了解如何啟用 Private Link 以便匯入或匯出受控磁碟，請參閱 [CLI](linux/disks-export-import-private-links-cli.md) 或[入口網站](disks-enable-private-links-for-import-export-portal.md)文章。

### <a name="encryption"></a>加密

受控磁片提供兩種不同的加密。 第一種是「伺服器端加密」(SSE)，這會由儲存體服務執行。 第二種是 Azure 磁碟加密 (ADE)，您可以在您 VM 的作業系統和資料磁碟上啟用它。

#### <a name="server-side-encryption"></a>伺服器端加密

伺服器端加密提供待用加密並保護資料安全，以符合組織的安全性和合規性承諾。 在所有受控磁碟可供使用的區域中，所有受控磁碟、快照集和映像依預設都會啟用伺服器端加密。 (另一方面，暫存磁碟不會由伺服器端加密來加密，除非您在主機上啟用加密；請參閱[磁碟角色：暫存磁碟](#temporary-disk))。

您可以允許 Azure 為您管理金鑰 (這些屬於平台管理的金鑰)，您也可以自行管理金鑰 (這些屬於客戶管理的金鑰)。 如需詳細資訊，請造訪 [Azure 磁碟儲存體的伺服器端加密](./disk-encryption.md)一文。


#### <a name="azure-disk-encryption"></a>Azure 磁碟加密

Azure 磁碟加密可讓您加密由 IaaS 虛擬機器所使用的作業系統和資料磁碟。 此加密包含受控磁碟。 對於 Windows，磁碟機是使用業界標準的 BitLocker 加密技術來加密。 對於 Linux，磁碟是使用 DM-Crypt 技術來加密。 加密程序會與 Azure Key Vault 整合，可讓您控制和管理磁碟加密金鑰。 如需詳細資訊，請參閱[適用於 Linux VM 的 Azure 磁碟加密](linux/disk-encryption-overview.md)或[適用於 Windows VM 的 Azure 磁碟加密](windows/disk-encryption-overview.md)。

## <a name="disk-roles"></a>磁碟角色

Azure 中有三個主要磁碟角色：資料磁碟、OS 磁碟和暫存磁碟。 這些角色對應至您的虛擬機器連結的磁碟。

![作用中的磁碟角色](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>資料磁碟

資料磁碟是連接至虛擬機器的受控磁碟，用來儲存應用程式資料或其他您需要保留的資料。 資料磁碟註冊為 SCSI 磁碟機，並以您選擇的字母標示。 每個資料磁碟都有 32,767 GiB 的容量上限。 虛擬機器的大小會決定您可以連接之磁碟的數量，以及您可以用來裝載磁碟的儲存體類型。

### <a name="os-disk"></a>作業系統磁碟

每個虛擬機器都有一個連接的作業系統磁碟。 作業系統磁碟有預先安裝作業系統，在建立虛擬機器時即已選取。 此磁碟包含開機磁碟區。

此磁片的最大容量為 4095 GiB。

### <a name="temporary-disk"></a>暫存磁碟

大部分的 Vm 都包含暫存磁片，而非受控磁片。 暫存磁片提供應用程式和處理常式的短期儲存空間，目的是要只儲存頁面或分頁檔等資料。 暫存磁碟上的資料可能會在[維護事件](./manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime)期間或當您[重新佈署 VM](troubleshooting/redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json) 時遺失。 在 VM 的成功標準重新開機期間，暫存磁片上的資料將會保留。 如需沒有暫存磁片之 Vm 的詳細資訊，請參閱 [沒有本機暫存磁片的 AZURE VM 大小](azure-vms-no-temp-disk.md)。

在 Azure Linux VM 上，暫存磁碟通常是 /dev/sdb，而 Windows VM 上的暫存磁碟則預設為 D:。 除非您在主機上啟用加密，否則暫存磁碟不會由伺服器端加密來加密。

## <a name="managed-disk-snapshots"></a>受控磁碟快照集

受控磁碟快照集是受控磁碟的絕對一致完整唯讀複本，預設會儲存為標準受控磁碟。 快照集可讓您在任何時間點備份受控磁碟。 這些快照集可在來源磁碟外獨立存在，還能用來建立新的受控磁碟。 

快照集會根據使用的大小來計費。 例如，如果建立佈建容量為 64 GiB 的受控磁碟快照集，而實際使用資料大小為 10 GiB，則只會對已使用的 10 GiB 資料大小收取快照集費用。 您可以藉由檢查 [Azure 使用量報表](../cost-management-billing/understand/review-individual-bill.md)來查看已使用的快照大小。 例如，如果快照集的已使用資料大小為 10 GiB，則 **每日** 使用量報表會顯示已使用數量為 10 GiB/(31 天) = 0.3226。

若要深入了解如何建立受控磁碟的快照集，請參閱下列資源︰

- [在 Windows 中建立受控磁碟的快照集](windows/snapshot-copy-managed-disk.md)
- [在 Linux 中建立受控磁碟的快照集](linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>影像

受控磁碟也支援建立受管理的自訂映像。 您可以從儲存體帳戶中的自訂 VHD 或直接從一般化 (系統預備的) 虛擬機器建立映像。 此程序會擷取單一映像。 此映像包含與虛擬機器相關聯的所有受控磁碟，包括作業系統和資料磁碟。 這個受控自訂映像可讓您使用自訂映像建立數百部虛擬機器，而不需要複製或管理任何儲存體帳戶。

如需建立映像的詳細資訊，請參閱下列文章︰

- [在 Azure 中如何擷取一般化 VM 的受控映像](windows/capture-image-resource.md)
- [如何使用 Azure CLI 將 Linux 虛擬機器一般化並加以擷取](linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>映像與快照集的比較

請務必了解映像和快照集之間的差異。 受控磁碟可讓您為已解除配置的一般化虛擬機器建立映像。 此映像包含所有附加至虛擬機器的磁碟。 您可以使用此映像建立虛擬機器，它會包含所有磁碟。

快照集是在建立快照當下的磁碟複本， 只適用於一個磁碟。 如果您的虛擬機器有一個磁碟 (作業系統磁碟)，您可以建立它的快照集或映像，然後從快照集或映像建立虛擬機器。

快照集只會感知到本身包含的磁碟，對其他任何磁碟一概不知。 若要在需要協調多個磁碟的情況下 (例如等量分割) 使用，這會出現問題。 快照集必須能夠彼此協調，但目前不支援。

## <a name="disk-allocation-and-performance"></a>磁碟配置和效能

下圖使用三層佈建系統，說明進行磁碟的頻寬和 IOPS 即時配置：

![顯示頻寬和 IOPS 配置的三層佈建系統](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

第一層佈建會設定每個磁碟的 IOPS 和頻寬指派。  在第二層，計算伺服器主機會實作 SSD 佈建，只將它套用至伺服器 SSD 上儲存的資料，包括具有快取 (ReadWrite 和 ReadOnly) 的磁碟，以及本機和暫存磁碟。 最後，VM 網路佈建會針對計算主機傳送到 Azure 儲存體後端的任何 I/O 在第三層進行。 使用此配置時，VM 的效能取決於各種不同的因素，包括 VM 如何使用本機 SSD、連結的磁碟數目，以及其所連結磁碟的效能和快取類型。

在這些限制的範例中，Standard_DS1v1 VM 因為 SSD 和網路層級的限制而無法達到 P30 磁碟的 5,000 IOPS 潛能 (不論是否快取)：

![Standard_DS1v1 範例配置](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Azure 會針對磁碟流量使用依優先順序的網路通道，其優先順序高於其他低優先順序的網路流量。 這可協助磁碟在網路爭用的情況下維持其預期的效能。 同樣地，Azure 儲存體會使用自動負載平衡來處理背景中的資源爭用和其他問題。 當您建立磁碟時，Azure 儲存體會配置所需的資源，並套用資源的主動式和回應式平衡來處理流量層級。 這可進一步確保磁碟可維持其預期的 IOPS 和輸送量目標。 您可以視需要使用 VM 層級和磁碟層級計量來追蹤效能和設定警示。

請參閱我們的[高效能設計](premium-storage-performance.md)一文，以了解最佳化 VM 和磁碟組態的最佳作法，以便您達到所需的效能

## <a name="next-steps"></a>後續步驟

如果您想要詳細說明受控磁碟的影片，請參閱：[使用受控磁碟提升 Azure VM 復原能力](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency)。

請參閱磁碟類型的相關文章，深入了解 Azure 提供的各種磁碟類型，找出何種類型最符合您的需求，以及了解其效能目標。

> [!div class="nextstepaction"]
> [選取適用於 IaaS VM 的磁碟類型](disks-types.md)