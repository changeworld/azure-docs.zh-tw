---
title: Azure Service Fabric CLI-sfctl 網狀 secretvalue
description: 深入瞭解 sfctl，這是 Azure Service Fabric 命令列介面。 包含用來取得和刪除 Service Fabric 網格 secretvalue 資源的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b2fe3a7995cdd88a8f63d0d865e72e9f161cf9d7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905956"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
取得和刪除網狀結構 mesh secretvalue 資源。

## <a name="commands"></a>命令

|Command|說明|
| --- | --- |
| delete | 刪除選定祕密資源的指定值。 |
| list | 列出指定祕密資源所有值的名稱。 |
| 顯示 | 列出秘密資源的指定值。 |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete
刪除選定祕密資源的指定值。

刪除以名稱識別的祕密值資源。 資源的名稱一般是與該值相關聯的版本。 如果指定值正在使用中，則會刪除失敗。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --secret-name -n [必要] | 祕密資源的名稱。 |
| --version -v     [必要] | 祕密版本的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue list
列出指定祕密資源所有值的名稱。

取得指定祕密資源所有祕密值資源的相關資訊。 資訊包含祕密值資源的名稱，但並不包含實際值。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --secret-name -n [必要] | 祕密資源的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
列出秘密資源的指定值。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --secret-name -n [必要] | 祕密資源的名稱。 |
| --version -v     [必要] | 祕密版本的名稱。 |
| --show-value | 顯示祕密版本的實際值。 |

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
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。