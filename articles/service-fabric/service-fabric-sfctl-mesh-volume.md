---
title: Azure 服務結構 CLI-sfctl 網格卷
description: 瞭解 sfctl，Azure 服務結構命令列介面。 包括用於獲取和刪除卷資源的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 0e5ba1a311bd0480c56ee40edf01fc13ec01664a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905920"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
取得和刪除磁碟區資源。

## <a name="commands"></a>命令

|Command|描述|
| --- | --- |
| delete | 可刪除磁碟區資源。 |
| list | 可列出所有磁碟區資源。 |
| 顯示 | 可取得指定名稱的磁碟區資源。 |

## <a name="sfctl-mesh-volume-delete"></a>sfctl mesh volume delete
可刪除磁碟區資源。

可刪除透過名稱識別的磁碟區資源。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --name -n [必要] | 磁碟區的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-mesh-volume-list"></a>sfctl mesh volume list
可列出所有磁碟區資源。

可取得指定資源群組中所有磁碟區資源的相關資訊。 資訊包括磁碟區的描述與其他屬性。

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-mesh-volume-show"></a>sfctl mesh volume show
可取得指定名稱的磁碟區資源。

可取得指定名稱磁碟區資源的相關資訊。 資訊包括磁碟區的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --name -n [必要] | 磁碟區的名稱。 |

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