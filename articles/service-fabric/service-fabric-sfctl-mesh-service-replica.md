---
title: Azure Service Fabric CLI-sfctl 網格服務-複本
description: 深入瞭解 sfctl，這是 Azure Service Fabric 命令列介面。 包含用來取得應用程式資源複本詳細資料的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ac604ec0a854964746ef34627b054e853662265f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905946"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
取得複本服務詳細資料和應用程式資源中指定服務的清單複本。

## <a name="commands"></a>命令

|Command|說明|
| --- | --- |
| list | 列出服務的所有複本。 |
| 顯示 | 取得應用程式服務的指定複本。 |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh service-replica list
列出服務的所有複本。

取服務所有複本的相關資訊。 資訊包括服務複本的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --app-name --application-name [必要] | 應用程式的名稱。 |
| --service-name                [必要] | 服務的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replica show
取得應用程式服務的指定複本。

取得指定名稱服務複本的相關資訊。 資訊包括服務複本的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --app-name --application-name [必要] | 應用程式的名稱。 |
| --name -n                     [必要] | 服務複本的名稱。 |
| --service-name                [必要] | 服務的名稱。 |

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