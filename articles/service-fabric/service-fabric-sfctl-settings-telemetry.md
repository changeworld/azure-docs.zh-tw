---
title: Azure Service Fabric CLI-sfctl 設定遙測
description: 瞭解 Azure Service Fabric 命令列介面的 sfctl。 包含用來設定 sfctl 遙測的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ef720a14617b4131474d50875701d0ef27df4151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245497"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
在 sfctl 的此執行個體設定遙測設定。

Sfctl telemetry 會收集命令名稱 (不提供參數或其名稱)、sfctl 版本、OS 類型、python 版本、命令成功或失敗，以及傳回的錯誤訊息。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
| set-telemetry | 開啟或關閉遙測。 |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
開啟或關閉遙測。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --off | 關閉遙測。 |
| --on | 開啟遙測。 這是預設值。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

### <a name="examples"></a>範例

關閉遙測。

```
sfctl settings telemetry set_telemetry --off
```

開啟遙測。

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>接下來的步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](./scripts/sfctl-upgrade-application.md)來使用 Service Fabric CLI。
