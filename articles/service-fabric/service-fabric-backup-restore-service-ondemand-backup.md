---
title: Azure Service Fabric 中的隨選備份
description: 使用 Service Fabric 中的備份與還原功能，依照需求備份您的應用程式資料。
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 04d8bb4a9f8157a229751d073e8d351f5448fa68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247892"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Azure Service Fabric 中的隨選備份

您可備份可靠具狀態服務和 Reliable Actors 的資料，以處理災害或資料遺失情況。

Azure Service Fabric 具有用來[定期備份資料](service-fabric-backuprestoreservice-quickstart-azurecluster.md)及依照需求備份資料的功能。 隨選備份很實用，因為它會_data loss_ / 因基礎服務或其環境中的規劃變更而防止資料遺失_資料損毀_。

在您手動觸發服務或服務環境作業之前，隨選備份功能對於擷取服務狀態很有幫助。 例如，如果您在升級或降級服務時變更了服務二進位檔。 在此情況下，隨選備份可協助防範由應用程式程式碼錯誤 (bug) 造成的資料損毀。
## <a name="prerequisites"></a>Prerequisites

- 請安裝 ServiceFabric，以進行設定的呼叫。

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- 使用 ServiceFabric 進行任何設定要求之前，請先使用命令來確定叢集已連線 `Connect-SFCluster` 。

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>觸發隨選備份

隨選備份需要儲存體詳細資料以便上傳備份檔案。 您可以在定期備份原則或隨選備份要求中指定隨選備份位置。

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>定期備份原則所指定的儲存體隨選備份

您可以設定定期備份原則，以使用可靠具狀態服務或 Reliable Actors 的分割區來作為儲存體的額外隨選備份。

下列案例接續＜[啟用可靠具狀態服務和 Reliable Actors 的定期備份](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)＞中的案例。 在此案例中，您會啟用備份原則來使用 Azure 儲存體中以所設定頻率發生的分割和備份。

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>使用 ServiceFabric 的 powershell 模組

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>使用 Powershell 進行 Rest 呼叫

使用 [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) API 可設定分割區識別碼 `974bd92a-b395-4631-8a7f-53bd4ae9cf22` 的隨選備份觸發。

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API 可用來追蹤[隨選備份進度](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)。

### <a name="on-demand-backup-to-specified-storage"></a>隨選備份至指定的儲存體

您可以要求對可靠具狀態服務或 Reliable Actor 的分割區進行隨選備份。 提供儲存體資訊以當作隨選備份要求的一部分。


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>使用 ServiceFabric 的 powershell 模組

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>使用 Powershell 進行 Rest 呼叫

使用 [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) API 可設定分割區識別碼 `974bd92a-b395-4631-8a7f-53bd4ae9cf22` 的隨選備份觸發。 包括下列 Azure 儲存體資訊：

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

您可以使用 [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API 設定[隨選備份進度](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)的追蹤作業。

### <a name="using-service-fabric-explorer"></a>使用 Service Fabric Explorer
請確定已在 Service Fabric Explorer 設定中啟用 Advanced 模式。
1. 選取所需的磁碟分割，然後按一下 [動作]。 
2. 選取 [觸發資料分割備份]，並填入 Azure 的資訊：

    ![觸發資料分割備份][0]

    或檔案共用：

    ![觸發資料分割備份檔案共用][1]

## <a name="tracking-on-demand-backup-progress"></a>追蹤隨選備份進度

可靠具狀態服務或 Reliable Actor 的分割區一次只接受一個隨選備份要求。 只有當目前的隨選備份要求完成後，才可以接受另一個要求。

不同的分割區可同時觸發多個隨選備份要求。


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>使用 ServiceFabric 的 powershell 模組

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>使用 Powershell 進行 Rest 呼叫

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

隨選備份要求可能會有下列狀態：

- 已**接受**：備份已在分割區上啟動且正在進行中。
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **成功**、 **失敗**或 **超時**：要求的隨選備份可在下列任何狀態中完成：
  - **成功**： _成功_ 備份狀態表示已成功備份分割區狀態。 回應會提供分割區的 _BackupEpoch_ 和 _BackupLSN_ 以及 UTC 時間。
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **失敗**： _失敗_ 備份狀態表示備份分割區狀態期間發生失敗。 回應中會陳述失敗的原因。
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Timeout**： _timeout_ 備份狀態表示無法在指定的時間內建立資料分割狀態備份。 預設的逾時值為 10 分鐘。 在此案例中，請以提高的 [BackupTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) 值起始新的隨選備份要求。
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>接下來的步驟

- [了解定期備份組態](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [BackupRestore REST API 參考](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png
