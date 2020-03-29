---
title: 設置和管理 Blob 存儲的不變性策略 - Azure 存儲
description: 瞭解如何使用 WORM（一次寫入、讀取多個）支援 Blob（物件）存儲，以在指定間隔內以不可哥移動、不可修改的狀態存儲資料。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 05a155584f0cb69191883cb82b3db0af435ccc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970105"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>設置和管理 Blob 存儲的不變性策略

Azure Blob 存儲的不可變存儲使使用者能夠將業務關鍵型資料物件存儲在 WORM（一次寫入、讀取多個）狀態中。 此狀態讓資料在使用者指定的間隔內不可清除，也不可修改。 在保留間隔的持續時間內，可以創建和讀取 blob，但不能修改或刪除 Blob。 不可變存儲可用於所有 Azure 區域中的通用 v2 和 Blob 存儲帳戶。

本文演示如何使用 Azure 門戶、PowerShell 或 Azure CLI 設置和管理 Blob 存儲中資料的不變性策略和法律保留。 有關不可變存儲的詳細資訊，請參閱[使用不可變存儲存儲業務關鍵型 Blob 資料](storage-blob-immutable-storage.md)。

## <a name="set-retention-policies-and-legal-holds"></a>設置保留原則和法律保留

### <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 建立新的容器或選取現有容器，以儲存必須保持固定狀態的 Blob。 容器必須位於通用 v2 或 Blob 存儲帳戶中。

2. 在容器設定中選取 [存取原則]****。 然後選擇 **"在****不可改變的 blob 存儲**下添加策略"。

    ![入口網站中的容器設定](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. 若要啟用以時間為基礎的保留，請從下拉式功能表中選取 [以時間為基礎的保留]****。

    ![在 [原則類型] 下選取的 [以時間為基礎的保留]](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. 輸入保留間隔（以天為單位（可接受的值為 1 到 146000 天）。

    ![[更新保留期限] 方塊](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    策略的初始狀態已解鎖，允許您在鎖定該功能之前測試該功能並更改策略。 鎖定政策對於遵守 SEC 17a-4 等法規至關重要。

5. 鎖定原則。 按右鍵省略號 （**...**）， 下面功能表將顯示其他操作：

    ![功能表上的 [鎖定原則]](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. 選擇**鎖定策略**並確認鎖定。 策略現已鎖定且無法刪除，只允許延長保留間隔。 不允許刪除和覆蓋 Blob。 

    ![確認功能表上的"鎖定策略"](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. 要啟用合法保留，請選擇"**添加策略**"。 從下拉式功能表中選取 [合法保存]****。

    ![在功能表上 [原則類型] 之下的 [合法保存]](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. 建立具有一或多個標記的合法保存。

    ![原則類型之下的 [標記名稱] 方塊](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. 要清除合法保留，請刪除應用的法律保留識別碼標記。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

此功能包含在下列命令群組中：`az storage container immutability-policy` 和 `az storage container legal-hold`。 在其上執行 `-h` 來查看命令。

### <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az.存儲模組支援不可變存儲。  若要啟用此功能，請依照下列步驟執行：

1. 確定您已安裝最新版的 PowerShellGet：`Install-Module PowerShellGet –Repository PSGallery –Force`。
2. 移除任何先前安裝的 Azure PowerShell。
3. 安裝 Azure PowerShell：`Install-Module Az –Repository PSGallery –AllowClobber`。

以下 PowerShell 範例指令碼僅供參考。 此指令碼會建立新的儲存體帳戶和容器。 然後，它演示如何設置和清除法律保留、創建和鎖定基於時間的保留原則（也稱為不變性策略），並延長保留間隔。

首先，創建 Azure 存儲帳戶：

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

設定及清除法務保存措施：

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

創建或更新基於時間的不變性策略：

```powershell
# Create a time-based immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

擷取不變性原則：

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

鎖定不變性策略（添加`-Force`以關閉提示）：

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

擴充不變性原則：

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

刪除未鎖定的不變性策略（添加`-Force`以關閉提示）：

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>啟用允許受保護的追加 Blob 寫入

### <a name="portal"></a>[入口網站](#tab/azure-portal)

![允許其他追加寫入](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

此功能包含在下列命令群組中：`az storage container immutability-policy` 和 `az storage container legal-hold`。 在其上執行 `-h` 來查看命令。

### <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

```powershell
# Create an immutablity policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>後續步驟

[使用不可變存儲存儲業務關鍵型 Blob 資料](storage-blob-immutable-storage.md)
