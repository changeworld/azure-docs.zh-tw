---
title: 預覽 - 建立以您自己的金鑰加密的映像版本
description: 使用客戶管理的加密金鑰，在共用映像庫中建立映像版本。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: e0534fa6eaccbfb9318369e0a4224d84fa8de7c8
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347704"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>預覽：使用客戶管理的金鑰加密映像

映像庫中的映像會儲存為受控磁碟，因此會使用伺服器端加密來自動加密。 伺服器端加密使用 256 位元的 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)，這是最強的區塊編碼器之一，且符合 FIPS 140-2 規範。 如需有關基礎 Azure 受控磁碟的加密模組詳細資訊，請參閱[加密 API：新一代](/windows/desktop/seccng/cng-portal)。

您可以依賴平臺管理的金鑰來加密您的映射、使用您自己的金鑰，也可以同時使用兩者來進行雙重加密。 如果您選擇使用自己的金鑰來管理加密，您可以指定 *客戶管理的金鑰* ，以用於加密和解密映像中的所有磁碟。 

使用「客戶管理的金鑰」的伺服器端加密，會使用 Azure Key Vault。 您可以將[您的 RSA 金鑰](../key-vault/keys/hsm-protected-keys.md)匯入 Key Vault，或在 Azure Key Vault 中產生新的 RSA 金鑰。

## <a name="prerequisites"></a>先決條件

本文要求您在要將映射複寫到的每個區域中都已有磁片加密集。

- 若只要使用客戶管理的金鑰，請參閱使用 [Azure 入口網站](./disks-enable-customer-managed-keys-portal.md)或 [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-your-azure-key-vault-and-diskencryptionset)**以伺服器端加密啟用客戶管理的金鑰** 。

- 若要使用平臺管理和客戶管理的金鑰 (進行雙重加密) ，請參閱使用 [Azure 入口網站](./disks-enable-double-encryption-at-rest-portal.md)或 [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md)**啟用靜態加密** 。
    > [!IMPORTANT]
    > 您必須使用此連結 [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) 來存取 Azure 入口網站。 靜態加密目前不會顯示在公用 Azure 入口網站中，而不需要使用連結。

## <a name="limitations"></a>限制

使用客戶管理的金鑰來加密共用映像庫映像時，有幾個限制：  

- 加密金鑰集必須位於與您映射相同的訂用帳戶中。

- 加密金鑰集是區域資源，因此每個區域都需要不同的加密金鑰集。

- 您無法複製或共用使用客戶管理金鑰的映射。 

- 一旦您使用自己的金鑰來加密磁碟或映像，就無法回去使用「平台管理的金鑰」來加密這些磁碟或映像。


> [!IMPORTANT]
> 使用「客戶管理的金鑰」加密目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="powershell"></a>PowerShell

在公開預覽中，您必須先註冊此功能。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

需要幾分鐘的時間才能完成註冊。 使用 Get-AzProviderFeature 檢查功能註冊的狀態。

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

當 RegistrationState 傳回 Registered 時，您可以移至下一個步驟。

檢查您的提供者註冊。 請確定它傳回 `Registered`。

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

如果未傳回 `Registered`，使用下列命令來註冊提供者：

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

若要指定映像版本的磁碟加密設定為，使用 [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) 搭配 `-TargetRegion` 參數。 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


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

您可以從共用映像庫建立 VM，並使用客戶管理的金鑰來加密磁碟。 語法與從映像建立[一般化](vm-generalized-image-version-powershell.md)或[特製化](vm-specialized-image-version-powershell.md) VM 相同，您需要使用擴充參數集，並將 `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` 加入 VM 設定。

至於資料磁碟，當您使用 [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) 時必須加入 `-DiskEncryptionSetId $setID` 參數。


## <a name="cli"></a>CLI 

若為公開預覽版本，您必須先註冊該功能。 註冊需要大約30分鐘的時間。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

檢查功能註冊的狀態。

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

當此檢查傳回 `"state": "Registered"` 時，您可以移至下一個步驟。

檢查您的註冊。

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

如未顯示 registered，執行下列命令：

```azurecli-interactive
az provider register -n Microsoft.Compute
```


若要為映像版本指定磁碟加密集，使用 [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) 搭配 `--target-region-encryption` 參數。 的格式 `--target-region-encryption` 是用來加密作業系統和資料磁片的金鑰清單（以逗號分隔）。 它看起來應該像這樣：`<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`。 

如果 OS 磁碟的來源是受控磁碟或 VM，請使用 `--managed-image` 指定映像版本的來源。 在此範例中，來源是一個受控映像，具有 OS 磁碟以及位於 LUN 0 的資料磁碟。 OS 磁碟將會使用 DiskEncryptionSet1 加密，且資料磁碟將會使用 DiskEncryptionSet2 加密。

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

如果 OS 磁碟的來源是快照集，請使用 `--os-snapshot` 來指定 OS 磁碟。 如果有資料磁碟快照集也應該是映像版本的一部分，請將其加入，使用 `--data-snapshot-luns` 指定邏輯單元編號 (LUN) 以及使用 `--data-snapshots` 指定快照集。

在此範例中，來源是磁碟快照集。 有一個 OS 磁碟，在 LUN 0 有一個資料磁碟。 OS 磁碟將會使用 DiskEncryptionSet1 加密，且資料磁碟將會使用 DiskEncryptionSet2 加密。

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>建立 VM

您可以從共用映像庫建立 VM，並使用客戶管理的金鑰來加密磁碟。 語法與從映射建立[一般化](vm-generalized-image-version-cli.md)或[特製化](vm-specialized-image-version-cli.md) VM 相同，您只需要使用加密集的識別碼來加入 `--os-disk-encryption-set` 參數即可。 對於資料磁碟，請加入 `--data-disk-encryption-sets`，並以空格分隔資料磁碟的磁碟加密集清單。


## <a name="portal"></a>入口網站

當您在入口網站中建立映射版本時，您可以使用 [ **加密** ] 索引標籤輸入套用您的儲存體加密集。

> [!IMPORTANT]
> 若要使用雙重加密，您必須使用此連結 [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) 來存取 Azure 入口網站。 靜態加密目前不會顯示在公用 Azure 入口網站中，而不需要使用連結。


1. 在 [建立映像版本] 頁面上，選取 [加密] 索引標籤。
2. 在 [ **加密類型** ] 中，選取 **使用客戶管理的金鑰進行待用加密** ，或 **使用平臺管理和客戶管理的金鑰進行雙重加密** 。 
3. 為映像中的每個磁碟，從下拉式選單中選取要使用的 **磁碟加密集** 。 

### <a name="create-the-vm"></a>建立 VM

您可以從映射版本建立 VM，並使用客戶管理的金鑰來加密磁片。 當您在入口網站中建立 VM 時，請在 [ **磁片** ] 索引標籤上，選取 [ **使用客戶管理的金鑰進行待用加密** ]，或針對 **加密類型****使用由平臺管理和客戶管理的金鑰進行雙重加密** 。 然後，您可以從下拉式選單中選取加密集。

## <a name="next-steps"></a>後續步驟

深入瞭解[伺服器端磁碟加密](./windows/disk-encryption.md)。

如需有關如何提供採購方案資訊的詳細資訊，請參閱 [在建立映射時提供 Azure Marketplace 採購方案資訊](marketplace-images.md)。
