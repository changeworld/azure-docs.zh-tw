---
title: Azure 服務結構 CLI-sfctl 網格代碼-包日誌
description: 瞭解 sfctl，Azure 服務結構命令列介面。 包括用於獲取指定代碼包的日誌的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906040"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
針對指定服務複本，取得指定程式碼套件的容器記錄。

## <a name="commands"></a>命令

|Command|描述|
| --- | --- |
| get | 取得容器的記錄。 |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
取得容器的記錄。

取得服務複本指定程式碼套件的容器記錄。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --app-name --application-name [必要] | 應用程式的名稱。 |
| --code-package-name           [必要] | 服務程式碼套件的名稱。 |
| --replica-name                [必要] | Service Fabric 複本名稱。 |
| --service-name                [必要] | 服務的名稱。 |
| --tail | 要從記錄結尾顯示的行數。 預設值為 100。 'all' 表示顯示完整記錄。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |


## <a name="next-steps"></a>後續步驟
- [設置](service-fabric-cli.md)服務結構 CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。