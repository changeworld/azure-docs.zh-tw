---
title: Azure 流量分析架構 |微軟文檔
description: 瞭解流量分析的架構以分析 Azure 網路安全性群組流日誌。
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: ccfbb92c27e4508595f19c2ea6900730cde609b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74666370"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>流量分析中的架構和資料聚合

流量分析是一個雲端式解決方案，可顯示雲端網路中的使用者和應用程式活動。 「流量分析」會分析網路監看員網路安全性群組 (NSG) 流量記錄，讓您深入了解 Azure 雲端中的流量。 透過流量分析，您可以：

- 視覺化您的 Azure 訂用帳戶之間的網路活動，並識別作用點。
- 透過開啟的連接埠、嘗試存取網際網路的應用程式，和連線至 Rogue 網路的虛擬機器 (VM) 等資訊，識別網路的安全性威脅並保護您的網路。
- 了解各個 Azure 區域間和網際網路的流量模式，使網路部署達到最理想的效能和容量。
- 找出導致網路連線失敗的網路設定錯誤。
- 瞭解以位元組、資料包或流為單位的網路使用方式。

### <a name="data-aggregation"></a>資料彙總

1. 在流量分析處理之前，在"FlowIntervalStartTime_t"和"FlowIntervalEndTime_t"之間 NSG 上的所有流日誌在存儲帳戶中每隔一分鐘作為 blob 捕獲。
2. 流量分析的預設處理間隔為 60 分鐘。 這意味著流量分析每 60 分鐘從存儲中選取一個 blob 進行聚合。 如果選擇的處理間隔為 10 分鐘，流量分析將在每 10 分鐘從存儲帳戶中選取 blob。
3. 具有相同源 IP、目標 IP、目標埠、NSG 名稱、NSG 規則、流方向和傳輸層協定（TCP 或 UDP）（注意：源埠排除用於聚合）的流通過流量分析被綁定到單個流中
4. 此單條記錄被修飾（下面部分的詳細資訊）並在流量分析的日誌分析中引入。 此過程最多可能需要 1 小時。
5. FlowStartTime_t欄位指示在"FlowIntervalStartTime_t"和"FlowIntervalEndTime_t"之間的流日誌處理間隔中首次出現此類聚合流（相同的四元組）。
6. 對於 TA 中的任何資源，UI 中指示的流是 NSG 看到的總流，但在日誌分析中，使用者將只看到單個減少的記錄。 要查看所有流，請使用blob_id欄位，可以從存儲引用該欄位。 該記錄的總流量計數將與 blob 中看到的單個流匹配。

以下查詢可説明您查看過去 30 天內來自本地的所有流日誌。
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
要查看上述查詢中流的 blob 路徑，請使用下面的查詢：

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

上述查詢構造一個 URL 來直接存取 Blob。 具有預留位置的 URL 如下所示：

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>流量分析架構中使用的欄位
  > [!IMPORTANT]
  > 流量分析架構已于 2019 年 8 月 22 日更新。 新的架構提供源和目標 IP 分別刪除解析 FlowDirection 欄位使查詢更簡單的需要。 </br>
  > FASchemaVersion_s從 1 更新到 2。 </br>
  > 已棄用欄位：VMIP_s、Subscription_s、Region_s、NSGRules_s、Subnet_s、VM_s、NIC_s、PublicIPs_s、FlowCount_d </br>
  > 新欄位：SrcPublicIPs_s、DestPublicIPs_s、NSGRule_s </br>
  > 棄用欄位將持續到 2019 年 11 月 22 日。

流量分析構建在日誌分析之上，因此您可以對流量分析修飾的資料運行自訂查詢，並在相同的資料上設置警報。

下面列出的是架構中的欄位及其表示的內容

