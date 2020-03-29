---
title: Azure 服務結構 CLI-sfctl 事件
description: 描述服務結構 CLI sfctl 事件命令。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906101"
---
# <a name="sfctl-events"></a>sfctl 事件
從事件存儲檢索事件（如果已安裝事件存儲服務）。

事件商店系統服務可以通過配置升級添加到運行>+6.4 的任何 SFRP 群集。 請檢查以下 url\: HTTPs\:/docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore。

## <a name="commands"></a>命令

|Command|描述|
| --- | --- |
| 所有應用程式清單 | 獲取所有與應用程式相關的事件。 |
| 全節點清單 | 獲取所有與節點相關的事件。 |
| 全分區清單 | 獲取所有與分區相關的事件。 |
| 所有服務清單 | 獲取所有與服務相關的事件。 |
| 應用程式清單 | 獲取與應用程式相關的事件。 |
| 群集清單 | 獲取所有與群集相關的事件。 |
| 節點清單 | 獲取與節點相關的事件。 |
| 分區所有副本清單 | 獲取分區的所有與副本相關的事件。 |
| 分區清單 | 獲取與分區相關的事件。 |
| 分區副本清單 | 獲取與分區副本相關的事件。 |
| 服務清單 | 獲取與服務相關的事件。 |

## <a name="sfctl-events-all-applications-list"></a>sfctl 事件全應用程式清單
獲取所有與應用程式相關的事件。

回應是應用程式事件物件的清單。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --結束時間-utc [必需] | ISO UTC yyyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的結束時間。 |
| --開始時間-utc [必需] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的開始時間。 |
| --事件種類-篩選器 | 這是一個逗號分隔字串，指定應僅包含在回應中的 FabricEvents 類型。 |
| --排除分析事件 | 如果傳遞 true，則此參數將禁用分析事件的檢索。 |
| --跳過相關查找 | 如果傳遞 true，則此參數將禁用對相關事件資訊的搜索。 否則，將處理關聯事件，並填充每個 FabricEvent 中的"關聯事件"欄位。 |
| --timeout -t | 伺服器超時，用於在幾秒鐘內執行操作。 此超時指定用戶端願意等待請求的操作完成的時間持續時間。 此參數的預設值為 60 秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-all-nodes-list"></a>sfctl 事件全節點清單
獲取所有與節點相關的事件。

回應是 NodeEvent 物件的清單。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --結束時間-utc [必需] | ISO UTC yyyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的結束時間。 |
| --開始時間-utc [必需] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的開始時間。 |
| --事件種類-篩選器 | 這是一個逗號分隔字串，指定應僅包含在回應中的 FabricEvents 類型。 |
| --排除分析事件 | 如果傳遞 true，則此參數將禁用分析事件的檢索。 |
| --跳過相關查找 | 如果傳遞 true，則此參數將禁用對相關事件資訊的搜索。 否則，將處理關聯事件，並填充每個 FabricEvent 中的"關聯事件"欄位。 |
| --timeout -t | 伺服器超時，用於在幾秒鐘內執行操作。 此超時指定用戶端願意等待請求的操作完成的時間持續時間。 此參數的預設值為 60 秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-all-partitions-list"></a>sfctl 事件全分區清單
獲取所有與分區相關的事件。

回應是分區事件物件的清單。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --結束時間-utc [必需] | ISO UTC yyyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的結束時間。 |
| --開始時間-utc [必需] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的開始時間。 |
| --事件種類-篩選器 | 這是一個逗號分隔字串，指定應僅包含在回應中的 FabricEvents 類型。 |
| --排除分析事件 | 如果傳遞 true，則此參數將禁用分析事件的檢索。 |
| --跳過相關查找 | 如果傳遞 true，則此參數將禁用對相關事件資訊的搜索。 否則，將處理關聯事件，並填充每個 FabricEvent 中的"關聯事件"欄位。 |
| --timeout -t | 伺服器超時，用於在幾秒鐘內執行操作。 此超時指定用戶端願意等待請求的操作完成的時間持續時間。 此參數的預設值為 60 秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-all-services-list"></a>sfctl 事件所有服務清單
獲取所有與服務相關的事件。

