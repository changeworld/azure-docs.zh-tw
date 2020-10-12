---
title: Azure Service Fabric CLI-sfctl 屬性
description: 瞭解 Azure Service Fabric 命令列介面的 sfctl。 包含用來儲存和查詢屬性的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 0a5ebd4822c5f0ff1735464bb4d5b42c436ee529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260323"
---
# <a name="sfctl-property"></a>sfctl property
在 Service Fabric 名稱底下儲存和查詢屬性。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
| [刪除] | 刪除指定的 Service Fabric 屬性。 |
| get | 取得指定的 Service Fabric 屬性。 |
| list | 取得指定名稱下所有 Service Fabric 屬性的相關資訊。 |
| put | 建立或更新 Service Fabric 屬性。 |

## <a name="sfctl-property-delete"></a>sfctl property delete
刪除指定的 Service Fabric 屬性。

刪除指定名稱下指定的 Service Fabric 屬性。 必須先建立屬性，才能將它刪除。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --name-id       [必要] | 不含 'fabric\:' URI 配置的 Service Fabric 名稱。 |
| --property-name [必要] | 指定要取得之屬性的名稱。 |
| --timeout -t | 用來執行作業的伺服器超時（以秒為單位）。 此超時時間指定用戶端願意等候要求的作業完成的持續時間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-property-get"></a>sfctl property get
取得指定的 Service Fabric 屬性。

取得指定名稱下指定的 Service Fabric 屬性。 這一律會同時傳回值和中繼資料。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --name-id       [必要] | 不含 'fabric\:' URI 配置的 Service Fabric 名稱。 |
| --property-name [必要] | 指定要取得之屬性的名稱。 |
| --timeout -t | 用來執行作業的伺服器超時（以秒為單位）。 此超時時間指定用戶端願意等候要求的作業完成的持續時間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-property-list"></a>sfctl property list
取得指定名稱下所有 Service Fabric 屬性的相關資訊。

Service Fabric 名稱可以包含一或多個儲存自訂資訊的具名屬性。 此作業會以分頁清單方式取得這些屬性的相關資訊。 此資訊包含每個屬性的相關名稱、值及中繼資料。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --name-id [必要] | 不含 'fabric\:' URI 配置的 Service Fabric 名稱。 |
| --continuation-token | 接續權杖參數可用來取得下一組結果。 當來自系統的結果無法放入單一回應中時，API 的回應中會包含具有非空白值的接續權杖。 當此值傳遞至下一個 API 呼叫時，API 會傳回下一組結果。 如果沒有任何進一步的結果，接續權杖就不會包含值。 此參數的值不能經過 URL 編碼。 |
| --include-values | 允許指定是否要包含所傳回屬性的值。 如果值應該與中繼資料一起傳回，即為 True；若只要傳回屬性中繼資料，則為 False。 |
| --timeout -t | 用來執行作業的伺服器超時（以秒為單位）。 此超時時間指定用戶端願意等候要求的作業完成的持續時間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-property-put"></a>sfctl property put
建立或更新 Service Fabric 屬性。

建立或更新指定名稱下指定的 Service Fabric 屬性。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --name-id       [必要] | 不含 'fabric\:' URI 配置的 Service Fabric 名稱。 |
| --property-name [必要] | Service Fabric 屬性的名稱。 |
| --value         [必要] | 描述 Service Fabric 屬性值。 這是一個 JSON 字串。 <br><br> Json 字串有兩個欄位（資料的「種類」）和值（輸入為數據的「資料」）。 'Kind' 值必須是 JSON 字串中第一個出現的項目，其值可以是 'Binary'、'Int64'、'Double'、'String' 或 'Guid'。 此值應該是可序列化成所指定類型的值。 'Kind' 和 'Data' 值都應該以字串形式提供。 |
| --custom-id-type | 屬性的自訂類型識別碼。 使用此屬性時，使用者將能夠標記屬性值的類型。 |
| --timeout -t | 預設值\: 60。 |

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