| 欄位 | [格式] | 註解 |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | 流量分析資料表
| SubType_s | 流日誌 | 流日誌的子類型。 僅使用"FlowLog"，SubType_s的其他值用於產品的內部工作 |
| FASchemaVersion_s |   2   | 架構版本。 不反映 NSG 流日誌版本 |
| TimeProcessed_t   | 以 UTC 表示的日期和時間  | 流量分析處理來自存儲帳戶的原始流日誌的時間 |
| FlowIntervalStartTime_t | 以 UTC 表示的日期和時間 |  流日誌處理間隔的開始時間。 這是測量流間隔的時間 |
| FlowIntervalEndTime_t | 以 UTC 表示的日期和時間 | 流日誌處理間隔的結束時間 |
| FlowStartTime_t | 以 UTC 表示的日期和時間 |  在"FlowIntervalStartTime_t"和"FlowIntervalEndTime_t"之間的流日誌處理間隔中首次出現流（將聚合）。 此流基於聚合邏輯進行聚合 |
| FlowEndTime_t | 以 UTC 表示的日期和時間 | 流（將聚合）在"FlowIntervalStartTime_t"和"FlowIntervalEndTime_t"之間的流日誌處理間隔中的最後一次出現。 在流日誌 v2 方面，此欄位包含啟動具有相同四元組的最後一個流的時間（在原始流記錄中標記為"B"） |
| FlowType_s |  • 內部網路 <br> • 互維網路 <br> • S2S <br> • P2S <br> • Azure 公共 <br> • 外部公共 <br> • 惡意流 <br> • 未知私人 <br> • 未知 | 下表下方注釋中的定義 |
| SrcIP_s | 來源 IP 位址 | 在 Azure 公共和外部公共流的情況下，將為空 |
| DestIP_s | 目的地 IP 位址 | 在 Azure 公共和外部公共流的情況下，將為空 |
| VMIP_s | VM 的 IP | 用於 Azure 公共和外部公共流 |
| PublicIP_s | 公用 IP 位址 | 用於 Azure 公共和外部公共流 |
| DestPort_d | 目的地連接埠 | 傳入流量的埠 |
| L4Protocol_s  | • T <br> • U  | 運輸協定。 T = TCP <br> U = UDP |
| L7Protocol_s  | 通訊協定名稱 | 派生自目標埠 |
| FlowDirection_s | • I = 入境<br> • O = 出站 | 根據流日誌的 NSG 流入/流出方向 |
| FlowStatus_s  | • A = NSG 規則允許 <br> • D = 被 NSG 規則拒絕  | NSG 根據流日誌允許/阻止的流狀態 |
| NSGList_s | \<訂閱 ID \/><\/ RESOURCEGROUP_NAME<NSG_NAME>>> | 與流關聯的網路安全性群組 （NSG） |
| NSGRules_s | \<索引值\|\<0）>NSG_RULENAME>\|\<流方向\|\<>流計數\|\<>流計數處理> |  允許或拒絕此流的 NSG 規則 |
| NSGRule_s | NSG_RULENAME |  允許或拒絕此流的 NSG 規則 |
| NSGRuleType_s | • 使用者已定義 = 預設值 |   流使用的 NSG 規則的類型 |
| MACAddress_s | MAC 位址 | 捕獲流的 NIC 的 MAC 位址 |
| Subscription_s | 在此欄位中填充 Azure 虛擬網路/網路介面/虛擬機器的訂閱 | 僅適用于 FlowType = S2S、P2S、Azure 公共、外部公共、惡意流和未知私有流類型（只有一側是 azure 的流類型） |
| Subscription1_s | 訂用帳戶識別碼 | 流中源 IP 所屬的虛擬網路/網路介面/虛擬機器的訂閱 ID |
| Subscription2_s | 訂用帳戶識別碼 | 流中目標 IP 所屬的虛擬網路/網路介面/虛擬機器的訂閱 ID |
| Region_s | 流中 IP 所屬的虛擬網路/網路介面/虛擬機器的 Azure 區域 | 僅適用于 FlowType = S2S、P2S、Azure 公共、外部公共、惡意流和未知私有流類型（只有一側是 azure 的流類型） |
| Region1_s | Azure 區域 | 流中源 IP 所屬的虛擬網路/網路介面/虛擬機器的 Azure 區域 |
| Region2_s | Azure 區域 | 流中目標 IP 所屬的虛擬網路的 Azure 區域 |
| NIC_s | \<resourcegroup_Name>\/\<網路介面名稱> |  與 VM 發送或接收流量關聯的 NIC |
| NIC1_s | <resourcegroup_Name>/\<網路介面名稱> | 與流中的源 IP 關聯的 NIC |
| NIC2_s | <resourcegroup_Name>/\<網路介面名稱> | 與流中的目標 IP 關聯的 NIC |
| VM_s | <resourcegroup_Name>\/\<網路介面名稱> | 與網路介面關聯的虛擬機器NIC_s |
| VM1_s | resourcegroup_Nameresourcegroup_Name>/\<虛擬機器名稱>< | 與流中的源 IP 關聯的虛擬機器 |
| VM2_s | resourcegroup_Nameresourcegroup_Name>/\<虛擬機器名稱>< | 與流中的目標 IP 關聯的虛擬機器 |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<子網名稱> | 與NIC_s關聯的子網 |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<子網名稱> | 與流中的源 IP 關聯的子網 |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<子網名稱>    | 與流中的目標 IP 關聯的子網 |
| ApplicationGateway1_s | \<訂閱 ID\<>/資源組名稱\<>/應用程式閘道名稱> | 與流中的源 IP 關聯的應用程式閘道 |
| ApplicationGateway2_s | \<訂閱 ID\<>/資源組名稱\<>/應用程式閘道名稱> | 與流中的目標 IP 關聯的應用程式閘道 |
| LoadBalancer1_s | \<訂閱 ID\<>/資源組名稱\<>/負載平衡器名稱> | 負載等化器與流中的源 IP 關聯 |
| LoadBalancer2_s | \<訂閱 ID\<>/資源組名稱\<>/負載平衡器名稱> | 與流中的目標 IP 關聯的負載等化器 |
| LocalNetworkGateway1_s | \<訂閱 ID\<>/資源組名稱\<>/本地網路閘道名稱> | 與流中的源 IP 關聯的本地網路閘道 |
| LocalNetworkGateway2_s | \<訂閱 ID\<>/資源組名稱\<>/本地網路閘道名稱> | 與流中的目標 IP 關聯的本地網路閘道 |
| ConnectionType_s | 可能的值是 VNet 對等、VpnGateway 和 ExpressRoute |    連接類型 |
| ConnectionName_s | \<訂閱 ID\<>/資源組名稱\<>/連接名稱> | 連接名稱。 對於流型 P2S，這將格式化為<gateway name>|<VPN Client IP> |
| ConnectingVNets_s | 虛擬網路名稱的空間分隔清單 | 對於集線器和分支拓撲，將在此處填充集線器虛擬網路 |
| Country_s | 兩個字母的國家/地區代碼 （ISO 3166-1 阿爾法-2） | 為流類型外部公共填充。 PublicIPs_s欄位中的所有 IP 位址將共用相同的國家/地區代碼 |
| AzureRegion_s | Azure 區域位置 | 為流類型 AzurePublic 填充。 PublicIPs_s欄位中的所有 IP 位址將共用 Azure 區域 |
| AllowedInFlows_d | | 允許的入站流計數。 這表示共用相同的四元組入站到捕獲流的網路介面的流數 |
| DeniedInFlows_d |  | 被拒絕的入站流計數。 （入站到捕獲流的網路介面） |
| AllowedOutFlows_d | | 允許的出站流計數（從出站到捕獲流的網路介面） |
| DeniedOutFlows_d  | | 被拒絕的出站流計數（從出站到捕獲流的網路介面） |
| FlowCount_d | 已被取代。 匹配相同四元組的總流。 在流類型外部公共和 AzurePublic 的情況下，計數也將包括來自各種 PublicIP 位址的流。
| InboundPackets_d | 在應用 NSG 規則的網路介面捕獲的資料包 | 這僅填充于 NSG 流日誌架構的版本 2 |
| OutboundPackets_d  | 在應用 NSG 規則的網路介面捕獲的資料包 | 這僅填充于 NSG 流日誌架構的版本 2 |
| InboundBytes_d |  在應用 NSG 規則的網路介面捕獲的位元組 | 這僅填充于 NSG 流日誌架構的版本 2 |
| OutboundBytes_d | 在應用 NSG 規則的網路介面捕獲的位元組 | 這僅填充于 NSG 流日誌架構的版本 2 |
| CompletedFlows_d  |  | 這僅填充為 NSG 流日誌架構的版本 2 的非零值 |
| PublicIPs_s | <PUBLIC_IP \| \<>\| \< \| \< \| \|INBOUND_PACKETS OUTBOUND_PACKETS>FLOW_STARTED_COUNT>FLOW_ENDED_COUNTFLOW_ENDED_COUNT>>>>OUTBOUND_BYTES>INBOUND_BYTES>>>>\< \< \| \< | 以條形分隔的條目 |
| SrcPublicIPs_s | <\| \< \<INBOUND_BYTES OUTBOUND_BYTES \| \< \| \< \| \<>\|>OUTBOUND_PACKETS>FLOW_ENDED_COUNT FLOW_STARTED_COUNT>SOURCE_PUBLIC_IPSOURCE_PUBLIC_IP>>>>INBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETS>>>> \< \| | 以條形分隔的條目 |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP \| \<OUTBOUND_BYTES \| \< \| \< \| \<>\<>OUTBOUND_PACKETS>FLOW_ENDED_COUNT FLOW_STARTED_COUNT>>INBOUND_PACKETS>INBOUND_BYTES>INBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETSINBOUND_PACKETS> \| \| \< | 以條形分隔的條目 |

