---
title: 使用 Azure 串流分析錯誤碼進行疑難排解
description: 使用內部錯誤碼針對 Azure 串流分析問題進行疑難排解。
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 2e101747322baac860cbde02aca341171aa0cc1d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658358"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Azure 串流分析內部錯誤碼

您可使用活動記錄和資源記錄，協助對 Azure 串流分析作業中的非預期行為進行偵錯。 本文列出每個內部錯誤碼的描述。 內部錯誤屬於一般性錯誤，當串流分析無法分辨錯誤屬於內部可用性錯誤還是系統中的錯誤時，便會在串流分析平台中擲回這種錯誤。

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **原因**︰用來寫入到 Cosmos DB 的批次大小過大。 
* **建議**：請使用較小的批次大小重試。

## <a name="next-steps"></a>後續步驟

* [對輸入連線進行疑難排解](stream-analytics-troubleshoot-input.md)
* [對 Azure 串流分析輸出進行疑難排解](stream-analytics-troubleshoot-output.md)
* [對 Azure 串流分析查詢進行疑難排解](stream-analytics-troubleshoot-query.md)
* [使用資料記錄對 Azure 串流分析進行疑難排解](stream-analytics-job-diagnostic-logs.md)
* [Azure 串流分析資料錯誤](data-errors.md)