---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 2474b8920c5387c7896b413f229c2f5b06cdafb1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011122"
---
| 屬性 | 描述 |
|:--- |:---|
|**time** | 儲存體收到要求時的國際標準時間 (UTC) 時間。 例如： `2018/11/08 21:09:36.6900118` 。|
|**resourceId** | 儲存體帳戶的資源識別碼。 例如： `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**類別** | 所要求作業的分類。 例如：`StorageRead`、`StorageWrite` 或 `StorageDelete`。|
|**operationName** | 執行的 REST 作業類型。 <br> 如需作業的完整清單，請參閱[儲存體分析記錄作業和狀態訊息主題](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。 |
|**operationVersion** | 提出要求時所指定的儲存體服務版本。 這等同於 **x-ms-version** 標頭值。 例如： `2017-04-17` 。|
|**schemaVersion** | 記錄的結構描述版本。 例如： `1.0` 。|
|**statusCode** | 要求的 HTTP 狀態碼。 如果要求中斷，此值可能會被設為 `Unknown`。 <br> 例如： `206` |
|**statusText** | 所要求作業的狀態。  如需狀態訊息的完整清單，請參閱[儲存體分析記錄作業和狀態訊息主題](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。 在 2017-04-17 版和更新版本中，不會使用狀態訊息 `ClientOtherError`。 相反地，此欄位包含錯誤碼。 例如： `SASSuccess`  |
|**durationMs** | 執行要求作業的總時間 (以毫秒表示)。 包括讀取連入要求和傳送回應給要求者的時間。 例如： `12` 。|
|**callerIpAddress** | 要求者的 IP 位址，包含連接埠號碼。 例如： `192.100.0.102:4362` 。 |
|**correlationId** | 用來讓資源之間記錄相互關聯的識別碼。 例如： `b99ba45e-a01e-0042-4ea6-772bbb000000` 。 |
|**location** | 儲存體帳戶的位置。 例如： `North Europe` 。 |
|**protocol**|作業中使用的通訊協定。 例如：`HTTP`、`HTTPS`、`SMB` 或 `NFS`|
| **uri** | 要求的統一資源識別項。 |