### <a name="notes"></a>注意

1. 對於 Azure 公共和外部公共流，客戶擁有的 Azure VM IP 填充在VMIP_s欄位中，而公共 IP 位址則填充在PublicIPs_s欄位中。 對於這兩種流類型，我們應該使用VMIP_s和PublicIPs_s而不是SrcIP_s和DestIP_s欄位。 對於 AzurePublic 和外部公共IP 位址，我們進一步聚合，以便引入到客戶日誌分析工作區的記錄數量最小。（此欄位將很快棄用，我們應該使用SrcIP_和DestIP_s具體取決於 azure VM 是流中的源還是目標）
1. 流類型的詳細資訊：根據流中涉及的 IP 位址，我們將流分類為以類型：
1. 內部 VNet – 流中的兩個 IP 位址都駐留在同一 Azure 虛擬網路中。
1. InterVNet - 流中的 IP 位址駐留在兩個不同的 Azure 虛擬網路中。
1. S2S = （網站到網站） 其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址屬於通過 VPN 閘道或快速路由連接到 Azure 虛擬網路的客戶網路（網站）。
1. P2S - （點對點） 其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址屬於通過 VPN 閘道連接到 Azure 虛擬網路的客戶網路（網站）。
1. AzurePublic - 其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址屬於 Microsoft 擁有的 Azure 內部公共 IP 位址。 客戶擁有的公共 IP 位址不會是此流類型的一部分。 例如，任何客戶擁有的 VM 向 Azure 服務（存儲終結點）發送流量都將在此流類型下分類。
1. 外部公共 - 其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址不在 Azure 中的公共 IP，在流量分析在"" 之間的處理間隔使用 ASC 源中不報告為惡意。FlowIntervalStartTime_t"和"FlowIntervalEndTime_t"。
1. 惡意流 - 其中一個 IP 位址屬於 azure 虛擬網路，而另一個 IP 位址是不在 Azure 中的公共 IP，在流量分析在"" 之間的處理間隔使用 ASC 源中報告為惡意 IP。FlowIntervalStartTime_t"和"FlowIntervalEndTime_t"。
1. 未知專用 - 其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址屬於 RFC 1918 中定義的專用 IP 範圍，並且流量分析無法映射到客戶擁有的網站或 Azure 虛擬網路。
1. 未知 = 無法將流中的任一 IP 位址與 Azure 中的客戶拓撲以及本地（網站）映射。
1. 某些欄位名稱隨\_s 或\_d 追加。 它們不表示源和目標，但分別表示資料類型字串和小數。

### <a name="next-steps"></a>後續步驟
要獲取常見問題解答，請參閱[流量分析常見問題解答](traffic-analytics-faq.md)有關功能的詳細資訊，請參閱[流量分析文檔](traffic-analytics.md)
