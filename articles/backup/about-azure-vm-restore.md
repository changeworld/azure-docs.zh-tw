---
title: 關於 Azure 虛擬機器還原程式
description: 瞭解 Azure 備份服務如何還原 Azure 虛擬機器
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: a604e146dbe387675e9ed82030639921cfc03167
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87067471"
---
# <a name="about-azure-vm-restore"></a>關於 Azure VM 還原

本文說明[Azure 備份服務](./backup-overview.md)如何還原 Azure 虛擬機器（vm）。 有一些還原選項。 我們將討論其支援的各種案例。

## <a name="concepts"></a>概念

- **復原點**（也稱為**還原點**）：復原點是正在備份的原始資料複本。

- **層（快照集與保存庫）**： Azure VM 備份會分兩個階段發生：

  - 在第1階段中，所建立的快照集會與磁片一起儲存。 這稱為「快照集**層**」。 快照集層還原的速度較快（而不是從保存庫還原），因為它們會在觸發還原之前，消除快照集複製到保存庫的等候時間。 因此，從快照集層進行還原也稱為「[立即還原](./backup-instant-restore-capability.md)」。
  - 在第2階段中，會將快照集傳輸並儲存在 Azure 備份服務所管理的保存庫中。 這稱為「保存**庫層**」。

- **原始位置復原（OLR）**：從還原點到來源 Azure VM （從中取得備份）進行的復原，以儲存在復原點中的狀態取代。 這會取代來源 VM 的 OS 磁片和資料磁片。

- **替代位置復原（ALR）**：從復原點到執行備份的源伺服器以外的伺服器進行復原。

- **專案層級還原（ILR）：** 從復原點還原 VM 內的個別檔案或資料夾

- **可用性（複寫類型）**： Azure 備份提供兩種複寫類型，讓您的儲存體/資料具有高可用性：
  - [本機多餘儲存體（LRS）](../storage/common/storage-redundancy.md)會在資料中心的儲存體縮放單位中，複寫您的資料三次（它會建立三個數據複本）。 此資料的所有複本都存在於相同的區域內。 LRS 是保護資料免於本機硬體失敗的低成本選項。
  - [異地冗余儲存體（GRS）](../storage/common/storage-redundancy.md)是預設和建議的複寫選項。 GRS 會將資料複寫到次要地區 (與來源資料主要位置距離數百英哩)。 GRS 的價格高於 LRS，但可為您的資料提供更高層級的持久性，即使遭受區域性中斷也不影響。

- **跨區域還原（CRR）**：做為其中一個[還原選項](./backup-azure-arm-restore-vms.md#restore-options)，跨區域還原（CRR）可讓您在次要區域（也就是[azure 配對的區域](../best-practices-availability-paired-regions.md#what-are-paired-regions)）中還原 azure vm。

## <a name="restore-scenarios"></a>還原案例

![還原案例 ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **案例**                                                 | **完成的工作**                                             | **使用時機**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [還原以建立新的虛擬機器](./backup-azure-arm-restore-vms.md) | 將整個 VM 還原至 OLR （如果來源 VM 仍然存在）或 ALR | <li> 如果來源 VM 遺失或損毀，您可以還原整個 VM  <li> 您可以建立 VM 的複本  <li> 您可以針對 audit 或合規性執行還原演練  <li> 此選項不適用於從 Marketplace 映射建立的 Azure Vm （也就是因為授權已過期而無法使用）。 |
| [還原 VM 的磁片](./backup-azure-arm-restore-vms.md#restore-disks) | 還原已連接至 VM 的磁片                             |  所有磁片：此選項會建立範本並復原磁碟。 您可以使用特殊設定（例如，可用性設定組）來編輯此範本，以符合您的需求，然後同時使用範本和復原磁碟來重新建立 VM。 |
| [還原 VM 內的特定檔案](./backup-azure-restore-files-from-vm.md) | 選擇 [還原點]、[流覽]、[選取檔案]，然後將它們還原到與備份 VM 相同的（或相容的）作業系統。 |  如果您知道要還原哪些特定檔案，請使用此選項，而不是還原整個 VM。 |
| [還原已加密的 VM](./backup-azure-vms-encryption.md) | 從入口網站復原磁碟，然後使用 PowerShell 來建立 VM | <li> [具有 Azure Active Directory （AAD）的加密 VM](../virtual-machines/windows/disk-encryption-windows-aad.md)  <li> [沒有 AAD 的已加密 VM](../virtual-machines/windows/disk-encryption-windows.md) <li> [已將*aad*遷移至*但未使用 aad*的已加密 VM](../virtual-machines/windows/disk-encryption-faq.md#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [跨區域還原](./backup-azure-arm-restore-vms.md#cross-region-restore) | 建立新的 VM，或將磁片還原到次要區域（Azure 配對的區域） | <li> **完全中斷**：使用跨區域還原功能時，不會有在次要區域中復原資料的等候時間。 即使在 Azure 宣告中斷之前，您也可以在次要區域中起始還原。 <li> **部分中斷**：特定的儲存叢集可能會發生停機狀況，其中 Azure 備份會儲存已備份的資料，甚至是在網路中，連接與您備份的資料相關聯的 Azure 備份和存放叢集。 透過跨區域還原，您可以使用次要地區中已備份資料的複本，在次要區域中執行還原。 <li> **無中斷**：您可以使用次要地區資料，進行商務持續性和嚴重損壞修復（BCDR）演練，以進行審核或合規性的目的。 這可讓您在次要區域中執行已備份資料的還原，即使主要區域中的商務持續性和嚴重損壞修復演練沒有完整或部分中斷的情況也是如此。  |

------





## <a name="next-steps"></a>後續步驟

- [VM 還原的相關常見問題](./backup-azure-vm-backup-faq.md#restore)
- [支援的還原方法](./backup-support-matrix-iaas.md#supported-restore-methods)
- [針對還原問題進行疑難排解](./backup-azure-vms-troubleshoot.md#restore)
