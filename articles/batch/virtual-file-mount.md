---
title: 在集區上掛接虛擬檔案系統-Azure Batch |Microsoft Docs
description: 瞭解如何在 Batch 集區上掛接虛擬檔案系統。
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: 703b65f0a1571659d7be479776dd8fdf02d86731
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117024"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>在 Batch 集區上掛接虛擬檔案系統

Azure Batch 現在支援在 Batch 集區中的 Windows 或 Linux 計算節點上裝載雲端儲存體或外部檔案系統。 當計算節點加入集區時，虛擬檔案系統會掛接並視為該節點上的本機磁片磁碟機。 您可以掛接檔案系統，例如 Azure 檔案儲存體、Azure Blob 儲存體、網路檔案系統（NFS），包括[Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)快取或通用網際網路檔案系統（CIFS）。

在本文中，您將瞭解如何使用[適用于 .net 的 Batch 管理程式庫](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet)，在計算節點的集區上掛接虛擬檔案系統。

> [!NOTE]
> 在2019-08-19 或之後建立的 Batch 集區上，支援裝載虛擬檔案系統。 在2019-08-19 之前建立的 Batch 集區不支援這項功能。
> 
> 在計算節點上裝載檔案系統的 Api 是[Batch .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet)程式庫的一部分。

## <a name="benefits-of-mounting-on-a-pool"></a>在集區上裝載的優點

將檔案系統掛接到集區，而不是讓工作從大型資料集取出自己的資料，讓工作更容易且更有效率地存取必要的資料。

假設有多個工作需要存取一組常用的資料，例如轉譯電影。 每個工作會從場景檔案一次呈現一或多個畫面格。 藉由掛接包含場景檔案的磁片磁碟機，計算節點就能更輕鬆地存取共用資料。 此外，基礎檔案系統也可以根據同時存取資料的計算節點數目所需的效能和規模（輸送量和 IOPS），分別進行選擇和調整。 例如， [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)分散式記憶體內部快取可用來支援大規模的動態轉譯節點轉譯，並存取位於內部部署的來源資料。 或者，對於已位於雲端 Blob 儲存體中的資料，您可以使用[blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) ，將此資料掛接為本機檔案系統。 Blobfuse 僅適用于 Linux 節點，不過， [Azure 檔案儲存體](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/)提供類似的工作流程，並可在 Windows 和 Linux 上使用。

## <a name="mount-a-virtual-file-system-on-a-pool"></a>在集區上裝載虛擬檔案系統  

在集區上裝載虛擬檔案系統，可讓檔案系統供集區中的每個計算節點使用。 檔案系統是在計算節點加入集區時，或是節點重新開機或重新安裝映射時進行設定。

若要在集區上裝載檔案系統，請`MountConfiguration`建立物件。 選擇適合您虛擬檔案系統的物件`AzureBlobFileSystemConfiguration`：、 `AzureFileShareConfiguration`、 `NfsMountConfiguration`或。 `CifsMountConfiguration`

所有掛接設定物件都需要下列基底參數。 某些掛接設定具有所使用之檔案系統的特定參數，在程式碼範例中會更詳細地討論。

- **帳戶名稱或來源**：若要掛接虛擬檔案共用，您需要儲存體帳戶或其來源的名稱。
- **相對掛接路徑或來源**：載入計算節點上的檔案系統位置，相對於透過節點可存取`fsmounts`的標準目錄`AZ_BATCH_NODE_MOUNTS_DIR`。 確切的位置會根據節點上所使用的作業系統而有所不同。 例如，Ubuntu 節點上的實體位置會對應至`mnt\batch\tasks\fsmounts`，並在其對應的 CentOS 節點上。 `mnt\resources\batch\tasks\fsmounts`
- **掛接選項或 blobfuse 選項**：這些選項描述用來掛載檔案系統的特定參數。

建立`MountConfiguration`物件之後，當您建立集區時， `MountConfigurationList`請將物件指派給屬性。 當節點加入集區，或節點重新開機或重新安裝映射時，就會掛接檔案系統。

