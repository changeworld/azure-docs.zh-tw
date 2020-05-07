---
title: 將 Azure Vm 移至新的訂用帳戶或資源群組
description: 使用 Azure Resource Manager 將虛擬機器移至新的資源群組或訂用帳戶。
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: e5bd004b6619db9c9882b8e9e6005309317b8ca5
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744644"
---
# <a name="move-guidance-for-virtual-machines"></a>適用于虛擬機器的移動指引

本文說明目前不支援的案例，以及使用備份來移動虛擬機器的步驟。

## <a name="scenarios-not-supported"></a>不支援的案例

尚未支援下列案例：

* 可用性區域中的受控磁碟無法移至不同的訂用帳戶。
* 不能移動標準 SKU Load Balancer 或標準 SKU 公用 IP 的虛擬機器擴展集。
* 從 Marketplace 資源建立且附加方案的虛擬機器無法在資源群組或訂用帳戶之間移動。 取消布建目前訂用帳戶中的虛擬機器，然後在新的訂用帳戶中再次部署。
* 當您不移動虛擬網路中的所有資源時，無法將現有虛擬網路中的虛擬機器移至新的訂用帳戶。
* 低優先順序的虛擬機器和低優先順序的虛擬機器擴展集無法在資源群組或訂用帳戶之間移動。
* 無法個別移動可用性設定組中的虛擬機器。

## <a name="virtual-machines-with-azure-backup"></a>具有 Azure 備份的虛擬機器

若要移動以 Azure 備份設定的虛擬機器，您必須從保存庫中刪除還原點。

如果您的虛擬機器已啟用虛[刪除](../../../backup/backup-azure-security-feature-cloud.md)，則在保留這些還原點時，您無法移動虛擬機器。 刪除還原點之後，請停用虛[刪除](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)或等待14天。

### <a name="portal"></a>入口網站

1. 暫時停止備份並保留備份資料。
2. 若要移動以 Azure 備份設定的虛擬機器，請執行下列步驟：

   1. 尋找虛擬機器的位置。
   2. 尋找具有下列命名模式的資源群組： `AzureBackupRG_<location of your VM>_1`。 例如， *AzureBackupRG_westus2_1*
   3. 在 [Azure 入口網站中，勾選 [**顯示隱藏的類型**]。
   4. 尋找類型為**Microsoft. Compute/restorePointCollections**且具有命名模式`AzureBackup_<name of your VM that you're trying to move>_###########`的資源。
   5. 刪除此資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。
   6. 刪除作業完成之後，您就可以移動虛擬機器。

3. 將 VM 移至目標資源群組。
4. 繼續備份。

### <a name="powershell"></a>PowerShell

* 尋找您虛擬機器的位置。
* 尋找具有下列命名模式的資源群組：`AzureBackupRG_<location of your VM>_1`，例如 AzureBackupRG_westus2_1
* 如果是在 PowerShell 中，請使用 `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` Cmdlet
* 尋找類型為 `Microsoft.Compute/restorePointCollections` 且命名模式為 `AzureBackup_<name of your VM that you're trying to move>_###########` 的資源
* 刪除此資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。

### <a name="azure-cli"></a>Azure CLI

* 尋找您虛擬機器的位置。
* 尋找具有下列命名模式的資源群組：`AzureBackupRG_<location of your VM>_1`，例如 AzureBackupRG_westus2_1
* 如果是在 CLI 中，請使用 `az resource list -g AzureBackupRG_<location of your VM>_1`
* 尋找類型為 `Microsoft.Compute/restorePointCollections` 且命名模式為 `AzureBackup_<name of your VM that you're trying to move>_###########` 的資源
* 刪除此資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。

## <a name="next-steps"></a>後續步驟

* 如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../move-resource-group-and-subscription.md)。

* 如需移動復原服務保存庫以進行備份的相關資訊，請參閱[復原服務限制](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。
