---
title: Azure Functions 中的事件驅動規模調整
description: 說明耗用量方案和高階方案函式應用程式的調整行為。
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: 8aca1ab6629f95ef9162e1247434bd3189d5a7d2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937591"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Azure Functions 中的事件驅動規模調整

在耗用量和高階方案中，Azure Functions 藉由新增函數主機的其他實例來調整 CPU 和記憶體資源。 實例數目取決於觸發函數的事件數目。 

取用方案中的每個函式主機實例限制為 1.5 GB 的記憶體和一個 CPU。  主機的實例是整個函數應用程式，這表示函式應用程式內的所有函式都會共用實例內的資源，並同時調整規模。 共用相同耗用量方案規模的函式應用程式會獨立調整。  在高階方案中，方案大小會決定該實例上該方案中所有應用程式的可用記憶體和 CPU。  

函式程式碼檔案會儲存在函式主要儲存體帳戶 Azure 檔案儲存體共用上。 當您刪除函式應用程式的主要儲存體帳戶時，函式程式碼檔案會被刪除，且無法復原。

## <a name="runtime-scaling"></a>執行階段調整

Azure Functions 使用名為「縮放控制器」的元件來監視事件的速率，並判斷是否相應放大或相應縮小。 縮放控制器會在每種觸發程序類型使用啟發學習法。 例如，當使用 Azure 佇列儲存體觸發程序時，會根據佇列長度和最舊佇列訊息的壽命調整規模。

Azure Functions 的調整單位是函數應用程式。 當函式應用程式相應放大時，會配置額外資源來執行 Azure Functions 主機的多個執行個體。 反之，當計算需求降低時，縮放控制器會移除 Functions 主機的執行個體。 當函式應用程式內未執行任何函式時，實例數目最終會「相應放大」為零。

![縮放控制器能監視事件及建立執行個體](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>冷啟動

當您的函式應用程式閒置數分鐘後，該平臺可能會將您的應用程式執行的實例數目調整為零。 下一個要求的新增延遲是從零調整為一。 這項延遲稱為 _冷啟動_。 函數應用程式所需的相依性數目可能會影響冷開始時間。 冷啟動是同步作業的問題，例如必須傳迴響應的 HTTP 觸發程式。 如果冷啟動影響您的函式，請考慮在高階方案中執行，或在啟用 **Always on** 設定的專用方案中執行。   

## <a name="understanding-scaling-behaviors"></a>了解規模調整行為

縮放比例會因為許多因素而有所不同，也會因為選取的觸發程序和語言不同，而進行不同的規模調整。 有一些複雜的調整行為需要注意：

* **最大實例數目：** 單一函式應用程式只會相應放大至最多200個實例。 單一執行個體可能會一次處理一個以上的訊息或要求，因此沒有設定平行執行的數目上限。  您可以 [指定較低的最大值](#limit-scale-out) ，以視需要調整規模。
* **新的實例速率：** 針對 HTTP 觸發程式，每秒最多會配置一個新的實例。 若為非 HTTP 觸發程式，則會每隔30秒配置一次新的實例。 在高階 [方案](functions-premium-plan.md)中執行時，縮放比例會更快。
* **調整效率：** 針對服務匯流排觸發程式，請使用資源的 _管理_ 許可權，以獲得最有效率的調整。 使用 _接聽_ 許可權時，縮放比例不會正確，因為佇列長度無法用來通知調整決策。 若要深入瞭解如何設定服務匯流排存取原則中的許可權，請參閱 [共用存取授權原則](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies)。 針對事件中樞觸發程式，請參閱參考文章中的 [調整指導](functions-bindings-event-hubs-trigger.md#scaling) 方針。 

## <a name="limit-scale-out"></a>限制 scale out

您可能會想要限制應用程式用來相應放大的實例數目上限。 這最常見的情況是，下游元件（例如資料庫）的輸送量有限。  根據預設，取用方案函式會向外延展至多達200實例，而高階方案函式會相應放大至多達100個實例。  您可以藉由修改值，為特定應用程式指定較低的最大 `functionAppScaleLimit` 值。  `functionAppScaleLimit`可以設定為或（ `0` 不 `null` 受限制）或介於 `1` 和應用程式最大值之間的有效值。

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>可調整應用程式的最佳做法與模式

函數應用程式有許多方面會影響其調整方式，包括主機設定、執行時間使用量和資源效率。  如需詳細資訊，請參閱[效能考量文章中的延展性一節](functions-best-practices.md#scalability-best-practices)。 您也應了解在縮放函式應用程式後，連線會如何運作。 如需詳細資訊，請參閱[如何管理 Azure Functions 中的連線](manage-connections.md)。

如需在 Python 和 Node.js 中進行調整的詳細資訊，請參閱 [Azure Functions python 開發人員指南-調整規模和並行](functions-reference-python.md#scaling-and-performance) 存取和 [Azure Functions Node.js 開發人員指南-調整規模和並行](functions-reference-node.md#scaling-and-concurrency)存取。

## <a name="billing-model"></a>計費模式

不同方案的計費方式會在 [Azure Functions 定價頁面](https://azure.microsoft.com/pricing/details/functions/)上詳細說明。 使用量是在函式應用程式層級彙總，且只會計算函式程式碼執行的時間。 計費單位如下︰

* **以十億位元組-秒 (GB-s) 為單位的資源取用量**。 會計算為在函數應用程式中執行之所有函式的記憶體大小和執行時間組合。 
* 執行。 每次函式為回應事件觸發程序而執行，就算一次。

有關如何瞭解您的耗用量帳單的實用查詢和資訊，可 [在帳單常見問題](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)中找到。

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>後續步驟

+ [Azure Functions 裝載選項](functions-scale.md)

