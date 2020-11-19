---
title: 包含檔案
description: 包含檔案
services: data-factory
author: chez-charlie
ms.service: data-factory
ms.topic: include
ms.date: 11/16/2020
ms.author: chez
ms.custom: include file
ms.openlocfilehash: 1e8d13e7cf302c486bab291ef6482216122b62a3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94680749"
---
Azure Data Factory 是一種多租用戶服務，並具有以下的預設限制以確保客戶訂用帳戶不會受到彼此工作負載的影響。 若要提高訂用帳戶的上限，請連絡支援人員。

### <a name="version-2"></a>第 2 版

| 資源 | 預設限制 | 上限 |
| -------- | ------------- | ------------- |
| Azure 訂用帳戶中的資料處理站 | 800 | 800 |
| 資料處理站中的實體總數，例如管線、資料集、觸發程序、連結的服務、私人端點和整合執行階段 | 5,000 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 一個訂用帳戶下的 Azure-SSIS Integration Runtime 總 CPU 核心數 | 256 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個資料處理站的並行管線執行，其在處理站中的所有管線共用 | 10,000  | 10,000 |
| 每訂用帳戶每個 [Azure Integration Runtime 區域的並行外部活動執行](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>外部活動是在整合執行階段上管理，但在連結的服務上執行；包括 Databricks、預存程序、HDInsight、網頁和其他。此限制不適用於自我裝載 IR。</small> | 3,000 | 3,000 |
| 每訂用帳戶每個 [Azure Integration Runtime 區域的並行管線活動執行](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location) <br><small>管線活動會在整合執行階段上執行，包括 Lookup、GetMetadata 和 Delete。此限制不適用於自我裝載 IR。</small> | 1,000 | 1,000                                                        |
| 每訂用帳戶每個 [Azure Integration Runtime 區域的製作作業](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>包括測試連線、瀏覽資料夾清單和資料表清單、預覽資料。此限制不適用於自我裝載 IR。</small> | 200 | 200                                                          |
| 每訂用帳戶每個 [Azure Integration Runtime 區域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)的並行資料整合單位<sup>1</sup>耗用量| 區域群組 1<sup>2</sup>：6,000<br>區域群組 2<sup>2</sup>：3,000<br>區域群組 3<sup>2</sup>：1,500 | 區域群組 1<sup>2</sup>：6,000<br/>區域群組 2<sup>2</sup>：3,000<br/>區域群組 3<sup>2</sup>：1,500 |
| 每個管線的活動數目上限，包含容器的內部活動 | 40 | 40 |
| 可以針對單一自我裝載整合執行階段建立的連結整合執行階段最大數目 | 100 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個管線的參數上限 | 50 | 50 |
| ForEach 項目 | 100,000 | 100,000 |
| ForEach 平行處理原則 | 20 | 50 |
| 每個管線的已佇列執行上限 | 100 | 100 |
| 每個運算式的字元數 | 8,192 | 8,192 |
| 輪轉視窗觸發程序最小間隔 | 15 分鐘 | 15 分鐘 |
| 管線活動執行逾時上限 | 7 天 | 7 天 |
| 管線物件的每個物件位元組大小<sup>3</sup> | 200 KB | 200 KB |
| 資料集和已連結服務的每個物件位元組大小<sup>3</sup> | 100 KB | 2,000 KB |
| 每個活動執行的每個承載位元組數<sup>4</sup> | 896 KB | 896 KB |
| 每次複製活動執行的資料整合單位<sup>1</sup> | 256 | 256 |
| 寫入 API 呼叫 | 1,200/小時 | 1,200/小時<br/><br/> 這是由 Azure Resource Manager 所加諸的限制，而不是 Azure Data Factory。 |
| 讀取 API 呼叫 | 12,500/小時 | 12,500/小時<br/><br/> 這是由 Azure Resource Manager 所加諸的限制，而不是 Azure Data Factory。 |
| 監視每分鐘查詢次數 | 1,000 | 1,000 |
| 資料流程偵錯工作階段上限 | 8 小時 | 8 小時 |
| 每個整合執行階段的並行資料流程數 | 50 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每處理站的每個使用者的資料流程偵錯工作階段並行數 | 3 | 3 |
| 資料流程 Azure IR TTL 限制 | 4 小時 |  4 小時 |

<sup>1</sup>資料整合單位 (DIU) 用於雲端到雲端複製作業，請參閱[資料整合單位 (第2版)](../articles/data-factory/copy-activity-performance.md#data-integration-units) 以深入了解。 如需計費的詳細資訊，請參閱 [Azure Data Factory 定價](https://azure.microsoft.com/pricing/details/data-factory/)。

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) 為[全域可用](https://azure.microsoft.com/global-infrastructure/services/)，以確保資料合規性、效率，並降低網路輸出成本。 

| 區域群組 | 區域 |
| -------- | ------ |
| 區域群組 1 | 美國中部、美國東部、美國東部 2、歐洲北部、歐洲西部、美國西部、美國西部 2 |
| 區域群組 2 | 澳大利亞東部、澳大利亞東南部、巴西南部、印度中部、日本東部、美國中北部、美國中南部、東南亞、美國中西部 |
| 區域群組 3 | 加拿大中部、亞太地區、法國中部、南韓中部、英國南部 |

<sup>3</sup>管線、資料集和連結的服務物件代表您工作負載的邏輯群組。 這些物件的限制與您可使用 Azure Data Factory 移動和處理的資料量無關。 Data Factory 可視需要調整以處理數 PB 的資料。

<sup>4</sup> 每個活動執行的承載包含活動設定、相關聯的資料集和已連結的服務設定 (如果有的話)，以及每個活動類型所產生的一小部分系統屬性。 此承載大小的限制與您可使用 Azure Data Factory 移動和處理的資料量無關。 如果您達到此限制，請參閱[徵兆和建議](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large)。

### <a name="version-1"></a>第 1 版

| **Resource** | **預設限制** | **上限** |
| --- | --- | --- |
| 資料處理站內的管線 |2,500 |[連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 資料處理站內的資料集 |5,000 |[連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個資料集的並行配量 |10 |10 |
| 管線物件的每個物件位元組大小<sup>1</sup> |200 KB |200 KB |
| 資料集和已連結服務的每個物件位元組大小<sup>1</sup> |100 KB |2,000 KB |
| 訂用帳戶中的 Azure HDInsight 隨選叢集核心<sup>2</sup> |60 |[連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每次複製活動執行的雲端資料移動單位<sup>3</sup> |32 |32 |
| 管線活動執行的重試計數 |1,000 |MaxInt (32 位元) |

<sup>1</sup>管線、資料集和連結的服務物件代表您工作負載的邏輯群組。 這些物件的限制與您可使用 Azure Data Factory 移動和處理的資料量無關。 Data Factory 可視需要調整以處理數 PB 的資料。

<sup>2</sup> 隨選 HDInsight 核心並非配置在包含資料處理站的訂用帳戶之中。 因此，先前的限制是隨選 HDInsight 核心的 Data Factory 強制核心限制， 與您 Azure 訂用帳戶相關聯的核心限制不同。

<sup>3</sup>第 1 版的雲端資料移動單位 (DMU) 用於雲端到雲端複製作業，請參閱[雲端資料移動單位 (第 1 版)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) 深入了解。 如需計費的詳細資訊，請參閱 [Azure Data Factory 定價](https://azure.microsoft.com/pricing/details/data-factory/)。

| **Resource** | **預設下限** | **下限** |
| --- | --- | --- |
| 排程間隔 |15 分鐘 |15 分鐘 |
| 重試嘗試間的間隔 |1 秒 |1 秒 |
| 重試逾時值 |1 秒 |1 秒 |

#### <a name="web-service-call-limits"></a>Web 服務呼叫限制
Azure Resource Manager 有 API 呼叫限制。 您可使用 [Azure 資源管理員 API 限制](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)內的速率進行 API 呼叫。
