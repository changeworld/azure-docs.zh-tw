---
title: 在集區上掛接虛擬檔案系統
description: 了解如何在 Batch 集區上掛接虛擬檔案系統。
ms.topic: how-to
ms.date: 08/13/2019
ms.openlocfilehash: 80acf5df0cf5262249b2eac584152744a4224a35
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954667"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>在 Batch 集區上掛接虛擬檔案系統

Azure Batch 現在支援在 Batch 集區中的 Windows 或 Linux 計算節點上掛接雲端儲存空間或外部檔案系統。 當計算節點加入集區時，就會掛接虛擬檔案系統並將其視為該節點上的本機磁碟機。 您可以掛接檔案系統，例如 Azure 檔案儲存體、Azure Blob 儲存體、網路檔案系統 (NFS)，包括 [Avere vFXT 快取](../avere-vfxt/avere-vfxt-overview.md)或 Common Internet File System (CIFS)。

在本文中，您將了解如何使用[適用於 .NET 的 Batch 管理程式庫](/dotnet/api/overview/azure/batch?view=azure-dotnet)，在計算節點的集區上掛接虛擬檔案系統。

> [!NOTE]
> 在 2019-08-19 當天起建立的 Batch 集區上，支援掛接虛擬檔案系統。 在 2019-08-19 之前建立的 Batch 集區則不支援此功能。
> 
> 用於在計算節點上掛接檔案系統的 API 是 [Batch .NET](/dotnet/api/microsoft.azure.batch?view=azure-dotnet) 程式庫的一部分。

## <a name="benefits-of-mounting-on-a-pool"></a>在集區上掛接的優點

將檔案系統掛接到集區，而不是讓工作從大型資料集擷取自己的資料，會讓工作更容易且更有效率地存取必要的資料。

請設想有多個工作需要存取一組常用資料的情況，例如呈現電影。 每個工作都會從場景檔案一次呈現一或多個畫面格。 藉由掛接包含場景檔案的磁碟機，計算節點就能更輕鬆地存取共用資料。 此外，也可以根據同時存取資料的計算節點數量所需的效能和規模 (輸送量和 IOPS)，分別選擇和擴縮基礎檔案系統。 例如，[Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) 分散式記憶體內部快取可使用數千個同時呈現節點來支援大規模的動畫呈現，以存取位於內部部署環境的來源資料。 或者，對於已位於雲端式 Blob 儲存體的資料，[Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) 可用來將此資料掛接為本機檔案系統。 Blobfuse 僅適用於 Linux 節點，不過，[Azure 檔案儲存體](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/)可提供類似的工作流程，並同時適用於 Windows 和 Linux。

## <a name="mount-a-virtual-file-system-on-a-pool"></a>在集區上掛接虛擬檔案系統  

在集區上掛接虛擬檔案系統，會讓檔案系統可供集區中的每個計算節點使用。 檔案系統會在計算節點加入集區時，或是節點重新啟動或重新安裝映像時進行設定。

若要在集區上掛接檔案系統，請建立 `MountConfiguration` 物件。 選擇適合您虛擬檔案系統的物件：`AzureBlobFileSystemConfiguration`、`AzureFileShareConfiguration`、`NfsMountConfiguration` 或 `CifsMountConfiguration`。

所有掛接設定物件都需要下列基礎參數。 某些掛接設定具有所使用檔案系統的特定參數，程式碼範例中會更加詳細地討論這些參數。

- **帳戶名稱或來源**：若要掛接虛擬檔案共用，您需要儲存體帳戶或其來源的名稱。
- **相對掛接路徑或來源**：掛接於計算節點上的檔案系統所在位置 (相對於節點上可透過 `AZ_BATCH_NODE_MOUNTS_DIR` 來存取的標準 `fsmounts` 目錄)。 確切位置會根據節點上所使用的作業系統而有所不同。 例如，Ubuntu 節點上的實體位置會對應到 `mnt\batch\tasks\fsmounts`，而在 CentOS 節點上則會對應到 `mnt\resources\batch\tasks\fsmounts`。
- **掛接選項或 Blobfuse 選項**：這些選項會說明用來掛接檔案系統的特定參數。

`MountConfiguration` 物件建立之後，請於建立集區時將此物件指派給 `MountConfigurationList` 屬性。 檔案系統會在節點加入集區時，或是節點重新啟動或重新安裝映像時進行掛接。

