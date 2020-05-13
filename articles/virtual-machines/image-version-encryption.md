---
title: 預覽-建立以您自己的金鑰加密的映射版本
description: 使用客戶管理的加密金鑰，在共用映射資源庫中建立映射版本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/06/2020
ms.author: cynthn
ms.openlocfilehash: aeacfdc07e5349dfce45b209da1d78bddf870f33
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83269575"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>預覽：使用客戶管理的金鑰來加密映射

圖庫映射會儲存為受控磁片，因此會使用伺服器端加密來自動加密。 伺服器端加密使用256位[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)，這是可用的最強區塊密碼之一，且符合 FIPS 140-2 規範。 如需 Azure 受控磁片基礎密碼編譯模組的詳細資訊，請參閱[密碼編譯 API：新一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

您可以依賴平臺管理的金鑰來加密您的映射，也可以使用自己的金鑰來管理加密。 如果您選擇使用自己的金鑰來管理加密，您可以指定*客戶管理的金鑰*，以用於加密和解密映射中的所有磁片。 

使用客戶管理的金鑰的伺服器端加密會使用 Azure Key Vault。 您可以將[您的 rsa 金鑰](../key-vault/keys/hsm-protected-keys.md)匯入 Key Vault，或在 Azure Key Vault 中產生新的 rsa 金鑰。

若要針對影像使用客戶管理的金鑰，您必須先 Azure Key Vault。 接著，您會建立磁片加密組。 然後，在建立映射版本時，會使用磁片加密集。

如需建立和使用磁片加密集的詳細資訊，請參閱[客戶管理的金鑰](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)。

## <a name="limitations"></a>限制

使用客戶管理的金鑰來加密共用映射庫映射時，有幾項限制：  

- 加密金鑰集必須與您的映射位於相同的訂用帳戶和區域中。

- 您無法共用使用客戶管理金鑰的映射。 

- 您無法將使用客戶管理金鑰的影像複寫到其他區域。

- 一旦您使用自己的金鑰來加密磁片或映射，就無法返回使用平臺管理的金鑰來加密這些磁片或映射。


> [!IMPORTANT]
> 使用客戶管理的金鑰進行加密目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="powershell"></a>PowerShell

在公開預覽中，您必須先註冊此功能。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

註冊需要幾分鐘的時間才能完成。 使用 AzProviderFeature 來檢查功能註冊的狀態。

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

當 RegistrationState 傳回 [已註冊] 時，您可以移至下一個步驟。

檢查您的提供者註冊。 請確定它會傳回 `Registered` 。

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

如果未傳回 `Registered` ，請使用下列內容來註冊提供者：

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

若要指定映射版本的磁片加密設定為，請使用[AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)搭配 `-TargetRegion` 參數。 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$targetRegion = @{$region1}


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>建立 VM

您可以從共用映射資源庫建立 VM，並使用客戶管理的金鑰來加密磁片。 語法與從映射建立[一般化](vm-generalized-image-version-powershell.md)或[特製](vm-specialized-image-version-powershell.md)化的 vm 相同，您需要使用擴充參數集並新增 `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` 至 VM 設定。

針對資料磁片，您必須在 `-DiskEncryptionSetId $setID` 使用[AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk)時新增參數。


## <a name="cli"></a>CLI 

在公開預覽中，您必須先註冊此功能。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

檢查功能註冊的狀態。

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

當此傳回時 `"state": "Registered"` ，您可以移至下一個步驟。

檢查您的註冊。

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

如果未顯示為 [已註冊]，請執行下列動作：

```azurecli-interactive
az provider register -n Microsoft.Compute
```


若要指定映射版本的磁片加密設定為，請使用[az image 圖庫 create-映射版本](/cli/azure/sig/image-version#az-sig-image-version-create)搭配 `--target-region-encryption` 參數。 的格式 `--target-region-encryption` 是用來加密 OS 和資料磁片的索引鍵清單（以空格分隔）。 它看起來應該像這樣：`<encryption set for the OS disk>,<Lun number of the data disk>, <encryption set for the data disk>, <Lun number for the second data disk>, <encryption set for the second data disk>`。 

如果 OS 磁片的來源是受控磁片或 VM，請使用 `--managed-image` 來指定映射版本的來源。 在此範例中，來源是一個受控映射，其具有 OS 磁片以及位於 LUN 0 的資料磁片。 OS 磁片將會使用 DiskEncryptionSet1 加密，且資料磁片將會使用 DiskEncryptionSet2 加密。

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

如果 OS 磁片的來源是快照集，請使用 `--os-snapshot` 來指定作業系統磁片。 如果有資料磁片快照集也必須是映射版本的一部分，請使用來新增它們 `--data-snapshot-luns` 以指定 LUN，並 `--data-snapshots` 指定快照集。

在此範例中，來源是磁片快照集。 還有一個 OS 磁片，另一個是位於 LUN 0 的資料磁片。 OS 磁片將會使用 DiskEncryptionSet1 加密，且資料磁片將會使用 DiskEncryptionSet2 加密。

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot"
   --data-snapshot-luns 0
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot"
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>建立 VM

您可以從共用映射資源庫建立 VM，並使用客戶管理的金鑰來加密磁片。 語法與從映射建立[一般化](vm-generalized-image-version-cli.md)或[特製](vm-specialized-image-version-cli.md)化的 VM 相同，您只需要新增 `--os-disk-encryption-set` 具有加密集識別碼的參數即可。 對於資料磁片，請新增 `--data-disk-encryption-sets` 資料磁片的磁片加密組清單（以空格分隔）。


## <a name="portal"></a>入口網站

當您在入口網站中建立映射版本時，您可以使用 [**加密**] 索引標籤來輸入儲存體加密集的相關資訊。

1. 在 [**建立映射版本**] 頁面中，選取 [**加密**] 索引標籤。
2. 在 [**加密類型**] 中，選取 [待用**加密] 與客戶管理的金鑰**。 
3. 針對映射中的每個磁片，從下拉式選單選取要使用的**磁片加密集**。 

### <a name="create-the-vm"></a>建立 VM

您可以從共用映射資源庫建立 VM，並使用客戶管理的金鑰來加密磁片。 當您在入口網站中建立 VM 時，請在 [**磁片**] 索引標籤上，選取 [**使用客戶管理的金鑰**進行待用加密] 做為**加密類型**。 然後，您可以從下拉式選單選取加密集。

## <a name="next-steps"></a>後續步驟

深入瞭解[伺服器端磁片加密](/windows/disk-encryption.md)）。
