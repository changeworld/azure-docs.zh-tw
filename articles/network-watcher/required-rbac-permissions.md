---
title: 使用功能所需的 Azure RBAC 許可權
titleSuffix: Azure Network Watcher
description: 了解使用網路監看員功能時，需要哪些 Azure 角色型存取控制權限。
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: damendo
ms.openlocfilehash: 5e048729aa1d37a63678451525f8f37612c8c464
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426746"
---
# <a name="azure-role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>使用網路監看員功能所需的 Azure 角色型存取控制許可權

Azure 角色型存取控制 (Azure RBAC) 可讓您只將特定動作指派給組織成員，以完成其指派的責任。 若要使用網路監看員的功能，登入 Azure 的帳號必須受指派為[擁有者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner)、[參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)、[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor)等內建角色，或者是受指派以下段落中各項網路監看員功能所屬動作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。 若要深入了解網路監看員的功能，請參閱[網路監看員是什麼？](network-watcher-monitoring-overview.md)。

## <a name="network-watcher"></a>網路監看員

| 動作                                                              | 描述                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | 取得網路監看員                                          |
| Microsoft.Network/networkWatchers/write                             | 建立或更新網路監看員                             |
| Microsoft.Network/networkWatchers/delete                            | 刪除網路監看員                                       |

## <a name="nsg-flow-logs"></a>NSG 流程記錄

| 動作                                                              | 描述                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | 設定流量記錄                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | 流量記錄的查詢狀態                                    |

## <a name="connection-troubleshoot"></a>疑難排解連線

| 動作                                                              | 描述                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | 起始連線疑難排解測試
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | 連線疑難排解測試的查詢結果                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | 執行連線疑難排解測試                             |

## <a name="connection-monitor"></a>連線監視

| 動作                                                              | 描述                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | 啟動連線監視                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | 停止連線監視                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | 查詢連線監視                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | 取得連線監視                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | 建立連線監視                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | 刪除連線監視                                    |

## <a name="packet-capture"></a>封包擷取

| 動作                                                              | 描述                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | 查詢封包擷取的狀態                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | 停止封包擷取                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | 取得封包擷取                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | 建立封包擷取                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | 刪除封包擷取                                        |

## <a name="ip-flow-verify"></a>IP 流程驗證

| 動作                                                              | 描述                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | 驗證 IP 流量                                              |

## <a name="next-hop"></a>下一個躍點

| 動作                                                              | 描述                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | 從 VM 取得下一個躍點                                     |

## <a name="network-security-group-view"></a>網路安全性群組檢視

| 動作                                                              | 描述                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | 檢視安全性群組                                           |

## <a name="topology"></a>拓撲

| 動作                                                              | 描述                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | 取得拓撲                                                   |

## <a name="reachability-report"></a>連線性報告

| 動作                                                              | 描述                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | 取得 Azure 連線性報告                               |


## <a name="additional-actions"></a>其他動作

網路監看員功能也需要下列動作：

| 動作 (s)                                                            | 描述                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft.Authorization/\*/Read                                     | 用來提取 Azure 角色指派和原則定義          |
| Microsoft.Resources/subscriptions/resourceGroups/Read               | 用來列舉訂用帳戶中的所有資源群組    |
| Microsoft.Storage/storageAccounts/Read                              | 用來取得指定之儲存體帳戶的屬性   |
| Microsoft. Storage/storageAccounts/listServiceSas/Action， </br> Microsoft. Storage/storageAccounts/listAccountSas/Action， <br> Microsoft.Storage/storageAccounts/listKeys/Action| 用來提取共用存取簽章 (SAS) [安全地存取儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) 並寫入儲存體帳戶 |
| Microsoft. Compute/virtualMachines/Read， </br> Microsoft.Compute/virtualMachines/Write| 用來登入 VM、進行封包捕獲，並將其上傳至儲存體帳戶|
| Microsoft.Compute/virtualMachines/extensions/Read </br> Microsoft.Compute/virtualMachines/extensions/Write| 用來檢查網路監看員延伸模組是否存在，並視需要進行安裝 |
| Microsoft. Compute/virtualMachineScaleSets/Read， </br> Microsoft.Compute/virtualMachineScaleSets/Write| 用來存取虛擬機器擴展集、進行封包捕獲，並將它們上傳至儲存體帳戶|
| Microsoft. Compute/virtualMachineScaleSets/extensions/Read、 </br> Microsoft.Compute/virtualMachineScaleSets/extensions/Write| 用來檢查網路監看員延伸模組是否存在，並視需要進行安裝 |
| Microsoft.Insights/alertRules/*                                     | 用來設定計量警示                                     |
| Microsoft.Support/*                                                 | 用來從網路監看員建立和更新支援票證 |
