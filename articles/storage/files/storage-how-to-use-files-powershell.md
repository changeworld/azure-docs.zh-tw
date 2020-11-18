---
title: 使用 Azure PowerShell 管理 Azure 檔案共用的快速入門
description: 使用此快速入門了解如何使用 Azure PowerShell 管理 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8b4bd9ece5f010f1294356ad4673543834e5076a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626906"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立及管理 Azure 檔案共用 
本指南會逐步說明透過 PowerShell 來使用 [Azure 檔案共用](storage-files-introduction.md)的基本概念。 Azure 檔案共用與其他檔案共用類似，但它儲存在雲端中，並且由 Azure 平台支援。 Azure 檔案共用支援業界標準的伺服器訊息區 (SMB) 通訊協定、網路檔案系統 (NFS) 通訊協定 (預覽)，並可橫跨多部機器、應用程式和執行個體來啟用檔案共用。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您想要在本機安裝和使用 PowerShell，則本指南將需要 Azure PowerShell 模組 Az 0.7 版或更新版本。 若要確認您所執行的 Azure PowerShell 模組版本，請執行 `Get-Module -ListAvailable Az`。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzAccount` 來登入 Azure 帳戶。

## <a name="create-a-resource-group"></a>建立資源群組
資源群組是在其中部署與管理 Azure 資源的邏輯容器。 如果您尚未擁有 Azure 資源群組，則可以使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)Cmdlet 來建立一個新資源群組。 

下列範例會在「美國西部 2」區域建立名為 myResourceGroup 的資源群組：

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶
儲存體帳戶是可供您用來部署 Azure 檔案共用的共用儲存體集區。 儲存體帳戶可包含無限制數目的共用，而共用可儲存無限制數目的檔案，最多可達儲存體帳戶的容量限制。 本範例會建立一般用途第 2 版 (GPv2 儲存體帳戶)，其可以在硬碟 (HDD) 轉動式媒體上儲存標準 Azure 檔案共用或其他儲存體資源 (例如 Blob 或佇列)。 Azure 檔案儲存體也支援進階固態硬碟 (SSD)；FileStorage 儲存體帳戶中可建立進階 Azure 檔案共用。

此範例會使用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) Cmdlet 來建立儲存體帳戶。 儲存體帳戶的名稱為 mystorageaccount\<random number>，而且該儲存體帳戶的參考會儲存在 **$storageAcct** 變數中。 儲存體帳戶名稱必須是唯一的，因此，請使用 `Get-Random` 為名稱附加一個數字，使其成為唯一名稱。 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> 大於 5 TiB 的共用 (每個共用最多可有 100 TiB) 僅適用於本地備援 (LRS) 和區域備援 (ZRS) 儲存體帳戶。 若要建立異地備援 (GRS) 或異地區域備援 (GZRS) 儲存體帳戶，請移除 `-EnableLargeFileShare` 參數。

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用
現在，您可以建立第一個 Azure 檔案共用。 您可以使用 [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare) Cmdlet 來建立檔案共用。 此範例會建立名為 `myshare` 的共用。

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

共用名稱必須全部使用小寫字母、數字和單一連字號，但開頭不可以是連字號。 如需有關為檔案共用與檔案命名的完整詳細資料，請參閱 [命名和參考共用、目錄、檔案及中繼資料](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)。

## <a name="use-your-azure-file-share"></a>使用您的 Azure 檔案共用
Azure 檔案服務提供兩個在 Azure 檔案共用中使用檔案和資料夾的方法：業界標準[伺服器訊息區 (SMB) 通訊協定](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)和[檔案 REST 通訊協定](/rest/api/storageservices/file-service-rest-api)。 

若要使用 SMB 掛接檔案共用，請根據您的作業系統參閱下列文件：
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>透過檔案 REST 通訊協定使用 Azure 檔案共用 
您可以使用 File REST 通訊協定 (也就是自己製作 REST HTTP 呼叫)，但最常見的 File REST 通訊協定使用方式是使用 Azure PowerShell 模組、[Azure CLI](storage-how-to-use-files-cli.md) 或「Azure儲存體 SDK」，這些都能以您選擇的指令碼/程式設計語言為 File REST 通訊協定提供良好的包裝函式。  

在大部分的情況下，您會透過 SMB 通訊協定使用 Azure 檔案共用，因為這可讓您使用預期能使用的現有應用程式和工具，但使用檔案 REST API 比使用 SMB 好的原因有很多個，例如：

- 您要從 PowerShell Cloud Shell (無法透過 SMB 掛接檔案共用) 瀏覽檔案共用。
- 您要利用無伺服器資源，例如 [Azure Functions](../../azure-functions/functions-overview.md)。 
- 您要建立會與許多 Azure 檔案共用互動的加值服務，例如執行備份或防毒掃描。

下列範例示範如何搭配 File REST 通訊協定使用 Azure PowerShell 模組，來操作 Azure 檔案共用。 `-Context` 參數可用來擷取儲存體帳戶金鑰，以對檔案共用執行指定的動作。 若要擷取儲存體帳戶金鑰，您必須具有儲存體帳戶上 `Owner` 的 Azure 角色。

#### <a name="create-directory"></a>建立目錄
若要在 Azure 檔案共用的根目錄建立名為 *myDirectory* 的新目錄，請使用 [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) Cmdlet。

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>上傳檔案
為了示範如何使用 [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) Cmdlet 來上傳檔案，我們必須先在 PowerShell Cloud Shell 的臨時磁碟機內建立要上傳的檔案。 

此範例會將目前的日期和時間放入可用磁碟機上的新檔案中，然後將檔案上傳至檔案共用。

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

如果您要在本機執行 PowerShell，則應將 `~/CloudDrive/` 替換為存在於您的機器上的路徑。

上傳檔案之後，您可以使用 [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) Cmdlet 來確認檔案已上傳至 Azure 檔案共用。 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>下載檔案
您可以使用 [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) Cmdlet，將您剛才上傳的檔案複本下載到 Cloud Shell 的臨時磁碟機。

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

下載檔案之後，您可以使用 `Get-ChildItem` 來確認檔案已下載到 PowerShell Cloud Shell 的可用磁碟機。

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>複製檔案
其中一個常見的工作是將檔案從某個檔案共用複製到另一個檔案共用。 若要示範這項功能，您可以建立新的共用，然後使用 [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) Cmdlet 將您剛才上傳的檔案複製到這個新的共用。 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

現在，當您列出新共用中的檔案時，您應該會看到您所複製的檔案。

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

雖然 `Start-AzStorageFileCopy` Cmdlet 可方便您在 Azure 檔案共用之間移動臨機操作檔案，但若要進行移轉和移動較大型的資料，則建議在 Windows 上使用 `robocopy` 以及在 macOS 和 Linux 上使用 `rsync`。 `robocopy` 和 `rsync` 會使用 SMB 來執行資料移動，而不是使用 FileREST API。

## <a name="create-and-manage-share-snapshots"></a>建立及管理共用快照集
可以使用 Azure 檔案共用來執行的另一項實用工作，是建立共用快照集。 快照集會保留 Azure 檔案共用的時間點。 共用快照集類似於您可能已經很熟悉的下列作業系統技術：

- Windows 檔案系統的[磁碟區陰影複製服務 (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal)，例如 NTFS 和 ReFS。
- Linux 系統的[邏輯磁碟區管理員 (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) 快照集。
- macOS 的 [Apple 檔案系統 (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) 快照集。 

您可以對檔案共用 (使用 [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare)Cmdlet 所擷取) 的 PowerShell 物件使用 `Snapshot` 方法，來為共用建立共用快照集。 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.CloudFileShare.Snapshot()
```

