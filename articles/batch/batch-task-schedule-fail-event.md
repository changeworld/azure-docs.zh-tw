---
title: Azure Batch 工作排程失敗事件
description: Batch 工作排程失敗事件的參考。 當工作無法排程時，就會發出此事件，稍後再重試。
ms.topic: reference
ms.date: 09/20/2020
ms.openlocfilehash: 549281d2b2c371e8f09c584e771cf44f7abc8a00
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91852110"
---
# <a name="task-schedule-fail-event"></a>工作排程失敗事件

 當工作無法排程且稍後將重試時，就會發出此事件。 這是因為資源限制而導致工作排程時間的暫時性失敗，例如，節點上沒有足夠的可用位置來執行具有指定的工作 `requiredSlots` 。

 下列範例顯示工作排程失敗事件的主體。

```
{
    "jobId": "job-01",
    "id": "task-01",
    "taskType": "User",
    "systemTaskVersion": 665378862,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-01",
        "nodeId": " "
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 0
    },
    "schedulingError": {
        "category": "UserError",
        "code": "JobPreparationTaskFailed",
        "message": "Task cannot run because the job preparation task failed on node"
    }
}
```

|元素名稱|類型|注意|
|------------------|----------|-----------|
|`jobId`|String|含有工作的作業識別碼。|
|`id`|String|工作識別碼。|
|`taskType`|String|工作類型。 可以是 'JobManager'，表示這是作業管理員工作，或是 'User'，表示不是作業管理員工作。 對於作業準備工作、作業發行工作或開始工作。不會發出此事件。|
|`systemTaskVersion`|Int32|這是作業上的內部重試計數器。 Batch 服務可在內部重試工作，以處理暫時性問題。 這些問題包含內部排程錯誤，或嘗試從不正常狀態的計算節點復原。|
|`requiredSlots`|Int32|執行工作所需的位置。|
|[`nodeInfo`](#nodeInfo)|複雜類型|包含工作執行所在計算節點的相關資訊。|
|[`multiInstanceSettings`](#multiInstanceSettings)|複雜類型|指定工作為需要多個計算節點的多重執行個體工作。  如需詳細資訊，請參閱 [`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task)。|
|[`constraints`](#constraints)|複雜類型|套用至此工作的執行限制。|
|[`schedulingError`](#schedulingError)|複雜類型|包含工作排程錯誤的相關資訊。|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|元素名稱|類型|注意|
|------------------|----------|-----------|
|`poolId`|String|工作執行所在集區的識別碼。|
|`nodeId`|String|工作執行所在節點的識別碼。|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|元素名稱|類型|注意|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|工作需要的計算節點數目。|

###  <a name="constraints"></a><a name="constraints"></a> constraints

|元素名稱|類型|注意|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|工作重試次數上限。 如果工作的結束代碼不是零，Batch 服務會重試工作。<br /><br /> 請注意，這個值會特別控制重試次數。 Batch 服務會嘗試工作一次，然後可一直重試直到達此限制。 例如，如果重試計數上限為 3，則 Batch 可嘗試工作最多 4 次 (一次首次嘗試，3 次重試)。<br /><br /> 如果重試計數上限為 0，則 Batch 服務不會重試工作。<br /><br /> 如果重試計數上限為 -1，則 Batch 服務會無限制地重試工作。<br /><br /> 預設值為 0 (不重試)。|


###  <a name="schedulingerror"></a><a name="schedulingError"></a> schedulingError

|元素名稱|類型|注意|
|------------------|----------|-----------|
|`category`|String|錯誤的類別。|
|`code`|String|工作排程錯誤的識別碼。 程式碼是不變的，旨在以程式設計的方式取用。|
|`message`|String|描述工作排程錯誤的訊息，其適用于在使用者介面中顯示。|
