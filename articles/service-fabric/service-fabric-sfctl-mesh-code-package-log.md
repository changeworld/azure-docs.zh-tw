---
title: Azure Service Fabric CLI-sfctl 網格程式碼-封裝-記錄檔
description: 深入瞭解 sfctl，這是 Azure Service Fabric 命令列介面。 包含用來取得指定程式碼封裝之記錄檔的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9ac1d85a1a498f9f6fcd0a03f8f819d1cdfcac33
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86257295"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
針對指定服務複本，取得指定程式碼套件的容器記錄。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| get | 取得容器的記錄。 |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
取得容器的記錄。

取得服務複本指定程式碼套件的容器記錄。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --app-name --application-name [必要] | 應用程式的名稱。 |
| --code-package-name           [必要] | 服務程式碼套件的名稱。 |
| --replica-name                [必要] | Service Fabric 複本名稱。 |
| --service-name                [必要] | 服務的名稱。 |
| --tail | 要從記錄結尾顯示的行數。 預設值為 100。 'all' 表示顯示完整記錄。 |

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
- 了解如何使用[範例指令碼](./scripts/sfctl-upgrade-application.md)來使用 Service Fabric CLI。
