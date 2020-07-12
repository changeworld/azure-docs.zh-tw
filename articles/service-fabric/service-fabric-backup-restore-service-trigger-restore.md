---
title: 在 Azure Service Fabric 中還原備份
description: 在 Service Fabric 中使用定期備份與還原功能，從您應用程式資料的備份還原資料。
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: f98bf4f4518abd5f1b1a826e355c851acc055852
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246685"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>在 Azure Service Fabric 中還原備份

在 Azure Service Fabric 中，可靠具狀態服務和 Reliable Actors 可在要求與回應交易完成後，維護可變動授權狀態。 具狀態服務可能會因為災害而長時間停止運作或遺失資訊。 如果發生這種情況，服務必須從可接受的最新備份中還原，讓其可繼續運作。

例如，您可以設定服務來備份其資料，以預防下列情況：

- 嚴重損壞**修復的情況**：永久遺失整個 Service Fabric 叢集。
- **資料遺失的情況**：永久遺失服務分割區的大部分複本。
- **資料遺失的情況**：意外刪除或服務損毀。 例如，系統管理員錯誤地刪除服務。
- **資料損毀的情況**：服務中的 bug 會導致資料損毀。 例如，當服務程式碼升級而將錯誤資料寫入「可靠的集合」時，就可能發生資料損毀。 在這種情況下，可能必須將程式碼和資料還原成先前的狀態。

## <a name="prerequisites"></a>必要條件

- 若要觸發還原，必須對叢集啟用還原「錯誤分析服務 (FAS)」__。
- 「備份還原服務 (BRS)」__ 已建立備份。
- 還原只能在分割區觸發。
- 安裝 ServiceFabric 模組 [在預覽中] 以進行設定呼叫。

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- 請先使用命令來確定叢集已連線， `Connect-SFCluster` 再使用 ServiceFabric 模組進行任何設定要求。

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>已觸發的還原

還原可針對下列任何情況而觸發：

- 針對「災害復原」__ 進行的資料還原。
- 針對「資料損毀/資料遺失」__ 進行的資料還原。

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>災害復原案例中的資料還原

如果遺失整個 Service Fabric 叢集，您可以復原可靠具狀態服務和 Reliable Actors 分割區的資料。 您可以使用[具有備份儲存體詳細資料的 GetBackupAPI](/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation)，從清單中選取所需的備份。 備份列舉適用於應用程式、服務或分割區。

在下列範例中，我們會假設遺失的叢集是＜[啟用可靠具狀態服務和 Reliable Actors 的定期備份](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)＞中提到的相同叢集。 在此案例中，`SampleApp` 會在啟用備份原則的情況下進行部署，而備份會設定放在 Azure 儲存體。

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>使用 ServiceFabric 的 powershell 模組

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>使用 Powershell 的 Rest 呼叫

請執行 PowerShell 指令碼來使用 REST API，以傳回針對 `SampleApp` 應用程式內所有分割區建立的備份。 API 會要求備份儲存體資訊以列出可用的備份。

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

上述執行的範例輸出：

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

若要觸發還原，請選擇其中一個備份。 例如，目前用於災害復原的備份可能是下列備份：

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

對於還原 API，您需要提供 _BackupId_ 和 _BackupLocation_ 詳細資料。

