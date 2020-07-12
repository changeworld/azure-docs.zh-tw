---
title: Azure Service Fabric CLI-sfctl 網狀網路
description: 深入瞭解 sfctl，這是 Azure Service Fabric 命令列介面。 包含用來取得和刪除 Service Fabric 網格網路資源的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b2b2c444bb492fa6c6b945a82090e91963fb1a8
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245855"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
可取得和刪除 mesh 網路資源。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| 刪除 | 可刪除網路資源。 |
| list | 可列出所有網路資源。 |
| 顯示 | 可取得指定名稱的網路資源。 |

## <a name="sfctl-mesh-network-delete"></a>sfctl mesh network delete
可刪除網路資源。

可刪除透過名稱識別的網路資源。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --name -n [必要] | 網路的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-mesh-network-list"></a>sfctl mesh network list
可列出所有網路資源。

可取得指定資源群組中所有網路資源的相關資訊。 資訊包括網路的描述與其他屬性。

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-mesh-network-show"></a>sfctl mesh network show
可取得指定名稱的網路資源。

可取得指定名稱網路資源的相關資訊。 資訊包括網路的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --name -n [必要] | 網路的名稱。 |

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