掛接檔案系統時，會建立環境變數`AZ_BATCH_NODE_MOUNTS_DIR` ，以指向掛接的檔案系統位置，以及記錄檔，這對於疑難排解和偵錯工具很有用。 在[診斷掛接錯誤](#diagnose-mount-errors)一節中，將會更詳細地說明記錄檔。  

> [!IMPORTANT]
> 集區上裝載的檔案系統數目上限為10。 如需詳細資訊和其他限制，請參閱[Batch 服務配額和限制](batch-quota-limit.md#other-limits)。

## <a name="examples"></a>範例

下列程式碼範例示範如何將各種檔案共用裝載至計算節點集區。

### <a name="azure-files-share"></a>Azure 檔案儲存體共用

Azure 檔案儲存體是標準的 Azure 雲端檔案系統供應專案。 若要深入瞭解如何取得掛接設定程式碼範例中的任何參數，請參閱[使用 Azure 檔案儲存體共用](../storage/files/storage-how-to-use-files-windows.md)。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure Blob 檔案系統

另一個選項是透過[blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)使用 Azure Blob 儲存體。 裝載 blob 檔案系統需要您的`AccountKey`儲存體`SasKey`帳戶或。 如需取得這些金鑰的資訊，請參閱[管理儲存體帳戶存取金鑰](../storage/common/storage-account-keys-manage.md)，或[使用共用存取簽章（SAS）](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。 如需使用 blobfuse 的詳細資訊，請參閱 blobfuse[疑難排解常見問題](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)。 若要取得 blobfuse 裝載目錄的預設存取權，請以**系統管理員**身分執行工作。 Blobfuse 會在使用者空間裝載目錄，而在建立集區時，它會裝載為 root。 在 Linux 中，所有**系統管理員**工作都是 root。 熔斷器模組的所有選項都會在 [[保險絲參考] 頁面](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)中說明。

除了疑難排解指南以外，blobfuse 存放庫中的 GitHub 問題也是檢查目前 blobfuse 問題和解決方法的實用方式。 如需詳細資訊，請參閱[blobfuse 問題](https://github.com/Azure/azure-storage-fuse/issues)。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>網路檔案系統

網路檔案系統（NFS）也可以掛接到集區節點，讓 Azure Batch 的節點能夠輕鬆地存取傳統檔案系統。 這可能是部署在雲端中的單一 NFS 伺服器，或透過虛擬網路存取的內部部署 NFS 伺服器。 或者，利用[Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)分散式記憶體內部快取解決方案，其可提供與內部部署儲存體的順暢連線、視需要將資料讀取到其快取，以及為雲端式計算節點提供高效能和規模調整。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>一般網際網路檔案系統

一般的網際網路檔案系統（CIFS）也可以掛接到集區節點，讓 Azure Batch 的節點能夠輕鬆地存取傳統檔案系統。 CIFS 是一個檔案共用通訊協定，可提供開放且跨平臺的機制來要求網路伺服器檔案和服務。 CIFS 是以 Microsoft 伺服器訊息區（SMB）通訊協定（用於網際網路和內部網路檔案共用）的增強型版本為基礎，並用於在 Windows 節點上掛接外部檔案系統。 若要深入瞭解 SMB，請參閱[檔案伺服器和 smb](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview)。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>診斷掛接錯誤

如果掛接設定失敗，集區中的計算節點將會失敗，且節點狀態會變成無法使用。 若要診斷掛接設定失敗，請檢查[`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)屬性，以取得錯誤的詳細資料。

若要取得用於進行偵錯工具的記錄[OutputFiles](batch-task-output-files.md)檔，請使用`*.log` OutputFiles 上傳檔案。 `*.log`檔案包含`AZ_BATCH_NODE_MOUNTS_DIR`位置上檔案系統掛接的相關資訊。 掛接記錄檔的格式為： `<type>-<mountDirOrDrive>.log`每個掛接。 例如，掛接在`cifs`名為`test`的掛接目錄中，將會有一個名為的`cifs-test.log`掛接記錄檔：。

## <a name="supported-skus"></a>支援的 SKU

| 發行者 | 產品 | SKU | Azure 檔案儲存體共用 | Blobfuse | NFS 掛接 | CIFS 掛接 |
|---|---|---|---|---|---|---|
| 批次 | rendering-centos73 | 轉譯 | :heavy_check_mark: <br>注意：與 CentOS 7.7 相容</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS，18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>注意：與 CentOS 7.4 相容。 </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>注意：支援 A_8 或9個儲存體</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4、7.3、7。1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012、2016、2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>後續步驟

- 深入瞭解使用[Windows](../storage/files/storage-how-to-use-files-windows.md)或[Linux](../storage/files/storage-how-to-use-files-linux.md)掛接 Azure 檔案儲存體共用的詳細資料。
- 瞭解如何使用和裝載[blobfuse](https://github.com/Azure/azure-storage-fuse)虛擬檔案系統。
- 如需瞭解 NFS 及其應用程式的詳細資訊，請參閱[網路檔案系統總覽](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)。
- 如需深入瞭解 CIFS 的詳細資訊，請參閱[MICROSOFT SMB 通訊協定和 CIFS 通訊協定總覽](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)。
