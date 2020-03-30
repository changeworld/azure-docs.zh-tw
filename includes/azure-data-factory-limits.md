---
title: 包含檔案
description: 包含檔案
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 01/08/2020
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 217e48ab21439f03e0b52c894c9aace5b51b1502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086200"
---
Azure 資料工廠是一個多租戶服務，具有以下預設限制，以確保客戶訂閱免受彼此的工作負荷之保護。 要將限制提高到訂閱的最大值，請聯繫支援人員。

### <a name="version-2"></a>第 2 版

| 資源 | 預設限制 | 上限 |
| -------- | ------------- | ------------- |
| Azure 訂用帳戶中的資料處理站 | 800 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 資料工廠內的實體總數，如管道、資料集、觸發器、連結服務和集成運行時 | 5,000 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 一個訂閱下 Azure-SSIS 集成運行時的 CPU 內核總數 | 256 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個資料工廠之間共用的併發管道運行 | 10,000  | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個[Azure 集成運行時區域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)每個訂閱的併發外部活動運行<br><small>外部活動在集成運行時進行管理，但在連結的服務上執行，包括資料磚塊、預存程序、HDInsights、Web 等。</small> | 3000 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個[Azure 集成運行時區域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)每個訂閱的併發管道活動運行 <br><small>管道活動在集成運行時執行，包括查找、獲取中繼資料和刪除。</small>| 1000 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個[Azure 集成運行時區域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)每個訂閱的併發創作操作<br><small>包括測試連接、流覽資料夾清單和表清單、預覽資料。 | 200 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個[Azure 集成運行時區域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)每個訂閱的併發資料整合單元<sup>1</sup>消耗| 區域組 1<sup>2</sup>：6000<br>區域組 2<sup>2</sup>：3000<br>區域組 3<sup>2</sup>： 1500 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個管道的最大活動，包括容器的內部活動 | 40 | 40 |
| 可針對單個自託管集成運行時創建的最大連結集成運行時數 | 100 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個管道的最大參數 | 50 | 50 |
| ForEach 項目 | 100,000 | 100,000 |
| ForEach 平行處理原則 | 20 | 50 |
| 每個管道的最大排隊運行 | 100 | 100 |
| 每個運算式的字元數 | 8,192 | 8,192 |
| 最小翻滾視窗觸發間隔 | 15 分鐘 | 15 分鐘 |
| 管道活動運行的最大超時 | 7 天 | 7 天 |
| 管道物件每個物件的位元組數<sup>3</sup> | 200 KB | 200 KB |
| 資料集和連結服務物件的每個物件的位元組數<sup>3</sup> | 100 KB | 2，000 KB |
| 每次複製活動運行的資料整合單元<sup>1</sup> | 256 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 寫入 API 呼叫 | 1，200/小時<br/><br/> 這是由 Azure Resource Manager 所加諸的限制，而不是 Azure Data Factory。 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 讀取 API 呼叫 | 12，500/小時<br/><br/> 這是由 Azure Resource Manager 所加諸的限制，而不是 Azure Data Factory。 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 監視每分鐘查詢次數 | 1,000 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每分鐘的實體 CRUD 作業 | 50 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 資料流程調試會話的最大時間 | 8 小時 | 8 小時 |
| 每個工廠的併發資料流程數 | 50 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個工廠每個使用者的資料流程調試會話的併發數量 | 3 | 3 |
| 資料流程 Azure IR TTL 限制 | 4 小時 | [聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |

<sup>1</sup>資料整合單元 （DIU） 用於雲到雲複製操作，從[資料整合單元（版本 2）](../articles/data-factory/copy-activity-performance.md#data-integration-units)中瞭解更多資訊。 有關計費的資訊，請參閱[Azure 資料工廠定價](https://azure.microsoft.com/pricing/details/data-factory/)。

<sup>2</sup> [Azure 集成運行時](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime)[可在全球範圍內使用](https://azure.microsoft.com/global-infrastructure/services/)，以確保資料合規性、效率和降低網路出口成本。 

| 區域組 | 區域 | 
| -------- | ------ |
| 區域組 1 | 美國中部、美國東部、美國東部、北歐、西歐、美國西部、美國西部 2 |
| 區域組 2 | 澳大利亞東部、澳大利亞東南部、巴西南部、印度中部、日本東部、美國中北部、美國中南部、東南亞、美國中西部 |
| 區域組 3 | 加拿大 中部、 東亞、 法國 中部、 韓國 中部、 英國南部 |

<sup>3</sup>管道、資料集和連結的服務物件表示工作負載的邏輯分組。 這些物件的限制與可以使用 Azure 資料工廠移動和處理的資料量無關。 資料工廠旨在擴展以處理 PB 級資料。

### <a name="version-1"></a>第 1 版

| **資源** | **預設限制** | **上限** |
| --- | --- | --- |
| 資料處理站內的管線 |2,500 |[聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 資料工廠內的資料集 |5,000 |[聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個資料集的併發切片 |10 |10 |
| 管道物件<sup>1</sup>的每個物件的位元組數 |200 KB |200 KB |
| 資料集和連結服務物件每個物件的位元組<sup>數 1</sup> |100 KB |2，000 KB |
| 訂閱<sup>2</sup>中的 Azure HDInsight 按需群集內核 |60 |[聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個複製活動運行的雲資料移動單位<sup>3</sup> |32 |[聯繫支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 管線活動執行的重試計數 |1,000 |MaxInt (32 位元) |

<sup>1</sup>管道、資料集和連結的服務物件表示工作負載的邏輯分組。 這些物件的限制與可以使用 Azure 資料工廠移動和處理的資料量無關。 資料工廠旨在擴展以處理 PB 級資料。

<sup>2</sup> 隨選 HDInsight 核心並非配置在包含資料處理站的訂用帳戶之中。 因此，上一個限制是按需 HDInsight 內核的資料工廠強制實施的核心限制。 它與與 Azure 訂閱關聯的核心限制不同。

<sup>3</sup>版本 1 的雲資料移動單元 （DMU） 用於雲到雲複製操作，從[雲資料移動單元（版本 1）](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units)中瞭解更多資訊。 有關計費的資訊，請參閱[Azure 資料工廠定價](https://azure.microsoft.com/pricing/details/data-factory/)。

| **資源** | **預設下限** | **下限** |
| --- | --- | --- |
| 排程間隔 |15 分鐘 |15 分鐘 |
| 重試嘗試間的間隔 |1 秒 |1 秒 |
| 重試逾時值 |1 秒 |1 秒 |

#### <a name="web-service-call-limits"></a>Web 服務呼叫限制
Azure Resource Manager 有 API 呼叫限制。 您可使用 [Azure 資源管理員 API 限制](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)內的速率進行 API 呼叫。