回應是服務事件物件的清單。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --結束時間-utc [必需] | ISO UTC yyyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的結束時間。 |
| --開始時間-utc [必需] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的開始時間。 |
| --事件種類-篩選器 | 這是一個逗號分隔字串，指定應僅包含在回應中的 FabricEvents 類型。 |
| --排除分析事件 | 如果傳遞 true，則此參數將禁用分析事件的檢索。 |
| --跳過相關查找 | 如果傳遞 true，則此參數將禁用對相關事件資訊的搜索。 否則，將處理關聯事件，並填充每個 FabricEvent 中的"關聯事件"欄位。 |
| --timeout -t | 伺服器超時，用於在幾秒鐘內執行操作。 此超時指定用戶端願意等待請求的操作完成的時間持續時間。 此參數的預設值為 60 秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-application-list"></a>sfctl 事件應用程式清單
獲取與應用程式相關的事件。

回應是應用程式事件物件的清單。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --application-id [必要] | 應用程式的身分識別。 這通常是不含 'fabric\:' URI 配置的完整應用程式名稱。 從 6.0 版開始，階層的名稱會以 "\~" 字元分隔。 例如，如果應用程式名稱是 "fabric\:/myapp/app1"，則應用程式識別在 6.0+ 中會是 "myapp\~app1"，而在舊版中會是 "myapp/app1"。 |
| --結束時間-utc [必需] | ISO UTC yyyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的結束時間。 |
| --開始時間-utc [必需] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的開始時間。 |
| --事件種類-篩選器 | 這是一個逗號分隔字串，指定應僅包含在回應中的 FabricEvents 類型。 |
| --排除分析事件 | 如果傳遞 true，則此參數將禁用分析事件的檢索。 |
| --跳過相關查找 | 如果傳遞 true，則此參數將禁用對相關事件資訊的搜索。 否則，將處理關聯事件，並填充每個 FabricEvent 中的"關聯事件"欄位。 |
| --timeout -t | 伺服器超時，用於在幾秒鐘內執行操作。 此超時指定用戶端願意等待請求的操作完成的時間持續時間。 此參數的預設值為 60 秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-cluster-list"></a>sfctl 事件群集清單
獲取所有與群集相關的事件。

回應是群集事件物件的清單。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --結束時間-utc [必需] | ISO UTC yyyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的結束時間。 |
| --開始時間-utc [必需] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的開始時間。 |
| --事件種類-篩選器 | 這是一個逗號分隔字串，指定應僅包含在回應中的 FabricEvents 類型。 |
| --排除分析事件 | 如果傳遞 true，則此參數將禁用分析事件的檢索。 |
| --跳過相關查找 | 如果傳遞 true，則此參數將禁用對相關事件資訊的搜索。 否則，將處理關聯事件，並填充每個 FabricEvent 中的"關聯事件"欄位。 |
| --timeout -t | 伺服器超時，用於在幾秒鐘內執行操作。 此超時指定用戶端願意等待請求的操作完成的時間持續時間。 此參數的預設值為 60 秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-node-list"></a>sfctl 事件節點清單
獲取與節點相關的事件。

回應是 NodeEvent 物件的清單。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --結束時間-utc [必需] | ISO UTC yyyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的結束時間。 |
| --node-name      [必要] | 節點的名稱。 |
| --開始時間-utc [必需] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的開始時間。 |
| --事件種類-篩選器 | 這是一個逗號分隔字串，指定應僅包含在回應中的 FabricEvents 類型。 |
| --排除分析事件 | 如果傳遞 true，則此參數將禁用分析事件的檢索。 |
| --跳過相關查找 | 如果傳遞 true，則此參數將禁用對相關事件資訊的搜索。 否則，將處理關聯事件，並填充每個 FabricEvent 中的"關聯事件"欄位。 |
| --timeout -t | 伺服器超時，用於在幾秒鐘內執行操作。 此超時指定用戶端願意等待請求的操作完成的時間持續時間。 此參數的預設值為 60 秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl 事件分區所有副本清單
獲取分區的所有與副本相關的事件。

