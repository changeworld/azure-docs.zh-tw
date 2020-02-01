---
title: Azure Service Fabric CLI-sfctl 是
description: 深入瞭解 sfctl，這是 Azure Service Fabric 命令列介面。 包含用來管理基礎結構的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a64aaca97c9df61d795c82d64e6048d6f9ae032f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906085"
---
# <a name="sfctl-is"></a>sfctl is
查詢命令，並將其傳送至基礎結構服務。

## <a name="commands"></a>命令

|Command|說明|
| --- | --- |
| 命令 | 叫用指定基礎結構服務執行個體上的系統管理命令。 |
| 查詢 | 叫用指定基礎結構服務執行個體上的唯讀查詢。 |

## <a name="sfctl-is-command"></a>sfctl is command
叫用指定基礎結構服務執行個體上的系統管理命令。

對於設定一或多個基礎結構服務執行個體的叢集，此 API 提供將基礎結構特有的命令傳送至基礎結構服務特定執行個體的方法。 可用的命令和其對應的回應格式會根據叢集執行所在的基礎結構而有所不同。 這個 API 支援 Service Fabric 平台；這不表示直接從您的程式碼使用。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --command [必要] | 要叫用的命令文字。 命令的內容與基礎結構有關。 |
| --service-id | 基礎結構服務的識別。 <br><br> 這是不含 'fabric\:' URI 配置的基礎結構服務完整名稱。 只有當叢集中有多個基礎結構服務執行個體正在執行時，才需要提供此參數。 |
| --timeout -t | 預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-is-query"></a>sfctl is query
叫用指定基礎結構服務執行個體上的唯讀查詢。

對於設定一或多個基礎結構服務執行個體的叢集，此 API 提供將基礎結構特有的查詢傳送至基礎結構服務特定執行個體的方法。 可用的命令和其對應的回應格式會根據叢集執行所在的基礎結構而有所不同。 這個 API 支援 Service Fabric 平台；這不表示直接從您的程式碼使用。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --command [必要] | 要叫用的命令文字。 命令的內容與基礎結構有關。 |
| --service-id | 基礎結構服務的識別。 <br><br> 這是不含 'fabric\:' URI 配置的基礎結構服務完整名稱。 只有當叢集中有多個基礎結構服務執行個體正在執行時，才需要提供此參數。 |
| --timeout -t | 預設值\: 60。 |

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
