---
title: 使用 Rest API 管理 Azure 檔案共用備份
description: 瞭解如何使用 REST API 來管理和監視 Azure 備份備份的 Azure 檔案共用。
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: c4d1ee187fd1c45dfd043b28c0d4b3d5935f50e1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073247"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>使用 REST API 管理 Azure 檔案共用備份

本文說明如何執行工作來管理和監視[Azure 備份](./backup-overview.md)所備份的 Azure 檔案共用。

## <a name="monitor-jobs"></a>監視工作

Azure 備份服務會觸發在背景執行的作業。 這包括觸發備份、還原作業，以及停用備份等案例。 這些工作可以使用其識別碼進行追蹤。

### <a name="fetch-job-information-from-operations"></a>從操作提取作業資訊

觸發備份之類的作業一律會在回應中傳回 jobID。

例如，[觸發程式備份 REST API](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share)作業的最後回應如下所示：

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Azure 檔案共用備份作業是由 [ **jobId** ] 欄位所識別，並可使用 GET 要求來追蹤，如[這裡](/rest/api/backup/jobdetails/)所述。

### <a name="tracking-the-job"></a>追蹤作業

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

{JobName} 是上面所述的「jobId」。 回應一律為 "200 OK"，其中**status**欄位指出作業的狀態。 一旦「完成」或「CompletedWithWarnings」， **extendedInfo**區段就會顯示更多關於此作業的詳細資料。

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>回應

名稱  | 類型  |  描述
--- | --- | ----
200 確定 |  JobResource  | 確定

#### <a name="response-example"></a>回應範例

一旦提交*GET* URI，就會傳回200回應。

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>修改原則

若要變更檔案共用所保護的原則，您可以使用與啟用保護相同的格式。 只需在要求原則中提供新的原則識別碼，並提交要求。

例如：若要將*testshare*的保護原則從*schedule1*變更為*schedule2*，請在要求主體中提供*schedule2*識別碼。

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>停止保護，但保留現有資料

您可以移除受保護檔案共用上的保護，但保留已備份的資料。 若要這麼做，請移除您用來[啟用備份](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share)並提交要求的要求本文中的原則。 一旦移除與原則的關聯之後，就不會再觸發備份，也不會建立任何新的復原點。

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>範例回應

停止保護檔案共用是非同步作業。 作業會建立另一個需要追蹤的作業。 它會傳回兩個回應：在建立另一個作業時傳回202（已接受），而當該作業完成時傳回200。

成功接受作業時的回應標頭：

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

然後使用 location 標頭或 AsyncOperation 標頭搭配 GET 命令來追蹤所產生的作業：

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>回應本文

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>停止保護並刪除資料

若要在受保護的檔案共用上移除保護並刪除備份資料，請執行[此處](/rest/api/backup/protecteditems/delete)詳述的刪除作業。

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

參數 {容器} 和 {protectedItemName}[的設定方式如下。](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname)

下列範例會觸發作業，以停止保護使用*azurefilesvault*保護的*testshare*檔案共用。

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>回應

刪除保護是非同步作業。 作業會建立另一個需要個別追蹤的作業。
它會傳回兩個回應：在建立另一個作業時，202（已接受），而當該作業完成時，則會傳回204（NoContent）。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[在設定 Azure 檔案共用的備份時，針對問題進行疑難排解](troubleshoot-azure-files.md)。
