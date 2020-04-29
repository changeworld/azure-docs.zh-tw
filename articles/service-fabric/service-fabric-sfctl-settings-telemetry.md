---
title: Azure Service Fabric CLI-sfctl 設定遙測
description: 深入瞭解 sfctl，這是 Azure Service Fabric 命令列介面。 包含用來設定 sfctl 遙測的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6af5fa944ef399756f9e890ddd77a7f5f32e2bfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76903024"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
在 sfctl 的此執行個體設定遙測設定。

Sfctl telemetry 會收集命令名稱 (不提供參數或其名稱)、sfctl 版本、OS 類型、python 版本、命令成功或失敗，以及傳回的錯誤訊息。

## <a name="commands"></a>命令

|Command|描述|
| --- | --- |
| set-telemetry | 開啟或關閉遙測。 |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
開啟或關閉遙測。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --off | 關閉遙測。 |
| --on | 開啟遙測。 這是預設值。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
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


## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md)Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。