檔案系統掛接時會建立環境變數 `AZ_BATCH_NODE_MOUNTS_DIR`，其指向所掛接檔案系統以及記錄檔的所在位置，這對於疑難排解和偵錯很有用。 在[診斷掛接錯誤](#diagnose-mount-errors)一節中，將會更詳細地說明記錄檔。  

> [!IMPORTANT]
> 集區上所掛接的檔案系統數目上限為 10 個。 如需詳細資訊和其他限制，請參閱 [Batch 服務配額和限制](batch-quota-limit.md#other-limits)。

## <a name="examples"></a>範例

下列程式碼範例會示範如何將各種檔案共用掛接至計算節點集區。

### <a name="azure-files-share"></a>Azure 檔案儲存體共用

Azure 檔案儲存體是標準的 Azure 雲端檔案系統供應項目。 若要深入了解如何取得掛接設定程式碼範例中的任何參數，請參閱[使用 Azure 檔案儲存體共用](../storage/files/storage-how-to-use-files-windows.md)。

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
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure Blob 檔案系統

另一個選項是透過 [Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) 使用 Azure Blob 儲存體。 掛接 Blob 檔案系統需要儲存體帳戶的 `AccountKey` 或 `SasKey`。 如需如何取得這些金鑰的資訊，請參閱[管理儲存體帳戶存取金鑰](../storage/common/storage-account-keys-manage.md)或[使用共用存取簽章 (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。 如需如何使用 Blobfuse 的詳細資訊，請參閱 Blobfuse [疑難排解常見問題集](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)。 若要取得 Blobfuse 掛接目錄的預設存取權，請以**系統管理員**身分執行工作。 Blobfuse 會在使用者空間掛接目錄，而在集區建立時則會掛接為根目錄。 在 Linux 中，所有**系統管理員**工作都是根目錄。 [FUSE 參考頁面](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)中會說明 FUSE 模組的所有選項。

除了疑難排解指南外，Blobfuse 存放庫中的 GitHub 問題也可用來檢查目前的 Blobfuse 問題和解決方案。 如需詳細資訊，請參閱 [Blobfuse 問題](https://github.com/Azure/azure-storage-fuse/issues)。

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

網路檔案系統 (NFS) 也可以掛接到集區節點，讓 Azure Batch 節點能夠輕鬆地存取傳統檔案系統。 這可能是部署在雲端中的單一 NFS 伺服器，也可能是透過虛擬網路存取的內部部署 NFS 伺服器。 或者，請利用 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) 分散式記憶體內部快取解決方案，其可讓您順暢地連線到內部部署儲存體、視需要將資料讀取到其快取，以及為雲端式計算節點提供高效能和擴縮。

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

### <a name="common-internet-file-system"></a>Common Internet File System

Common Internet File System (CIFS) 也可以掛接到集區節點，讓 Azure Batch 節點能夠輕鬆地存取傳統檔案系統。 CIFS 是一個檔案共用通訊協定，可提供開放且跨平台的機制來要求網路伺服器檔案和服務。 CIFS 的基礎是 Microsoft 的伺服器訊息區 (SMB) 通訊協定 (用於網際網路和內部網路檔案共用) 增強型版本，並且可以用來在 Windows 節點上掛接外部檔案系統。 若要深入了解 SMB，請參閱[檔案伺服器和 SMB](/windows-server/storage/file-server/file-server-smb-overview)。

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

如果掛接設定失敗，集區中的計算節點就會失敗，且節點狀態會變成無法使用。 若要診斷掛接設定失敗，請檢查 [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) 屬性，以取得錯誤的詳細資料。

若要取得用於偵錯的記錄檔，請使用 [OutputFiles](batch-task-output-files.md) 來上傳 `*.log` 檔案。 `*.log` 檔案包含 `AZ_BATCH_NODE_MOUNTS_DIR` 位置的檔案系統掛接相關資訊。 掛接記錄檔的格式為：每個掛接的 `<type>-<mountDirOrDrive>.log`。 例如，在名為 `test` 的掛接目錄上，`cifs` 掛接將會有下列名稱的掛接記錄檔：`cifs-test.log`。

## <a name="supported-skus"></a>支援的 SKU

| 發行者 | 供應項目 | SKU | Azure 檔案儲存體共用 | Blobfuse | NFS 掛接 | CIFS 掛接 |
|---|---|---|---|---|---|---|
| 批次 | rendering-centos73 | 轉譯 | :heavy_check_mark: <br>注意:與 CentOS 7.7 相容</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS、18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>注意:與 CentOS 7.4 相容。 </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>注意:支援 A_8 或 9 儲存體</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4、7.3、7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012、2016、2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>後續步驟

- 深入了解如何使用 [Windows](../storage/files/storage-how-to-use-files-windows.md) 或 [Linux](../storage/files/storage-how-to-use-files-linux.md) 來掛接 Azure 檔案儲存體共用。
- 了解如何使用和掛接 [Blobfuse](https://github.com/Azure/azure-storage-fuse) 虛擬檔案系統。
- 若要了解 NFS 及其應用程式，請參閱[網路檔案系統概觀](/windows-server/storage/nfs/nfs-overview)。
- 若要深入了解 CIFS，請參閱 [Microsoft SMB 通訊協定和 CIFS 通訊協定概觀](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)。
