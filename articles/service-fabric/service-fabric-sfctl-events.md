---
title: Azure Service Fabric CLI-sfctl 事件
description: 描述 Service Fabric CLI sfctl 事件命令。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906101"
---
# <a name="sfctl-events"></a>sfctl 事件
從事件存放區取出事件（如果已安裝 EventStore 服務）。

您可以透過設定升級，將 EventStore 系統服務新增至任何執行 > = 6.4 的 SFRP 叢集。 請檢查下列 url\: HTTPs\://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore。

## <a name="commands"></a>命令

|Command|說明|
| --- | --- |
| 所有-應用程式-清單 | 取得所有與應用程式相關的事件。 |
| 所有-節點-清單 | 取得所有節點相關事件。 |
| 所有-資料分割-清單 | 取得所有資料分割相關的事件。 |
| 所有-服務-清單 | 取得所有服務相關事件。 |
| 應用程式清單 | 取得應用程式相關的事件。 |
| 群集-清單 | 取得所有叢集相關事件。 |
| 節點清單 | 取得節點相關事件。 |
| 資料分割-所有複本-清單 | 取得資料分割的所有複本相關事件。 |
| 資料分割清單 | 取得分割區相關事件。 |
| 資料分割-複本清單 | 取得與資料分割複本相關的事件。 |
| 服務-清單 | 取得服務相關事件。 |

## <a name="sfctl-events-all-applications-list"></a>sfctl 事件-應用程式-清單
取得所有與應用程式相關的事件。

回應是 ApplicationEvent 物件的清單。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --結束時間-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的結束時間。 |
| --start-time-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的開始時間。 |
| --事件-類型-篩選 | 這是以逗號分隔的字串，用來指定應該只包含在回應中的 FabricEvents 類型。 |
| --排除-分析-事件 | 如果傳遞 true，這個參數會停用 AnalysisEvents 的抓取。 |
| --skip-相互關聯-查閱 | 如果傳遞 true，這個參數會停用 CorrelatedEvents 資訊的搜尋。 否則，就會填入每個 FabricEvent 中的 [CorrelationEvents 處理] 和 [HasCorrelatedEvents] 欄位。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-all-nodes-list"></a>sfctl 事件-所有節點-清單
取得所有節點相關事件。

回應是 NodeEvent 物件的清單。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --結束時間-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的結束時間。 |
| --start-time-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的開始時間。 |
| --事件-類型-篩選 | 這是以逗號分隔的字串，用來指定應該只包含在回應中的 FabricEvents 類型。 |
| --排除-分析-事件 | 如果傳遞 true，這個參數會停用 AnalysisEvents 的抓取。 |
| --skip-相互關聯-查閱 | 如果傳遞 true，這個參數會停用 CorrelatedEvents 資訊的搜尋。 否則，就會填入每個 FabricEvent 中的 [CorrelationEvents 處理] 和 [HasCorrelatedEvents] 欄位。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-all-partitions-list"></a>sfctl 事件-所有資料分割-清單
取得所有資料分割相關的事件。

回應是 PartitionEvent 物件的清單。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --結束時間-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的結束時間。 |
| --start-time-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的開始時間。 |
| --事件-類型-篩選 | 這是以逗號分隔的字串，用來指定應該只包含在回應中的 FabricEvents 類型。 |
| --排除-分析-事件 | 如果傳遞 true，這個參數會停用 AnalysisEvents 的抓取。 |
| --skip-相互關聯-查閱 | 如果傳遞 true，這個參數會停用 CorrelatedEvents 資訊的搜尋。 否則，就會填入每個 FabricEvent 中的 [CorrelationEvents 處理] 和 [HasCorrelatedEvents] 欄位。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-all-services-list"></a>sfctl 事件-服務-清單
取得所有服務相關事件。

回應是 ServiceEvent 物件的清單。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --結束時間-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的結束時間。 |
| --start-time-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的開始時間。 |
| --事件-類型-篩選 | 這是以逗號分隔的字串，用來指定應該只包含在回應中的 FabricEvents 類型。 |
| --排除-分析-事件 | 如果傳遞 true，這個參數會停用 AnalysisEvents 的抓取。 |
| --skip-相互關聯-查閱 | 如果傳遞 true，這個參數會停用 CorrelatedEvents 資訊的搜尋。 否則，就會填入每個 FabricEvent 中的 [CorrelationEvents 處理] 和 [HasCorrelatedEvents] 欄位。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-application-list"></a>sfctl 事件應用程式清單
取得應用程式相關的事件。

回應是 ApplicationEvent 物件的清單。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --application-id [必要] | 應用程式的身分識別。 這通常是不含 'fabric\:' URI 配置的完整應用程式名稱。 從 6.0 版開始，階層的名稱會以 "\~" 字元分隔。 例如，如果應用程式名稱是 "fabric\:/myapp/app1"，則應用程式識別在 6.0+ 中會是 "myapp\~app1"，而在舊版中會是 "myapp/app1"。 |
| --結束時間-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的結束時間。 |
| --start-time-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的開始時間。 |
| --事件-類型-篩選 | 這是以逗號分隔的字串，用來指定應該只包含在回應中的 FabricEvents 類型。 |
| --排除-分析-事件 | 如果傳遞 true，這個參數會停用 AnalysisEvents 的抓取。 |
| --skip-相互關聯-查閱 | 如果傳遞 true，這個參數會停用 CorrelatedEvents 資訊的搜尋。 否則，就會填入每個 FabricEvent 中的 [CorrelationEvents 處理] 和 [HasCorrelatedEvents] 欄位。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-cluster-list"></a>sfctl 事件叢集-清單
取得所有叢集相關事件。

