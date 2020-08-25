---
title: 關於 Azure 虛擬機器還原程式
description: 瞭解 Azure 備份服務如何還原 Azure 虛擬機器
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: fb3f6bf729047dc6fe1c13c5ead1191188e30e5c
ms.sourcegitcommit: f1b18ade73082f12fa8f62f913255a7d3a7e42d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2020
ms.locfileid: "88761384"
---
# <a name="about-azure-vm-restore"></a>關於 Azure VM 還原

本文說明 [Azure 備份服務](./backup-overview.md) 如何)  (Vm 還原 Azure 虛擬機器。 有一些還原選項。 我們會討論支援的各種案例。

## <a name="concepts"></a>概念

- **復原點** (也稱為 **還原點**) ：復原點是正在備份的原始資料複本。

- 階層** (快照集與保存庫) **： Azure VM 備份會分兩個階段進行：

  - 在第1階段中，所建立的快照集會與磁片一併儲存。 這稱為 **快照集層**。 快照集層還原的 (比從保存庫) 還原更快，因為它們會在觸發還原之前，消除快照集複製到保存庫的等候時間。 因此，從快照集層還原也稱為「 [立即還原](./backup-instant-restore-capability.md)」。
  - 在階段2中，快照集會傳送並儲存在 Azure 備份服務所管理的保存庫中。 這稱為保存 **庫層**。

- **原始位置復原 (OLR) **：從還原點完成復原到來源 Azure VM （從備份取得的位置），並以復原點中儲存的狀態取代。 這會取代來源 VM 的 OS 磁片和資料磁片 (s) 。

- **替代位置復原 (ALR) **：從復原點復原到執行備份的源伺服器以外的伺服器。

- **專案層級還原 (ILR) ：** 從復原點還原 VM 內的個別檔案或資料夾

- **可用性 (複寫類型) **： Azure 備份提供兩種複寫類型，以保持儲存體/資料的高可用性：
  - [本機冗余儲存體 (LRS) ](../storage/common/storage-redundancy.md) 複寫您的資料三次 (它會在資料中心的儲存體縮放單位中建立三份資料) 。 此資料的所有複本都存在於相同的區域內。 LRS 是保護資料免於本機硬體失敗的低成本選項。
  - [異地多餘儲存體 (GRS) ](../storage/common/storage-redundancy.md) 是預設和建議的複寫選項。 GRS 會將資料複寫到次要地區 (與來源資料主要位置距離數百英哩)。 GRS 的價格高於 LRS，但可為您的資料提供更高層級的持久性，即使遭受區域性中斷也不影響。

- **跨區域還原 (crr) **：作為其中一個 [還原選項](./backup-azure-arm-restore-vms.md#restore-options)、跨區域還原 (CRR) 可讓您在次要區域（也就是 [Azure 配對的區域](../best-practices-availability-paired-regions.md#what-are-paired-regions)）中還原 azure vm。

## <a name="restore-scenarios"></a>還原案例

![還原案例 ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **案例**                                                 | **完成的工作**                                             | **使用時機**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [還原以建立新的虛擬機器](./backup-azure-arm-restore-vms.md) | 如果來源 VM 仍然存在) 或 ALR，則將整個 VM 還原至 OLR ( | <li> 如果來源 VM 遺失或損毀，您可以還原整個 VM  <li> 您可以建立 VM 的複本  <li> 您可以執行審核或合規性的還原演練  <li> 此選項不適用於從 Marketplace 映射建立的 Azure Vm (也就是因為授權已過期) ，所以無法使用。 |
| [還原 VM 的磁片](./backup-azure-arm-restore-vms.md#restore-disks) | 還原連接至 VM 的磁片                             |  所有磁片：此選項會建立範本並復原磁碟。 您可以使用特殊設定來編輯此範本 (例如，可用性設定組) 符合您的需求，然後使用範本並復原磁碟來重新建立 VM。 |
| [還原 VM 內的特定檔案](./backup-azure-restore-files-from-vm.md) | 選擇 [還原點]、[流覽]、[選取檔案]，然後將它們還原至與備份 VM 相同的 (或相容) 作業系統。 |  如果您知道要還原的特定檔案，請使用此選項，而不是還原整個 VM。 |
| [還原已加密的 VM](./backup-azure-vms-encryption.md) | 從入口網站復原磁碟，然後使用 PowerShell 來建立 VM | <li> [具有 Azure Active Directory (AAD) 的已加密 VM ](../virtual-machines/windows/disk-encryption-windows-aad.md)  <li> [沒有 AAD 的已加密 VM](../virtual-machines/windows/disk-encryption-windows.md) <li> [已將*aad*遷移至*但沒有 aad*的已加密 VM](../virtual-machines/windows/disk-encryption-faq.md#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [跨區域還原](./backup-azure-arm-restore-vms.md#cross-region-restore) | 建立新的 VM，或將磁片還原至 (Azure 配對區域的次要區域)  | <li> **完全中斷**：使用跨區域還原功能時，不會有等候時間來復原次要區域中的資料。 即使在 Azure 宣告中斷之前，您也可以在次要區域中起始還原。 <li> **部分中斷**：可能會在特定的儲存體叢集中發生停機狀況，其中 Azure 備份儲存您的備份資料，甚至是在網路中，連接到與備份資料相關聯的 Azure 備份和儲存叢集。 使用跨區域還原，您可以在次要區域中使用備份資料的複本，在次要區域中執行還原。 <li> **沒有中斷**：您可以使用次要區域資料來進行商務持續性和嚴重損壞修復， (BCDR) 演練進行審核或合規性的用途。 這可讓您在次要區域中執行已備份資料的還原，即使主要區域的商務持續性和嚴重損壞修復演練沒有完整或部分中斷。  |

## <a name="next-steps"></a>後續步驟

- [關於 VM 還原的常見問題](./backup-azure-vm-backup-faq.md#restore)
- [支援的還原方法](./backup-support-matrix-iaas.md#supported-restore-methods)
- [針對還原問題進行疑難排解](./backup-azure-vms-troubleshoot.md#restore)
