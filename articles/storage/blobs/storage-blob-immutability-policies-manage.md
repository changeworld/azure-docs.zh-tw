---
title: 設定和管理 Blob 儲存體的不變性原則 - Azure 儲存體
description: 了解如何將 WORM (一次寫入，多次讀取) 支援用於 Blob (物件) 儲存體，以在指定的間隔內以不可清除、不可修改的狀態儲存資料。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 539154135c35e034c889294d911fb53b3d45daa4
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771004"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>設定和管理 Blob 儲存體的不變性原則

Azure Blob 儲存體的固定儲存體可讓使用者以 WORM (一次寫入，多次讀取) 狀態儲存業務關鍵資料。 此狀態讓資料在使用者指定的間隔內不可清除，也不可修改。 在保留間隔的持續時間內可以建立和讀取 Blob，但無法加以修改或刪除。 固定儲存體適用於所有 Azure 區域的一般用途 v2 和 Blob 儲存體帳戶。

本文說明如何使用 Azure 入口網站、PowerShell 或 Azure CLI，針對 Blob 儲存體中的資料設定和管理不變性原則和法務保存。 如需固定儲存體的詳細資訊，請參閱[使用固定儲存體儲存業務關鍵 Blob 資料](storage-blob-immutable-storage.md)。

## <a name="set-retention-policies-and-legal-holds"></a>設定保留原則和法務保存

### <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 建立新的容器或選取現有容器，以儲存必須保持固定狀態的 Blob。 容器必須位於一般用途 v2 或 Blob 儲存體帳戶中。

2. 在容器設定中選取 [存取原則]。 然後，選取 [固定 Blob 儲存體] 底下的 [新增原則]。

    ![入口網站中的容器設定](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. 若要啟用以時間為基礎的保留，請從下拉式功能表中選取 [以時間為基礎的保留]。

    ![在 [原則類型] 下選取的 [以時間為基礎的保留]](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. 輸入保留間隔天數 (可接受的值為 1 到 146000 天)。

    ![[更新保留期限] 方塊](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    原則的初始狀態為「已解除鎖定」，可讓您測試功能並對原則進行變更，然後再進行鎖定。 若要符合 SEC 17a-4 等法規，鎖定原則是不可或缺的。

5. 鎖定原則。 以滑鼠右鍵按一下省略符號 ( **...** )，下列功能表隨即出現，並顯示其他動作：

    ![功能表上的 [鎖定原則]](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. 選取 [鎖定原則] 並確認鎖定。 原則現已鎖定且無法刪除，只允許延長保留間隔。 不允許刪除和覆寫 Blob。 

    ![在功能表上確認 [鎖定原則]](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. 若要啟用法務保存，請選取 [新增原則]。 從下拉式功能表中選取 [合法保存]。

    ![在功能表上 [原則類型] 之下的 [合法保存]](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. 建立具有一或多個標記的合法保存。

    ![原則類型之下的 [標記名稱] 方塊](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. 若要清除法務保存，請移除套用的法務保存識別碼標籤。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

此功能包含在下列命令群組中：`az storage container immutability-policy` 和 `az storage container legal-hold`。 在其上執行 `-h` 來查看命令。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az.Storage 模組支援固定儲存體。  若要啟用此功能，請依照下列步驟執行：

1. 確定您已安裝最新版的 PowerShellGet：`Install-Module PowerShellGet –Repository PSGallery –Force`。
2. 移除任何先前安裝的 Azure PowerShell。
3. 安裝 Azure PowerShell：`Install-Module Az –Repository PSGallery –AllowClobber`。

以下 PowerShell 範例指令碼僅供參考。 此指令碼會建立新的儲存體帳戶和容器。 然後，示範如何設定及清除法務保存、建立及鎖定以時間為基礎的保留原則 (也稱為不變性原則)，以及延長保留間隔。

首先，請建立 Azure 儲存體帳戶：

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
$account = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
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

建立或更新以時間為基礎的不變性原則：

```powershell
# Create a time-based immutability policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

擷取不變性原則：

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

鎖定不變性原則 (新增 `-Force` 可關閉提示)：

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

移除已解除鎖定的不變性原則 (新增 `-Force` 可關閉提示)：

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>啟用允許受保護的附加 Blob 寫入

### <a name="portal"></a>[入口網站](#tab/azure-portal)

![允許其他附加寫入](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

此功能包含在下列命令群組中：`az storage container immutability-policy` 和 `az storage container legal-hold`。 在其上執行 `-h` 來查看命令。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Create an immutability policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>後續步驟

[使用不可變儲存體儲存業務關鍵 Blob 資料](storage-blob-immutable-storage.md)