回應是 ClusterEvent 物件的清單。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --結束時間-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的結束時間。 |
| --start-time-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的開始時間。 |
| --事件-類型-篩選 | 這是以逗號分隔的字串，用來指定應該只包含在回應中的 FabricEvents 類型。 |
| --排除-分析-事件 | 如果傳遞 true，這個參數會停用 AnalysisEvents 的抓取。 |
| --skip-相互關聯-查閱 | 如果傳遞 true，這個參數會停用 CorrelatedEvents 資訊的搜尋。 否則，就會填入每個 FabricEvent 中的 [CorrelationEvents 處理] 和 [HasCorrelatedEvents] 欄位。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-node-list"></a>sfctl 事件節點清單
取得節點相關事件。

回應是 NodeEvent 物件的清單。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --結束時間-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的結束時間。 |
| --node-name      [必要] | 節點的名稱。 |
| --start-time-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的開始時間。 |
| --事件-類型-篩選 | 這是以逗號分隔的字串，用來指定應該只包含在回應中的 FabricEvents 類型。 |
| --排除-分析-事件 | 如果傳遞 true，這個參數會停用 AnalysisEvents 的抓取。 |
| --skip-相互關聯-查閱 | 如果傳遞 true，這個參數會停用 CorrelatedEvents 資訊的搜尋。 否則，就會填入每個 FabricEvent 中的 [CorrelationEvents 處理] 和 [HasCorrelatedEvents] 欄位。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl 事件資料分割-所有-複本-清單
取得資料分割的所有複本相關事件。

回應是 ReplicaEvent 物件的清單。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --結束時間-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的結束時間。 |
| --partition-id   [必要] | 分割區的識別。 |
| --start-time-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的開始時間。 |
| --事件-類型-篩選 | 這是以逗號分隔的字串，用來指定應該只包含在回應中的 FabricEvents 類型。 |
| --排除-分析-事件 | 如果傳遞 true，這個參數會停用 AnalysisEvents 的抓取。 |
| --skip-相互關聯-查閱 | 如果傳遞 true，這個參數會停用 CorrelatedEvents 資訊的搜尋。 否則，就會填入每個 FabricEvent 中的 [CorrelationEvents 處理] 和 [HasCorrelatedEvents] 欄位。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-partition-list"></a>sfctl 事件資料分割-清單
取得分割區相關事件。

回應是 PartitionEvent 物件的清單。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --結束時間-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的結束時間。 |
| --partition-id   [必要] | 分割區的識別。 |
| --start-time-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的開始時間。 |
| --事件-類型-篩選 | 這是以逗號分隔的字串，用來指定應該只包含在回應中的 FabricEvents 類型。 |
| --排除-分析-事件 | 如果傳遞 true，這個參數會停用 AnalysisEvents 的抓取。 |
| --skip-相互關聯-查閱 | 如果傳遞 true，這個參數會停用 CorrelatedEvents 資訊的搜尋。 否則，就會填入每個 FabricEvent 中的 [CorrelationEvents 處理] 和 [HasCorrelatedEvents] 欄位。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-partition-replica-list"></a>sfctl 事件資料分割-複本-清單
取得與資料分割複本相關的事件。

回應是 ReplicaEvent 物件的清單。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --結束時間-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的結束時間。 |
| --partition-id   [必要] | 分割區的識別。 |
| --replica-id [必要] | 複本的識別碼。 |
| --start-time-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的開始時間。 |
| --事件-類型-篩選 | 這是以逗號分隔的字串，用來指定應該只包含在回應中的 FabricEvents 類型。 |
| --排除-分析-事件 | 如果傳遞 true，這個參數會停用 AnalysisEvents 的抓取。 |
| --skip-相互關聯-查閱 | 如果傳遞 true，這個參數會停用 CorrelatedEvents 資訊的搜尋。 否則，就會填入每個 FabricEvent 中的 [CorrelationEvents 處理] 和 [HasCorrelatedEvents] 欄位。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-service-list"></a>sfctl 事件服務-清單
取得服務相關事件。

回應是 ServiceEvent 物件的清單。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --結束時間-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的結束時間。 |
| --service-id     [必要] | 服務的身分識別。 此識別碼通常是不含 'fabric\:' URI 配置的服務完整名稱。 從 6.0 版開始，階層的名稱會以 "\~" 字元分隔。 例如，如果服務名稱是 "fabric\:/myapp/app1/svc1"，則服務識別在 6.0+ 中會是 "myapp\~app1\~svc1"，而在舊版中會是 "myapp/app1/svc1"。 |
| --start-time-utc [必要] | 以 ISO UTC yyyy-mm-dd ddTHH\:MM\:ssZ 的查閱查詢的開始時間。 |
| --事件-類型-篩選 | 這是以逗號分隔的字串，用來指定應該只包含在回應中的 FabricEvents 類型。 |
| --排除-分析-事件 | 如果傳遞 true，這個參數會停用 AnalysisEvents 的抓取。 |
| --skip-相互關聯-查閱 | 如果傳遞 true，這個參數會停用 CorrelatedEvents 資訊的搜尋。 否則，就會填入每個 FabricEvent 中的 [CorrelationEvents 處理] 和 [HasCorrelatedEvents] 欄位。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

