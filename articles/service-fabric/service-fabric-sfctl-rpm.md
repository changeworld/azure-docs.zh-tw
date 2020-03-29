---
title: Azure 服務結構 CLI-sfctl rpm
description: 瞭解 sfctl，Azure 服務結構命令列介面。 包括維修管理器服務的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1787c0c8a8d5994ce1f1e9e4c55f155bbca6934c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904936"
---
# <a name="sfctl-rpm"></a>sfctl rpm
查詢命令，並將其傳送至修復管理員服務。

## <a name="commands"></a>命令

|Command|描述|
| --- | --- |
| approve-force | 強制核准指定的修復工作。 |
| delete | 刪除已完成的修復工作。 |
| list | 取得符合所指定篩選條件的修復工作清單。 |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm approve-force
強制核准指定的修復工作。

這個 API 支援 Service Fabric 平台；這不表示直接從您的程式碼使用。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --task-id [必要] | 修復工作的識別碼。 |
| --version | 修復工作的目前版本號碼。 如果不是零，則只有在此值符合修復工作的實際目前版本時，要求才會成功。 如果是零，則會不執行任何版本檢查。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
刪除已完成的修復工作。

這個 API 支援 Service Fabric 平台；這不表示直接從您的程式碼使用。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --task-id [必要] | 要刪除之已完成修復工作的識別碼。 |
| --version | 修復工作的目前版本號碼。 如果不是零，則只有在此值符合修復工作的實際目前版本時，要求才會成功。 如果是零，則會不執行任何版本檢查。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-rpm-list"></a>sfctl rpm list
取得符合所指定篩選條件的修復工作清單。

這個 API 支援 Service Fabric 平台；這不表示直接從您的程式碼使用。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --executor-filter | 其宣告的工作應該包含在清單中的修復執行程式名稱。 |
| --state-filter | 下列值的位元 OR，指定結果清單中應該包含哪些工作狀態。 <ul><li>1 - 已建立</li><li>2 - 索賠</li><li>4 - 準備</li><li>8 - 已批准</li><li>16 - 執行</li><li>32 - 恢復</li><li>64 - 已完成</li></ul>
| --task-id-filter | 要比對的修復工作識別碼前置詞。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |


## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。