回應是副本事件物件的清單。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --結束時間-utc [必需] | ISO UTC yyyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的結束時間。 |
| --partition-id   [必要] | 分割區的識別。 |
| --開始時間-utc [必需] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的開始時間。 |
| --事件種類-篩選器 | 這是一個逗號分隔字串，指定應僅包含在回應中的 FabricEvents 類型。 |
| --排除分析事件 | 如果傳遞 true，則此參數將禁用分析事件的檢索。 |
| --跳過相關查找 | 如果傳遞 true，則此參數將禁用對相關事件資訊的搜索。 否則，將處理關聯事件，並填充每個 FabricEvent 中的"關聯事件"欄位。 |
| --timeout -t | 伺服器超時，用於在幾秒鐘內執行操作。 此超時指定用戶端願意等待請求的操作完成的時間持續時間。 此參數的預設值為 60 秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-partition-list"></a>sfctl 事件分區清單
獲取與分區相關的事件。

回應是分區事件物件的清單。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --結束時間-utc [必需] | ISO UTC yyyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的結束時間。 |
| --partition-id   [必要] | 分割區的識別。 |
| --開始時間-utc [必需] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的開始時間。 |
| --事件種類-篩選器 | 這是一個逗號分隔字串，指定應僅包含在回應中的 FabricEvents 類型。 |
| --排除分析事件 | 如果傳遞 true，則此參數將禁用分析事件的檢索。 |
| --跳過相關查找 | 如果傳遞 true，則此參數將禁用對相關事件資訊的搜索。 否則，將處理關聯事件，並填充每個 FabricEvent 中的"關聯事件"欄位。 |
| --timeout -t | 伺服器超時，用於在幾秒鐘內執行操作。 此超時指定用戶端願意等待請求的操作完成的時間持續時間。 此參數的預設值為 60 秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-partition-replica-list"></a>sfctl 事件分區副本清單
獲取與分區副本相關的事件。

回應是副本事件物件的清單。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --結束時間-utc [必需] | ISO UTC yyyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的結束時間。 |
| --partition-id   [必要] | 分割區的識別。 |
| --複製體 id [必需] | 複本的識別碼。 |
| --開始時間-utc [必需] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的開始時間。 |
| --事件種類-篩選器 | 這是一個逗號分隔字串，指定應僅包含在回應中的 FabricEvents 類型。 |
| --排除分析事件 | 如果傳遞 true，則此參數將禁用分析事件的檢索。 |
| --跳過相關查找 | 如果傳遞 true，則此參數將禁用對相關事件資訊的搜索。 否則，將處理關聯事件，並填充每個 FabricEvent 中的"關聯事件"欄位。 |
| --timeout -t | 伺服器超時，用於在幾秒鐘內執行操作。 此超時指定用戶端願意等待請求的操作完成的時間持續時間。 此參數的預設值為 60 秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-events-service-list"></a>sfctl 事件服務清單
獲取與服務相關的事件。

回應是服務事件物件的清單。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --結束時間-utc [必需] | ISO UTC yyyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的結束時間。 |
| --service-id     [必要] | 服務的身分識別。 此識別碼通常是不含 'fabric\:' URI 配置的服務完整名稱。 從 6.0 版開始，階層的名稱會以 "\~" 字元分隔。 例如，如果服務名稱是 "fabric\:/myapp/app1/svc1"，則服務識別在 6.0+ 中會是 "myapp\~app1\~svc1"，而在舊版中會是 "myapp/app1/svc1"。 |
| --開始時間-utc [必需] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 中查找查詢的開始時間。 |
| --事件種類-篩選器 | 這是一個逗號分隔字串，指定應僅包含在回應中的 FabricEvents 類型。 |
| --排除分析事件 | 如果傳遞 true，則此參數將禁用分析事件的檢索。 |
| --跳過相關查找 | 如果傳遞 true，則此參數將禁用對相關事件資訊的搜索。 否則，將處理關聯事件，並填充每個 FabricEvent 中的"關聯事件"欄位。 |
| --timeout -t | 伺服器超時，用於在幾秒鐘內執行操作。 此超時指定用戶端願意等待請求的操作完成的時間持續時間。 此參數的預設值為 60 秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

