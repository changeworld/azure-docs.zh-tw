---
title: Durable Functions 計費 - Azure Functions
description: 了解 Durable Functions 的內部行為，以及它們對於 Azure Functions 計費有何影響。
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 504ef93a0002895bc5662d95ad269c8593170ee2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "74233002"
---
# <a name="durable-functions-billing"></a>Durable Functions 計費

[Durable Functions](durable-functions-overview.md) 的計費方式與 Azure Functions 相同。 如需詳細資訊，請參閱 [Azure Functions 價格](https://azure.microsoft.com/pricing/details/functions/)。

在 Azure Functions [取用方案](../functions-scale.md#consumption-plan)中執行協調器函式時，您必須注意一些計費行為。 下列各節會更詳細地說明這些行為及其影響。

## <a name="orchestrator-function-replay-billing"></a>協調器函式重新執行計費

[協調器函式](durable-functions-orchestrations.md)可能會在協調流程存留期間重新執行數次。 Azure Functions 執行階段會將每次重新執行視為不同的函式引動過程。 基於這個理由，在 Azure Functions 取用方案中，將會針對協調器函式的每次重新執行計費。 其他方案類型不會針對協調器函式重新執行收費。

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>在協調器函式中等候和暫止

當協調器函式使用 **await** (C#) 或 **yield** (JavaScript) 等候非同步動作完成時，執行階段會將特定執行作業視為完成。 協調器函式的計費會在該時間點停止。 在下一次協調器函式重新執行之前，它不會繼續。 您不需支付在協調器函式中任何花在等候或暫止的時間。

> [!NOTE]
> 呼叫其他函式的函式會被視為反模式。 這是因為所謂的「雙重計費」  問題。 當函式直接呼叫另一個函式時，兩者會同時執行。 呼叫端函式在等候回應時，被呼叫端函式會主動執行程式碼。 在此情況下，您必須針對呼叫端函式花在等候被呼叫端函式執行的時間付費。
>
> 協調器函式中不會雙重計費。 協調器函式會在等候活動函式或子協調流程的結果時停止計費。

## <a name="durable-http-polling"></a>長期 HTTP 輪詢

協調器函式可以對外部端點進行長時間執行的 HTTP 呼叫，如 [HTTP 功能](durable-functions-http-features.md)一文所述。 **CallHttpAsync** 方法 (C#) 和 **callHttp** 方法 (JavaScript) 可能會在依循[非同步 202 模式](durable-functions-http-features.md#http-202-handling)時於內部輪詢 HTTP 端點。

內部 HTTP 輪詢作業目前不會直接計費。 不過，內部輪詢可能會導致協調器函式定期重新執行。 您的這些內部函式重新執行將以標準費用計費。

## <a name="azure-storage-transactions"></a>Azure 儲存體交易

Durable Functions 依預設會使用 Azure 儲存體來保持狀態的一致性、處理訊息，以及透過 Blob 租用來管理分割區。 此儲存體帳戶是由您擁有，因此任何交易成本都會向您的 Azure 訂用帳戶收費。 如需 Durable Functions 所使 Azure 儲存體成品的詳細資訊，請參閱[工作中樞](durable-functions-task-hubs.md)一文。

有數個因素會構成 Durable Functions 應用程式所產生的實際 Azure 儲存體成本：

* 單一函式應用程式會與單一工作中樞相關聯，它會共用一組 Azure 儲存體資源。 函式應用程式中的所有 Durable Functions 都會使用這些資源。 函式應用程式中的實際函式數目不會影響 Azure 儲存體交易成本。
* 每個函式應用程式執行個體都會使用指數輪詢演算法，在內部輪詢儲存體帳戶中的多個佇列。 閒置應用程式執行個體輪詢佇列的頻率會低於作用中的應用程式，因此所產生的交易成本較少。 如需有關 Durable Functions 佇列輪詢行為的詳細資訊，請參閱[效能與級別的佇列輪詢區段](durable-functions-perf-and-scale.md#queue-polling)一文。
* 在 Azure Functions 取用或進階方案中執行時，[Azure Functions 級別控制器](../functions-scale.md#how-the-consumption-and-premium-plans-work)會在背景定期輪詢所有工作中樞佇列。 如果函式應用程式在輕量到中度級別之下，只有單一級別控制器執行個體會輪詢這些佇列。 如果函式應用程式相應放大為大量執行個體，則可能會新增更多級別控制器執行個體。 這些額外的級別控制器執行個體可能會增加佇列交易總成本。
* 每個函式應用程式執行個體都會競爭取得一組 Blob 租用。 這些執行個體會定期對 Azure Blob 服務進行呼叫，以更新保留的租用，或嘗試取得新的租用。 工作中樞設定的分割區計數將決定 Blob 租用的數目。 相應放大為較大量的函式應用程式執行個體，可能會增加與這些租用作業相關聯的 Azure 儲存體交易成本。

您可在 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)文件中找到 Azure 儲存體定價的詳細資訊。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入瞭解 Azure Functions 定價](https://azure.microsoft.com/pricing/details/functions/)
