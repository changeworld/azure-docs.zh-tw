---
title: Azure Service Fabric CLI-sfctl 撰寫
description: 深入瞭解 sfctl，這是 Azure Service Fabric 命令列介面。 包含 Docker Compose 應用程式的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1e40ca4e3c5ec8b7566646aa7ef723bd4c9e45a1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906125"
---
# <a name="sfctl-compose"></a>sfctl compose
建立、刪除和管理 Docker Compose 應用程式。

## <a name="commands"></a>命令

|Command|描述|
| --- | --- |
| 建立 | 建立 Service Fabric Compose 部署。 |
| list | 取得在 Service Fabric 叢集中建立的 Compose 部署清單。 |
| remove | 從叢集刪除現有的 Service Fabric Compose 部署。 |
| status | 取得 Service Fabric Compose 部署的相關資訊。 |
| 升級 | 開始升級 Service Fabric 叢集中的 Compose 部署。 |
| upgrade-rollback | 可開始復原 Service Fabric 叢集中的 Compose 部署升級。 |
| upgrade-status | 取得在此 Service Fabric Compose 部署上執行的最新升級詳細資料。 |

## <a name="sfctl-compose-create"></a>sfctl compose create
建立 Service Fabric Compose 部署。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --deployment-name [必要] | 部署的名稱。 |
| --file-path       [必要] | 目標 Docker Compose 檔案的路徑。 |
| --encrypted-pass | 使用已經加密的複雜密碼，而不提示輸入容器登錄密碼。 |
| --has-pass | 將提示輸入容器登錄的密碼。 |
| --timeout -t | 預設值\: 60。 |
| --user | 連線到容器登錄的使用者名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-compose-list"></a>sfctl compose list
取得在 Service Fabric 叢集中建立的 Compose 部署清單。

針對 Service Fabric 叢集中已建立或正在建立的 Compose 部署，取得部署的相關狀態。 回應會包含名稱、狀態，以及與 Compose 部署有關的其他詳細資料。 如果無法以一頁容納部署清單，則會傳回一頁結果及可用來取得下一頁的接續權杖。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --continuation-token | 接續權杖參數可用來取得下一組結果。 當來自系統的結果無法放入單一回應中時，API 的回應中會包含具有非空白值的接續權杖。 當此值傳遞至下一個 API 呼叫時，API 會傳回下一組結果。 如果沒有任何進一步的結果，接續權杖就不會包含值。 此參數的值不能經過 URL 編碼。 |
| --max-results | 分頁式查詢時傳回的最大結果數目。 此參數定義傳回結果數目的上限。 根據設定中所定義的訊息大小限制上限，如果所傳回的結果無法放入訊息中，則它們可以小於指定的結果上限。 如果此參數為零或未指定，則分頁式查詢會在傳回訊息中盡可能包含較多結果。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-compose-remove"></a>sfctl compose remove
從叢集刪除現有的 Service Fabric Compose 部署。

刪除現有的 Service Fabric Compose 部署。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --deployment-name [必要] | 部署的識別。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-compose-status"></a>sfctl compose status
取得 Service Fabric Compose 部署的相關資訊。

傳回 Service Fabric 叢集中已建立或正在建立且名稱符合參數所指定名稱的 Compose 部署狀態。 回應會包含名稱、狀態，以及與部署有關的其他詳細資料。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --deployment-name [必要] | 部署的識別。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-compose-upgrade"></a>sfctl compose upgrade
開始升級 Service Fabric 叢集中的 Compose 部署。

驗證所提供的升級參數，如果參數有效，便開始升級部署。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --deployment-name [必要] | 部署的名稱。 |
| --file-path [必要] | 目標 Docker Compose 檔案的路徑。 |
| --default-svc-type-health-map | 描述用來評估服務健康情況之健康原則的 JSON 編碼字典。 |
| --encrypted-pass | 使用已經加密的複雜密碼，而不提示輸入容器登錄密碼。 |
| --failure-action | 可能的值包括：'Invalid'、'Rollback'、'Manual'。 |
| --force-restart | 即使程式碼版本尚未變更，仍然會在升級期間強制重新啟動處理序。 <br><br> 升級只會變更組態或資料。 |
| --has-pass | 將提示輸入容器登錄的密碼。 |
| --health-check-retry | 當應用程式或叢集狀況不良時，嘗試執行健康情況檢查的間隔時間長度。 |
| --health-check-stable | 在升級繼續進入下一個升級網域之前，應用程式或叢集必須維持狀況良好的時間長度。 <br><br> 它會先解譯為代表 ISO 8601 持續時間的字串。 如果失敗，則會解譯為代表總毫秒數的數字。 |
| --health-check-wait | 完成升級網域之後，在開始健康情況檢查流程之前，要等待的時間長度。 |
| --replica-set-check | 發生非預期問題時，封鎖處理升級網域並防止遺失可用性的時間長度上限。 <br><br> 此逾時過期時，無論是否發生可用性遺失問題，升級網域的處理都將繼續。 逾時會在每個升級網域啟動時重設。 有效值介於 0 到 42949672925 (含) 之間。 |
| --svc-type-health-map | 針對用來評估各種不同服務類型健康情況的健康原則，描述這些原則的物件 JSON 編碼清單。 |
| --timeout -t | 預設值\: 60。 |
| --unhealthy-app | 在報告錯誤之前，允許健康情況不良應用程式的最大百分比。 <br><br> 例如，若要允許 10% 的應用程式健康情況不良，這個值會是 10。 百分比表示在系統將叢集視為錯誤之前，容許應用程式健康情況不良的最大百分比。 如果未到達此百分比，但至少有一個健康情況不良的應用程式，則健康情況會評估為 Warning。 其計算方式是將狀況不良的應用程式數目，除以叢集中應用程式執行個體的總數。 |
| --upgrade-domain-timeout | 執行 FailureAction 之前，每個升級網域必須完成的時間長度。 <br><br> 它會先解譯為代表 ISO 8601 持續時間的字串。 如果失敗，則會解譯為代表總毫秒數的數字。 |
| --upgrade-kind | 預設值\:Rolling。 |
| --upgrade-mode | 可能的值包括：'Invalid'、'UnmonitoredAuto'、'UnmonitoredManual'、'Monitored'。  預設值\:UnmonitoredAuto。 |
| --upgrade-timeout | 執行 FailureAction 之前，整體升級必須完成的時間長度。 <br><br> 它會先解譯為代表 ISO 8601 持續時間的字串。 如果失敗，則會解譯為代表總毫秒數的數字。 |
| --user | 連線到容器登錄的使用者名稱。 |
| --warning-as-error | 可指示是否將具有相同嚴重性的警告視為錯誤。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl compose upgrade-rollback
可開始復原 Service Fabric 叢集中的 Compose 部署升級。

復原 Service Fabric Compose 部署升級。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --deployment-name [必要] | 部署的識別。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-compose-upgrade-status"></a>sfctl compose upgrade-status
取得在此 Service Fabric Compose 部署上執行的最新升級詳細資料。

傳回 Compose 部署升級狀態的相關資訊，以及有助於進行應用程式健康情況問題偵錯的詳細資料。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --deployment-name [必要] | 部署的識別。 |
| --timeout -t | 執行作業的伺服器超時（以秒為單位）。 這個超時時間會指定用戶端願意等待要求的作業完成的持續期間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |


## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md)Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。