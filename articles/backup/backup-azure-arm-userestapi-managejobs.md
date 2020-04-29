---
title: 使用 REST API 管理備份作業
description: 在本文中，您將瞭解如何使用 REST API 來追蹤和管理 Azure 備份的備份和還原作業。
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: 628569c547aa776ec2fbb7ec7e32edad7c1fe7dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79273523"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>使用 REST API 追蹤備份和還原

Azure 備份服務會在各種情況下觸發在背景執行的作業，例如觸發備份、還原作業、停用備份。 這些工作可以使用其識別碼進行追蹤。

## <a name="fetch-job-information-from-operations"></a>從作業 (Operation) 擷取作業 (Job) 資訊

觸發備份之類的作業一律會傳回 jobID。 例如：[觸發程式備份 REST API](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3)作業的最後回應如下所示：

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

Azure VM 備份作業是由 "jobId" 欄位來識別，而且可如[這裡](https://docs.microsoft.com/rest/api/backup/jobdetails/)所述使用簡單的 *GET* 要求進行追蹤。

## <a name="tracking-the-job"></a>追蹤作業

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

`{jobName}` 是上面提及的 "jobId"。 回應一律為「200 確定」，其 [狀態] 欄位會指出作業的目前狀態。 一旦為 "Completed" 或 "CompletedWithWarnings"，'extendedInfo' 區段就會顯示更多有關作業的詳細資訊。

### <a name="response"></a>回應

|名稱  |類型  |說明  |
|---------|---------|---------|
|200 確定     | [JobResource](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | [確定]        |

#### <a name="example-response"></a>範例回應

一旦提交 GET** URI，就會傳回 200 (確定) 回應。

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```
