---
title: Azure Service Fabric CLI-sfctl 網格服務
description: 深入瞭解 sfctl，這是 Azure Service Fabric 命令列介面。 包含用來取得應用程式資源服務詳細資料的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b9f16062b02d7a1a3403d396643a7989b8976b7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905932"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
取得服務詳細資料和應用程式資源的清單服務。

## <a name="commands"></a>命令

|Command|說明|
| --- | --- |
| list | 可列出所有服務資源。 |
| 顯示 | 可取得指定名稱的服務資源。 |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
可列出所有服務資源。

可取得應用程式資源所有服務的相關資訊。 資訊包括服務的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --app-name --application-name [必要] | 應用程式的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
可取得指定名稱的服務資源。

可取得指定名稱服務資源的相關資訊。 資訊包括服務的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --app-name --application-name [必要] | 應用程式的名稱。 |
| --name -n                     [必要] | 服務的名稱。 |

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