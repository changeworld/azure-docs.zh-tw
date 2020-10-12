---
title: Azure Service Fabric CLI-sfctl sa-叢集
description: 瞭解 Azure Service Fabric 命令列介面的 sfctl。 包含用來管理獨立叢集的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 7e9f4b55945afc04e5c826b26632d68cb75e502f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260277"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
管理獨立 Service Fabric 叢集。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
| config | 取得 Service Fabric 獨立叢集設定。 |
| config-upgrade | 開始升級 Service Fabric 獨立叢集的設定。 |
| upgrade-status | 取得 Service Fabric 獨立叢集的叢集設定升級狀態。 |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
取得 Service Fabric 獨立叢集設定。

叢集設定包含叢集的屬性，其中包括叢集上各種不同的節點類型、安全性設定、錯誤和升級網域拓撲等等。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --configuration-api-version [必要] | 獨立叢集 JSON 設定的 API 版本。 |
| --timeout -t | 用來執行作業的伺服器超時（以秒為單位）。 此超時時間指定用戶端願意等候要求的作業完成的持續時間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
開始升級 Service Fabric 獨立叢集的設定。

驗證所提供的設定升級參數，如果參數有效，便開始升級叢集設定。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --cluster-config            [必要] | 叢集設定。 |
| --application-health-policies | 引發錯誤之前，應用程式類型名稱和健康情況不良最大百分比組合的 JSON 編碼目錄。 |
| --delta-unhealthy-nodes | 在叢集升級期間，可允許的差異健康情況衰退百分比上限。 允許的值為 0 到 100 的整數值。 |
| --health-check-retry | 當應用程式或叢集狀況不良時，嘗試執行健康情況檢查的間隔時間長度。  預設值：PT0H0M0S。 |
| --health-check-stable | 在升級繼續進入下一個升級網域之前，應用程式或叢集必須維持狀況良好的時間長度。  預設值：PT0H0M0S。 <br><br> 它會先解譯為代表 ISO 8601 持續時間的字串。 如果失敗，則會解譯為代表總毫秒數的數字。 |
| --health-check-wait | 完成升級網域之後，在開始健康情況檢查流程之前，要等待的時間長度。  預設值：PT0H0M0S。 |
| --timeout -t | 預設值\: 60。 |
| --unhealthy-applications | 在升級期間，可允許的狀況不良應用程式百分比上限。 允許的值為 0 到 100 的整數值。 |
| --unhealthy-nodes | 在升級期間，可允許的狀況不良節點百分比上限。 允許的值為 0 到 100 的整數值。 |
| --upgrade-domain-delta-unhealthy-nodes | 在升級期間，可允許的升級網域差異健康情況衰退百分比上限。 允許的值為 0 到 100 的整數值。 |
| --upgrade-domain-timeout | 執行 FailureAction 之前，每個升級網域必須完成的時間長度。  預設值：PT0H0M0S。 <br><br> 它會先解譯為代表 ISO 8601 持續時間的字串。 如果失敗，則會解譯為代表總毫秒數的數字。 |
| --upgrade-timeout | 執行 FailureAction 之前，整體升級必須完成的時間長度。  預設值：PT0H0M0S。 <br><br> 它會先解譯為代表 ISO 8601 持續時間的字串。 如果失敗，則會解譯為代表總毫秒數的數字。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

### <a name="examples"></a>範例

啟動叢集組態更新
```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
取得 Service Fabric 獨立叢集的叢集設定升級狀態。

取得 Service Fabric 獨立叢集的叢集設定升級狀態詳細資料。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --timeout -t | 用來執行作業的伺服器超時（以秒為單位）。 此超時時間指定用戶端願意等候要求的作業完成的持續時間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |


## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](./scripts/sfctl-upgrade-application.md)來使用 Service Fabric CLI。
