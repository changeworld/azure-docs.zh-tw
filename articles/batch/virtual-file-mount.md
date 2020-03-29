---
title: 在池上安裝虛擬檔案系統 - Azure 批次處理 |微軟文檔
description: 瞭解如何在批次處理池上安裝虛擬檔案系統。
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: bdf0b3bfc955d8a2e2ce1b363c8699ca719b957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919000"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>在批次處理池上安裝虛擬檔案系統

Azure Batch 現在支援在批次處理池中的 Windows 或 Linux 計算節點上安裝雲存儲或外部檔案系統。 當計算節點加入池時，虛擬檔案系統將裝載並被視為該節點上的本地磁碟機。 您可以裝載檔案系統，如 Azure 檔、Azure Blob 存儲、網路檔案系統 （NFS），包括[Avere vFXT 緩存](../avere-vfxt/avere-vfxt-overview.md)或通用 Internet 檔案系統 （CIFS）。

在本文中，您將學習如何使用[.NET 的批次處理管理庫](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet)在計算節點池上裝載虛擬檔案系統。

> [!NOTE]
> 在 2019-08-19 年或之後創建的批次處理池中支援安裝虛擬檔案系統。 2019-08-19 之前創建的批次處理池不支援此功能。
> 
> 用於在計算節點上安裝檔案系統的 API 是 Batch [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet)庫的一部分。

## <a name="benefits-of-mounting-on-a-pool"></a>安裝在泳池上的好處

將檔案系統安裝到池中，而不是讓任務從大型資料集檢索自己的資料，使任務訪問必要資料變得更加簡單和高效。

考慮具有多個任務需要訪問一組常見資料的方案，例如渲染影片。 每個任務一次從場景檔呈現一個或多個幀。 通過安裝包含場景檔的磁碟機，計算節點更容易訪問共用資料。 此外，還可以根據同時訪問資料的計算節點數所需的性能和規模（輸送量和 IOPS）獨立選擇和縮放基礎檔案系統。 例如[，Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)分散式記憶體緩存可用於支援具有數千個併發渲染節點的大型電影比例渲染，從而訪問駐留在本地的來源資料。 或者，對於已駐留在基於雲的 Blob 存儲中的資料[，Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)可用於將此資料裝載為本地檔案系統。 Blobfuse 僅在 Linux 節點上可用，但是[，Azure 檔](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/)提供了類似的工作流，並且在 Windows 和 Linux 上都可用。

## <a name="mount-a-virtual-file-system-on-a-pool"></a>將虛擬檔案系統安裝到池中  

在池上安裝虛擬檔案系統使檔案系統可供池中的每個計算節點使用。 在計算節點加入池時或重新開機或重新映射節點時配置檔案系統。

要將檔案系統裝載到池中，請創建一`MountConfiguration`個物件。 選擇適合`AzureBlobFileSystemConfiguration`虛擬檔案系統的物件： 、 `AzureFileShareConfiguration`、 `NfsMountConfiguration`、 或`CifsMountConfiguration`。

所有裝載設定物件都需要以下基本參數。 某些裝載配置具有特定于正在使用的檔案系統的參數，代碼示例中將更詳細地討論這些參數。

- **帳戶名稱或源**：要裝載虛擬檔案共用，您需要存儲帳戶或其源的名稱。
- **相對裝載路徑或源**：檔案系統的位置安裝在計算節點上，相對於通過 在`fsmounts``AZ_BATCH_NODE_MOUNTS_DIR`節點上訪問的標準目錄。 確切的位置因節點上使用的作業系統而異。 例如，Ubuntu 節點上的物理位置映射到`mnt\batch\tasks\fsmounts`，在 CentOS 節點上映射到 。 `mnt\resources\batch\tasks\fsmounts`
- **裝載選項或 Blobfuse 選項**：這些選項描述用於安裝檔案系統的特定參數。

創建`MountConfiguration`物件後，在創建池時將物件分配給`MountConfigurationList`該屬性。 當節點加入池或重新開機或重新映射節點時，將裝載檔案系統。