您也需要選擇替代叢集中的目的地分割區，如[資料分割配置](service-fabric-concepts-partitioning.md#get-started-with-partitioning)中所述。 替代叢集備份會從原始的遺失叢集中，還原到資料分割配置中指定的分割區。

如果替代叢集上的分割區識別碼為 `1c42c47f-439e-4e09-98b9-88b8f60800c6`，您可以藉由比較「定界分割 (UniformInt64Partition)」__ 的高鍵值和低鍵值，將其對應至原始叢集分割區識別碼：`974bd92a-b395-4631-8a7f-53bd4ae9cf22`。

對於「具名分割」__，比較名稱值可找出替代叢集中的目標分割區。

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>使用 ServiceFabric 的 powershell 模組

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>使用 Powershell 的 Rest 呼叫

您可以使用下列[還原 API](/rest/api/servicefabric/sfclient-api-restorepartition)，要求對備份叢集分割區進行還原:

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

您可以透過 TrackRestoreProgress 追蹤還原的進度。

### <a name="using-service-fabric-explorer"></a>使用 Service Fabric Explorer
您可以從 Service Fabric Explorer 觸發還原。 請確定已在 Service Fabric Explorer 設定中啟用 [Advanced] 模式。
1. 選取所需的磁碟分割，然後按一下 [動作]。 
2. 選取 [觸發資料分割還原並填入 Azure 的資訊]：

    ![觸發資料分割還原][2]

    或檔案共用：

    ![觸發資料分割還原檔案共用][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>資料_損毀_ / _資料遺失_的資料還原

針對「資料遺失」__ 或「資料損毀」__ 的情況，您可以將可靠具狀態服務和 Reliable Actors 的已備份分割區還原至任何所選的備份。

下列範例接續＜[啟用可靠具狀態服務和 Reliable Actors 的定期備份](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)＞。 在此範例中，備份原則會針對分割區啟用，且服務會依據所需頻率在 Azure 儲存體中建立備份。

從 [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups) 的輸出中選取備份。 在此案例中，備份會從與之前相同的叢集中產生。

若要觸發還原，請從清單中選擇備份。 針對目前的_資料遺失_ / _資料損毀_，請選取下列備份：

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

對於還原 API，請提供 _BackupId_ 和 _BackupLocation_ 詳細資料。 因為叢集已啟用備份，所以 Service Fabric 的「備份還原服務 (BRS)」__ 會依據相關聯的備份原則找出正確的儲存體位置。


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>使用 ServiceFabric 的 powershell 模組

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>使用 Powershell 的 Rest 呼叫

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

您可以使用 TrackRestoreProgress 追蹤還原進度。

## <a name="track-restore-progress"></a>追蹤還原進度

可靠具狀態服務或 Reliable Actor 的分割區一次只接受一個還原要求。 分割只會在目前的還原要求完成後，再接受另一個要求。 您可以同時在不同的分割區上觸發多個還原要求。

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>使用 ServiceFabric 的 powershell 模組

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>使用 Powershell 的 Rest 呼叫

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

還原要求會依下列順序進行：

1. 已**接受**：已_接受_的還原狀態表示已使用正確的要求參數觸發所要求的分割區。
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **Inprogress**： _inprogress_還原狀態表示還原發生在包含要求中所述之備份的分割區中。 分割區會回報 dataloss__ 狀態。
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **成功**、**失敗**或**超時**：要求的還原可以下列任何狀態完成。 每個狀態都有下列重要性和回應詳細資料：
    - **成功**：成功還原狀態表示已重新_取得_的分割區狀態。 分割區會報告 RestoredEpoch__ 和 RestoredLSN__ 狀態以及 UTC 時間。

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **失敗**：_失敗_還原狀態表示還原要求失敗。 系統會報告失敗的原因。

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Timeout**：_超時_還原狀態表示要求有 Timeout。 請以提高的 [RestoreTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) 值建立新的還原要求。 預設的逾時為 10 分鐘。 請先確定分割區不是處於資料遺失狀態後，再重新要求還原。
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>自動還原

您可針對「自動還原」__ 設定 Service Fabric 叢集中可靠具狀態服務和 Reliable Actors 分割區。 在備份原則中，將 `AutoRestore` 設定為「true」__。 啟用「自動還原」__ 後，即可在回報資料遺失時，自動從最新分割區的備份還原資料。 如需詳細資訊，請參閱：

- [備份原則中的自動還原啟用](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [RestorePartition API 參考](/rest/api/servicefabric/sfclient-api-restorepartition)
- [GetPartitionRestoreProgress API 參考](/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>後續步驟
- [了解定期備份組態](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [備份還原 REST API 參考](/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png
