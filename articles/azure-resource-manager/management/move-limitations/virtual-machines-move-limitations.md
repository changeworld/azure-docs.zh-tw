---
title: 將 Azure Vm 移至新的訂用帳戶或資源群組
description: 使用 Azure Resource Manager 將虛擬機器移至新的資源群組或訂用帳戶。
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: ace1fb6bf3944df539ec8f7301357e67d2b315a9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184071"
---
# <a name="move-guidance-for-virtual-machines"></a>虛擬機器的移動指引

本文說明目前不支援的案例，以及使用備份來移動虛擬機器的步驟。

## <a name="scenarios-not-supported"></a>不支援的案例

尚未支援下列案例：

* 具有標準 SKU Load Balancer 或標準 SKU 公用 IP 的虛擬機器擴展集無法移動。
* 當您未移動虛擬網路中的所有資源時，現有虛擬網路中的虛擬機器無法移至新的訂用帳戶。
* 低優先順序的虛擬機器和低優先順序的虛擬機器擴展集無法跨資源群組或訂用帳戶移動。
* 無法個別移動可用性設定組中的虛擬機器。

## <a name="azure-disk-encryption"></a>Azure 磁碟加密

您無法移動與金鑰保存庫整合的虛擬機器，以執行 [適用于 Linux vm 的 Azure 磁碟加密](../../../virtual-machines/linux/disk-encryption-overview.md) 或 [適用于 Windows vm 的 Azure 磁碟加密](../../../virtual-machines/windows/disk-encryption-overview.md)。 若要移動 VM，您必須停用加密。

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-marketplace-plans"></a>具有 Marketplace 方案的虛擬機器

從 Marketplace 資源建立且附加方案的虛擬機器，無法跨訂用帳戶移動。 若要解決這項限制，您可以在目前的訂用帳戶中取消布建虛擬機器，並在新的訂用帳戶中再次部署它。 下列步驟可協助您重新建立新訂用帳戶中的虛擬機器。 不過，它們在所有案例中可能無法運作。 如果 Marketplace 中已不再提供方案，則這些步驟將無法運作。

1. 複製計畫的相關資訊。

1. 請將 OS 磁碟複製到目的地訂用帳戶，或從來源訂用帳戶刪除虛擬機器之後移動原始磁片。

1. 在目的地訂用帳戶中，接受您方案的 Marketplace 條款。 您可以執行下列 PowerShell 命令來接受這些條款：

   ```azurepowershell
   Get-AzMarketplaceTerms -Publisher {publisher} -Product {product/offer} -Name {name/SKU} | Set-AzMarketplaceTerms -Accept
   ```

   或者，您可以透過入口網站，建立新的虛擬機器實例與方案。 您可以在接受新訂用帳戶中的條款之後，刪除虛擬機器。

1. 在目的地訂用帳戶中，使用 PowerShell、CLI 或 Azure Resource Manager 範本，從複製的 OS 磁片重新建立虛擬機器。 包含連接到磁片的 marketplace 方案。 計畫的相關資訊應該符合您在新訂用帳戶中購買的方案。

## <a name="virtual-machines-with-azure-backup"></a>具有 Azure 備份的虛擬機器

若要移動使用 Azure 備份設定的虛擬機器，您必須刪除保存庫中的還原點。

如果您的虛擬機器已啟用虛 [刪除](../../../backup/backup-azure-security-feature-cloud.md) ，則您無法在保留這些還原點時移動虛擬機器。 請在刪除還原點後停用虛 [刪除](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) 或等候14天。

### <a name="portal"></a>入口網站

1. 暫時停止備份並保留備份資料。
2. 若要移動使用 Azure 備份設定的虛擬機器，請執行下列步驟：

   1. 尋找虛擬機器的位置。
   2. 尋找具有下列命名模式的資源群組： `AzureBackupRG_<VM location>_1` 。 例如，名稱的格式為 *AzureBackupRG_westus2_1*。
   3. 在 [Azure 入口網站中，選取 [ **顯示隱藏的類型**]。
   4. 尋找具有命名模式的 **restorePointCollections** 類型的資源 `AzureBackup_<VM name>_###########` 。
   5. 刪除此資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。
   6. 刪除作業完成之後，您就可以移動虛擬機器。

3. 將 VM 移至目標資源群組。
4. 繼續備份。

### <a name="powershell"></a>PowerShell

1. 尋找虛擬機器的位置。

1. 尋找具有命名模式的資源群組- `AzureBackupRG_<VM location>_1` 。 例如，名稱可能是 `AzureBackupRG_westus2_1` 。

1. 如果您只移動一部虛擬機器，請取得該虛擬機器的還原點集合。

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -name AzureBackup_<VM name>* -ResourceType Microsoft.Compute/restorePointCollections
   ```

   刪除此資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。

   ```azurepowershell-interactive
   Remove-AzResource -ResourceId $restorePointCollection.ResourceId -Force
   ```

1. 如果您要將所有虛擬機器移至此位置的備份，請取得這些虛擬機器的還原點集合。

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

   刪除每個資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。

   ```azurepowershell-interactive
   foreach ($restorePoint in $restorePointCollection)
   {
     Remove-AzResource -ResourceId $restorePoint.ResourceId -Force
   }
   ```

### <a name="azure-cli"></a>Azure CLI

1. 尋找虛擬機器的位置。

1. 尋找具有命名模式的資源群組- `AzureBackupRG_<VM location>_1` 。 例如，名稱可能是 `AzureBackupRG_westus2_1` 。

1. 如果您只移動一部虛擬機器，請取得該虛擬機器的還原點集合。

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections --query "[?starts_with(name, 'AzureBackup_<VM name>')].id" --output tsv)
   ```

   刪除此資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

1. 如果您要將所有虛擬機器移至此位置的備份，請取得這些虛擬機器的還原點集合。

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections)
   ```

   刪除每個資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

## <a name="next-steps"></a>後續步驟

* 如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../move-resource-group-and-subscription.md)。

* 如需移動復原服務保存庫以進行備份的相關資訊，請參閱[復原服務限制](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。