裝載檔案系統時，將創建一個環境變數`AZ_BATCH_NODE_MOUNTS_DIR`，該變數指向裝載的檔案系統以及日誌檔的位置，這些變數可用於故障排除和調試。 日誌檔在["診斷裝載錯誤"](#diagnose-mount-errors)部分中進行了更詳細的解釋。  

> [!IMPORTANT]
> 池上裝載的檔案系統的最大數量為 10。 有關詳細資訊和其他限制，請參閱[批次處理服務配額和限制](batch-quota-limit.md#other-limits)。

## <a name="examples"></a>範例

以下代碼示例演示了將各種檔共用安裝到計算節點池。

### <a name="azure-files-share"></a>Azure 檔共用

Azure 檔是標準 Azure 雲檔案系統產品。 要瞭解有關如何獲取裝載配置代碼示例中的任何參數的更多內容，請參閱[使用 Azure 檔共用](../storage/files/storage-how-to-use-files-windows.md)。

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

另一個選項是使用 Azure Blob 存儲通過[blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)。 安裝 Blob 檔案系統需要`AccountKey`或`SasKey`用於存儲帳戶。 有關獲取這些金鑰的資訊，請參閱[管理存儲帳戶訪問金鑰](../storage/common/storage-account-keys-manage.md)或使用[共用訪問簽名 （SAS）。](../storage/common/storage-dotnet-shared-access-signature-part-1.md) 有關使用 blobfuse 的詳細資訊，請參閱 blobfuse[疑難排解常見問題](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)。 要獲得對 Blobfuse 裝載的目錄的預設存取權限，請以**管理員**身份運行該任務。 Blobfuse 在使用者空間裝載目錄，在創建池時，該目錄將作為根裝載。 在 Linux 中，所有**管理員**任務都是根任務。 FUSE 模組的所有選項都位於 FUSE[參考頁](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)中。

除了故障排除指南之外，Blobfuse 存儲庫中的 GitHub 問題是檢查當前 Blobfuse 問題和解決方法的有用方法。 有關詳細資訊，請參閱[blobfuse 問題](https://github.com/Azure/azure-storage-fuse/issues)。

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

網路檔案系統 （NFS） 也可以裝載到池節點，以便 Azure Batch-節點可以輕鬆訪問傳統檔案系統。 這可能是部署在雲中的單個 NFS 伺服器，也可以是通過虛擬網路訪問的本地 NFS 伺服器。 或者，利用[Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)分散式記憶體緩存解決方案，該解決方案提供與本機存放區的無縫連接，按需將資料讀取到其緩存中，並提供高性能和擴展到基於雲的計算節點。

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

### <a name="common-internet-file-system"></a>通用互聯網檔案系統

通用 Internet 檔案系統 （CIFS） 也可以裝載到池節點，以便 Azure Batch-節點可以輕鬆訪問傳統檔案系統。 CIFS 是一種檔共用協定，它為請求網路伺服器檔和服務提供了一個開放和跨平臺的機制。 CIFS 基於 Microsoft 伺服器訊息區 （SMB） 協定的增強版本，用於 Internet 和 Intranet 檔共用，用於在 Windows 節點上安裝外部檔案系統。 要瞭解有關 SMB 的更多，請參閱[檔案伺服器和 SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview)。

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

## <a name="diagnose-mount-errors"></a>診斷裝載錯誤

如果裝載配置失敗，池中的計算節點將失敗，節點狀態將不可用。 要診斷裝載配置失敗，[`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)請檢查該屬性以查找有關錯誤的詳細資訊。

要獲取用於調試的日誌檔，請使用["輸出檔案"](batch-task-output-files.md)上載`*.log`檔。 這些檔`*.log`包含有關檔案系統在`AZ_BATCH_NODE_MOUNTS_DIR`位置裝載的資訊。 裝載日誌檔的格式為：`<type>-<mountDirOrDrive>.log`針對每個裝載。 例如，`cifs`名為`test`裝載目錄的裝載將具有名為：`cifs-test.log`的裝載日誌檔。

## <a name="supported-skus"></a>支援的 SKU

| 發行者 | 供應項目 | SKU | Azure 檔共用 | Blobfuse | NFS 安裝 | CIFS 安裝 |
|---|---|---|---|---|---|---|
| 批次 | rendering-centos73 | 轉譯 | :heavy_check_mark: <br>注意：與 CentOS 7.7 相容</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS， 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>注意：與 CentOS 7.4 相容。 </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>注意：支援A_8或 9 個存儲</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>後續步驟

- 瞭解有關將 Azure 檔共用與[Windows](../storage/files/storage-how-to-use-files-windows.md)或[Linux](../storage/files/storage-how-to-use-files-linux.md)共用的更多詳細資訊。
- 瞭解使用和安裝[Blobfuse](https://github.com/Azure/azure-storage-fuse)虛擬檔案系統。
- 請參閱[網路檔案系統概述](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)以瞭解 NFS 及其應用程式。
- 請參閱[Microsoft SMB 協定和 CIFS 協定概述](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)，瞭解有關 CIFS 的詳細資訊。
