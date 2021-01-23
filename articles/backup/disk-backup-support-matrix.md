---
title: Azure 磁碟備份支援矩陣
description: 提供支援設定的摘要，以及 Azure 磁片備份的限制。
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 447283ba1d63267722e4167e0727a827e63d2e0d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732974"
---
# <a name="azure-disk-backup-support-matrix-in-preview"></a>預覽版中的 Azure 磁片備份支援矩陣 () 

>[!IMPORTANT]
>Azure 磁片備份處於預覽狀態，不含服務等級協定，因此不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
>[填寫此表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 以註冊預覽。

您可以使用 [Azure 備份](./backup-overview.md) 來保護 Azure 磁片。 本文摘要說明區域可用性、支援的案例和限制。

## <a name="supported-regions"></a>支援區域

Azure 磁片備份于下欄區域提供預覽：美國中西部。 

當有更多區域可供使用時，將會宣佈更多區域。

## <a name="limitations"></a>限制

- Azure 受控磁碟支援 azure 磁片備份，包括共用磁片 (共用 premium Ssd) 。 不支援非受控磁片。 由於缺少快照集功能，此解決方案目前不支援 Ultra 磁片（包括共用的 ultra 磁片）。

- Azure 磁片備份支援寫入加速器磁片的備份。 不過，在還原期間，磁片會還原為一般磁片。 將加速快取掛接至 VM 之後，就可以在磁片上啟用寫入快取記憶體。

- Azure 備份提供 Azure 受控磁片的操作 (快照集) 層備份，每天支援多個備份。 備份不會複製到備份保存庫。

- 目前，Original-Location 復原 (OLR) 選項，以藉由取代不支援備份的現有來源磁片來進行還原。 您可以從復原點還原，以在與來源磁片相同的資源群組中建立新的磁片，以從中取得備份或任何其他資源群組。 這就是所謂的 Alternate-Location Recovery (ALR) 。

- 受控磁碟的 Azure 備份會使用增量快照集，每個磁片僅限200個快照集。 為了讓您在排程的備份之外進行隨選備份，備份原則會將備份總數限制為180。 深入瞭解受控磁片的累加 [式快照](../virtual-machines/disks-incremental-snapshots.md#restrictions) 集。

- Azure [訂用帳戶和服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machine-disk-limits) 適用于每個訂用帳戶每個區域的磁片快照集總數。

- 不支援連接到虛擬機器的多個磁片的時間點快照集。

- 備份保存庫和要備份的磁片可以位於相同或不同的訂用帳戶中。 不過，備份保存庫和備份的磁片必須位於相同的區域。

- 要備份的磁片，以及快照集儲存在本機的快照集資源群組，必須位於相同的訂用帳戶中。

- 支援將磁片從備份還原至相同或不同的訂用帳戶。 不過，還原的磁片將會在與快照集相同的區域中建立。

- 無法保護 ADE 加密的磁片。

- 支援以平臺管理的金鑰或客戶管理的金鑰加密的磁片。 不過，使用客戶管理的金鑰加密之磁片的還原點還原將會失敗 (CMK) 如果已停用或刪除磁片加密集 KeyVault 機碼。

- 目前無法修改備份原則，而且當您設定磁片備份時，指派給備份實例的快照集資源群組無法變更。

- 目前，設定磁片備份的 Azure 入口網站體驗，最多可有20個來自相同訂用帳戶的磁片。

- 目前 (在預覽) 期間，不支援使用 PowerShell 和 Azure CLI 設定磁片的備份和還原。

- 設定備份時，選取要備份的磁片，以及要儲存快照集的快照集資源群組必須屬於相同的訂用帳戶。 您無法為該磁片訂用帳戶以外的特定磁片建立增量快照集。 深入瞭解受控磁片的 [增量快照](../virtual-machines/windows/disks-incremental-snapshots-portal.md#restrictions) 集。 如需如何選擇快照集資源群組的詳細資訊，請參閱  [設定備份](backup-managed-disks.md#configure-backup)。

- 針對成功的備份和還原作業，備份保存庫的受控識別需要角色指派。 請僅使用檔中提供的角色定義。 不支援使用其他角色（例如「擁有者」、「參與者」等等）。 如果您在指派角色之後立即開始設定備份或還原作業，您可能會遇到許可權問題。 這是因為角色指派需要幾分鐘的時間才會生效。

- 受控磁片可讓您在部署時或之後變更效能層級，而不會變更磁片的大小。 Azure 磁片備份解決方案支援效能層級變更至要備份的來源磁片。 在還原期間，還原的磁片的效能層級將會與備份時來源磁片的效能層級相同。 請依照 [此處](../virtual-machines/disks-performance-tiers-portal.md) 的檔，在還原作業之後變更磁片的效能層級。

- 受控磁片的[Private Links](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)支援可讓您限制受控磁片的匯出和匯入，使其只在 Azure 虛擬網路中發生。 Azure 磁片備份支援備份已啟用私人端點的磁片。 這不包括可透過私人端點存取的備份資料或快照集。

## <a name="next-steps"></a>後續步驟

- [備份 Azure 受控磁碟](backup-managed-disks.md)