### <a name="browse-share-snapshots"></a>瀏覽共用快照集
您可以將快照集參考 (`$snapshot`) 傳至 `Get-AzStorageFile` Cmdlet 的 `-Share` 參數，以瀏覽共用快照集的內容。

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>列出共用快照集
您可以使用下列命令，檢視您為共用建立的快照集清單。

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>從共用快照集還原
您可以使用我們先前使用的 `Start-AzStorageFileCopy` 命令來還原檔案。 基於此快速入門的用途，我們會先刪除我們先前上傳的 `SampleUpload.txt` 的檔案，以便從快照集加以還原。

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>刪除共用快照集
您可以使用 [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) Cmdlet 搭配包含 `-Share` 參數之 `$snapshot` 參考的變數，來刪除共用快照集。

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>清除資源
完成作業時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) Cmdlet 來移除資源群組及所有相關資源。 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

或者，您可以逐一移除資源：

- 以移除我們為此快速入門建立的 Azure 檔案共用。

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > 您必須先刪除所建立 Azure 檔案共用的所有共用快照集，然後才能刪除 Azure 檔案共用。

- 以移除儲存體帳戶本身 (這表示將會移除我們建立的 Azure 檔案共用，以及您已建立的任何其他儲存體資源，例如 Azure Blob 儲存體容器)。

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [什麼是 Azure 檔案服務？](storage-files-introduction.md)