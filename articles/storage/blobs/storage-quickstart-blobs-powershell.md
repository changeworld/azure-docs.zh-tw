---
title: 快速入門 - 使用 PowerShell 建立 Blob
titleSuffix: Azure Storage
description: 在本快速入門中，您會在物件 (Blob) 儲存體中使用 Azure PowerShell。 然後使用 PowerShell，將 blob 上傳至 Azure 儲存體、下載 blob，以及列出容器中的 blob。
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: tamram
ms.openlocfilehash: 4cc58838827d1ee9337216d9ccb56696735ead7e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664231"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>快速入門：使用 PowerShell 上傳、下載及列出 Blob

使用Azure PowerShell 模組建立及管理 Azure 資源。 您可以從 PowerShell 命令列或在指令碼中建立和管理 Azure 資源。 本指南說明如何使用 PowerShell 在本機磁碟和 Azure Blob 儲存體之間傳輸檔案。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prerequisites

若要存取 Azure 儲存體，您需要有 Azure 訂用帳戶。 如果您還沒有訂用帳戶，則先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本快速入門需要 Azure PowerShell 模組 Az 0.7 版或更新版本。 執行 `Get-InstalledModule -Name Az -AllVersions | select Name,Version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>建立容器

Blob 一律會上傳到容器中。 您可以組織 Blob 群組，如同在電腦的資料夾中組織檔案一般。

設定容器名稱，然後使用 [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer) 來建立容器。 請設定 `blob` 的權限，以允許檔案的公用存取。 此範例中的容器名稱是 *quickstartblobs*。

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>將 Blob 上傳到容器

Blob 儲存體支援區塊 Blob、附加 Blob 和分頁 Blob。 用來備份 IaaS VM 的 VHD 檔案是分頁 Blob。 附加 Blob 可用於記錄，例如，當您想要寫入檔案，並繼續新增更多資訊時。 儲存在 Blob 儲存體中的大部分檔案都是區塊 Blob。 

若要將檔案上傳至區塊 Blob，請取得容器參考，然後取得該容器中區塊 Blob 的參考。 取得 Blob 參考之後，即可使用 [Set-AzStorageBlobContent](/powershell/module/az.storage/set-AzStorageblobcontent) 將資料上傳至 Blob。 如果 Blob 不存在，此作業會予以建立，若已存在，則加以覆寫。

下列範例會將本機磁碟上 *D:\\_TestImages* 資料夾中的 *Image001.jpg* 和 *Image002.png* 上傳至您所建立的容器。

```powershell
# upload a file
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

請上傳您所需的檔案數量，再繼續進行。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

使用 [Get-AzStorageBlob](/powershell/module/az.storage/get-AzStorageblob) 來取得容器中 Blob 的清單。 此範例只會顯示已上傳之 Blob 的名稱。

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>下載 Blob

將 blob 下載到本機磁碟。 針對您要下載每個 Blob，請設定名稱並呼叫[Get-AzStorageBlobContent](/powershell/module/az.storage/get-AzStorageblobcontent) 以下載 Blob。

此範例會將 Blob 下載到本機磁碟上的 *D:\\_TestImages\Downloads*。 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>使用 AzCopy 進行資料轉送

AzCopy 命令列公用程式可為 Azure 儲存體提供高效能、可編寫指令碼的資料傳輸。 您可以使用 AzCopy 將資料傳輸到 Blob 儲存體和 Azure 檔案儲存體，以及從 Blob 儲存體和 Azure 檔案儲存體傳出資料。 如需 AzCopy v10 (AzCopy 的最新版本) 的詳細資訊，請參閱[開始使用 AzCopy](../common/storage-use-azcopy-v10.md)。 若要了解如何搭配使用 AzCopy v10 與 Blob 儲存體，請參閱[使用 AzCopy 和 Blob 儲存體傳輸資料](../common/storage-use-azcopy-blobs.md)。

下列範例會使用 AzCopy 將本機檔案上傳至 Blob。 請記得要以您自己的值取代範例值：

```powershell
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>清除資源

移除您已建立的所有資產。 要移除資產，最簡單的方法是刪除資源群組。 移除資源群組時也會刪除群組內包含的所有資源。 在下列範例中，移除資源群組時也會移除儲存體帳戶和資源群組本身。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已在本機磁碟和 Azure Blob 儲存體之間傳輸檔案。 若要深入了解如何透過 PowerShell 使用 Blob 儲存體，請繼續參閱「如何搭配使用 Azure PowerShell 與 Azure 儲存體」。

> [!div class="nextstepaction"]
> [搭配使用 Azure PowerShell 與 Azure 儲存體](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Microsoft Azure PowerShell 儲存體 Cmdlet 參考

* [儲存體 PowerShell Cmdlet](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 儲存體總管

* [Microsoft Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 是一個免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。
