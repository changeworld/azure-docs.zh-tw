---
title: Azure Service Fabric CLI-sfctl 網狀閘道
description: 深入瞭解 sfctl，這是 Azure Service Fabric 命令列介面。 包含用來取得和刪除 Service Fabric 網格閘道資源的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3e398ff8a1a0a28fd9d0650a7c9d18ba04817d33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906011"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
取得和刪除 mesh 閘道資源。

## <a name="commands"></a>命令

|Command|描述|
| --- | --- |
| [刪除] | 可刪除閘道資源。 |
| list | 可列出所有閘道資源。 |
| 顯示 | 可取得指定名稱的閘道資源。 |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl mesh gateway delete
可刪除閘道資源。

可刪除透過名稱識別的閘道資源。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --name -n [必要] | 閘道資源的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-mesh-gateway-list"></a>sfctl mesh gateway list
可列出所有閘道資源。

可取得指定資源群組中所有閘道資源的相關資訊。 資訊包括閘道的描述與其他屬性。

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-mesh-gateway-show"></a>sfctl mesh gateway show
可取得指定名稱的閘道資源。

可取得指定名稱閘道資源的相關資訊。 資訊包括閘道的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --name -n [必要] | 閘道資源的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |


## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md)Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。