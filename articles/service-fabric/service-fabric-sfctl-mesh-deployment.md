---
title: Azure Service Fabric CLI-sfctl 網狀架構部署
description: 深入瞭解 sfctl，這是 Azure Service Fabric 命令列介面。 包含用來建立 Service Fabric 網格資源的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906035"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
建立 Service Fabric Mesh 資源。

## <a name="commands"></a>命令

|Command|說明|
| --- | --- |
| 建立 | 建立 Service Fabric Mesh 資源的部署。 |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
建立 Service Fabric Mesh 資源的部署。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --input-yaml-files [必要] | 以逗號分隔的所有 yaml 檔案或包含 yaml 檔案之目錄（遞迴）的相對或絕對路徑的相對或絕對檔案路徑。 |
| --parameters | Yaml 檔或 json 物件的相對或絕對路徑，其中包含需要覆寫的參數。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

### <a name="examples"></a>範例

可透過覆寫 yaml 檔案中提到的參數，將所有資源合併並部署到叢集
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

可透過覆寫 yaml 檔案中提到的參數，將目錄中的所有資源合併並部署到叢集

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

藉由覆寫直接傳遞為 json 物件的參數，將目錄中的所有資源合併並部署到叢集
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。
