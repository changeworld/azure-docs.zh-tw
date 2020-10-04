---
title: Azure 資料表儲存體監視資料參考資料 |Microsoft Docs
description: 用於監視 Azure 資料表儲存體中資料的記錄和計量參考。
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: e86be87faf6bb0c58674a15882e52dcef43fe42e
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711214"
---
# <a name="azure-table-storage-monitoring-data-reference"></a>Azure 資料表儲存體監視資料參考

如需收集和分析 Azure 儲存體監視資料的詳細資訊，請參閱[監視 Azure 儲存體](monitor-table-storage.md)。

## <a name="metrics"></a>計量

下表列出針對 Azure 儲存體收集的平台計量。 

### <a name="capacity-metrics"></a>容量度量

容量計量值會每隔一小時就傳送給 Azure 監視器。 這些值會每天重新整理。 時間粒紋會定義用來呈現計量值的時間間隔。 所有容量計量支援的時間粒紋為一小時 (PT1H)。

Azure 儲存體會提供下列 Azure 監視器容量計量。

#### <a name="account-level"></a>帳戶層級

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="table-storage"></a>表格儲存體

下表顯示 [資料表儲存體計量](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountstableservices)。

| 計量 | 描述 |
| ------------------- | ----------------- |
| TableCapacity | 儲存體帳戶所使用的表格儲存體數量。 <br/><br/> 單位：位元組 <br/> 彙總類型：Average <br/> 值範例：1024 |
| TableCount   | 儲存體帳戶中的表格數目。 <br/><br/> 單位：Count <br/> 彙總類型：Average <br/> 值範例：1024 |
| TableEntityCount | 儲存體帳戶中的表格實體數目。 <br/><br/> 單位：Count <br/> 彙總類型：Average <br/> 值範例：1024 |

### <a name="transaction-metrics"></a>交易度量

對儲存體帳戶的每個要求，都會發出從 Azure 儲存體到 Azure 監視器的交易計量。 在您的儲存體帳戶上沒有活動的情況下，該期間內交易計量上不會有資料。 所有交易計量都可在帳戶和資料表儲存體服務層級使用。 時間粒紋會定義用來呈現計量值的時間間隔。 所有交易計量支援的時間粒紋為 PT1H 和 PT1M。

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>計量維度

Azure 儲存體支援下列 Azure 監視器計量維度。

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>資源記錄 (預覽)

> [!NOTE]
> Azure 監視器中的 Azure 儲存體記錄處於公開預覽狀態，可在所有公用雲端區域中進行預覽測試。 若要註冊預覽，請參閱[本頁](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。  此預覽可啟用 Blob (包括 Azure Data Lake Storage Gen2)、檔案、佇列、資料表、一般用途 v1 進階儲存體帳戶及一般用途 v2 儲存體帳戶的記錄。 不支援傳統儲存體帳戶。

下表列出在 Azure 監視器記錄或 Azure 儲存體中收集時，Azure 儲存體資源記錄的屬性。 屬性會描述作業、服務，以及用來執行作業的授權類型。

### <a name="fields-that-describe-the-operation"></a>描述作業的欄位

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>描述如何驗證作業的欄位

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>描述服務的欄位

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>另請參閱

- 請參閱 [監視 Azure 資料表儲存體](monitor-table-storage.md) ，以取得監視 Azure 儲存體的說明。
- 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器來監視 Azure 資源](../../azure-monitor/insights/monitor-azure-resource.md)。