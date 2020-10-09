---
title: Azure 流量分析架構 |Microsoft Docs
description: 瞭解用來分析 Azure 網路安全性群組流量記錄的流量分析架構。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74666370"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>流量分析中的架構和資料匯總

流量分析是一個雲端式解決方案，可顯示雲端網路中的使用者和應用程式活動。 「流量分析」會分析網路監看員網路安全性群組 (NSG) 流量記錄，讓您深入了解 Azure 雲端中的流量。 透過流量分析，您可以：

- 視覺化您的 Azure 訂用帳戶之間的網路活動，並識別作用點。
- 透過開啟的連接埠、嘗試存取網際網路的應用程式，和連線至 Rogue 網路的虛擬機器 (VM) 等資訊，識別網路的安全性威脅並保護您的網路。
- 了解各個 Azure 區域間和網際網路的流量模式，使網路部署達到最理想的效能和容量。
- 找出導致網路連線失敗的網路設定錯誤。
- 以位元組、封包或流程來瞭解網路使用量。

### <a name="data-aggregation"></a>資料彙總

1. 在「FlowIntervalStartTime_t」和「FlowIntervalEndTime_t」之間 NSG 的所有流量記錄，會在流量分析進行處理之前，在儲存體帳戶中以一分鐘的間隔捕捉到 blob。
2. 流量分析的預設處理間隔為60分鐘。 這表示每個60分鐘的流量分析都會從儲存體挑選 blob 以進行匯總。 如果選擇的處理間隔為10分鐘，則流量分析會在每隔10分鐘後從儲存體帳戶挑選 blob。
3. 具有相同來源 IP、目的地 IP、目的地埠、NSG 名稱、NSG 規則、流量方向和傳輸層通訊協定的流程 (TCP 或 UDP)  (注意：已針對匯總) 排除來源埠，藉由流量分析 clubbed 為單一流程
4. 在 Log Analytics 中，此單一記錄會以流量分析的 (詳細資料進行裝飾) 和內嵌。此程式最多可能需要1小時的時間。
5. FlowStartTime_t 欄位表示在「FlowIntervalStartTime_t」和「FlowIntervalEndTime_t」之間的流程記錄處理間隔中， (相同四個元) 組的第一次出現。
6. 針對 TA 中的任何資源，UI 中指出的流程是 NSG 看到的流程總數，但在 Log Analytics 中，使用者只會看到單一、減少的記錄。 若要查看所有流程，請使用可從儲存體參考的 blob_id 欄位。 該記錄的總流程計數將符合在 blob 中看到的個別流程。

下列查詢可協助您查看過去30天內來自內部部署的所有流量記錄。
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
若要查看上述查詢中流程的 blob 路徑，請使用下列查詢：

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

上述查詢會建立直接存取 blob 的 URL。 具有預留位置的 URL 如下：

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>流量分析架構中的欄位
  > [!IMPORTANT]
  > 流量分析架構已于2019年8月22日更新。 新的架構會分開提供來源和目的地 Ip，而不需要剖析 System.windows.frameworkelement.flowdirection 欄位，讓查詢變得更簡單。 </br>
  > FASchemaVersion_s 從1更新為2。 </br>
  > 已淘汰的欄位： VMIP_s、Subscription_s、Region_s、NSGRules_s、Subnet_s、VM_s、NIC_s、PublicIPs_s、FlowCount_d </br>
  > 新欄位： SrcPublicIPs_s、DestPublicIPs_s、NSGRule_s </br>
  > 已淘汰的欄位將在2019年11月22日之前提供。

流量分析以 Log Analytics 為基礎，因此您可以對流量分析裝飾的資料執行自訂查詢，並設定相同的警示。

以下列出架構中的欄位和它們所代表的內容

| 欄位 | [格式] | 註解 |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | 適用于流量分析資料的資料表
| SubType_s | FlowLog | 流程記錄的子類型。 僅使用 "FlowLog"，SubType_s 的其他值用於產品的內部運作 |
| FASchemaVersion_s |   2   | 架構版本。 不反映 NSG 流量記錄版本 |
| TimeProcessed_t   | UTC 格式的日期和時間  | 流量分析處理來自儲存體帳戶之原始流量記錄的時間 |
| FlowIntervalStartTime_t | UTC 格式的日期和時間 |  流程記錄處理間隔的開始時間。 這是測量流程間隔的時間 |
| FlowIntervalEndTime_t | UTC 格式的日期和時間 | 流程記錄處理間隔的結束時間 |
| FlowStartTime_t | UTC 格式的日期和時間 |  流程的第一次出現 (，其會在「FlowIntervalStartTime_t」和「FlowIntervalEndTime_t」之間的流程記錄處理間隔中取得匯總) 。 此流程會根據匯總邏輯進行匯總 |
| FlowEndTime_t | UTC 格式的日期和時間 | 最後一次出現的流程 (，其會在「FlowIntervalStartTime_t」和「FlowIntervalEndTime_t」之間的流程記錄處理間隔中取得匯總) 。 就 flow 記錄 v2 而言，此欄位包含具有相同四個元組的最後一個流程開始 (在原始流程記錄中標示為 "B" 的時間)  |
| FlowType_s |  * IntraVNet <br> * Vnet 之間 <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * 私用未知 <br> * 未知 | 資料表下方附注中的定義 |
| SrcIP_s | 來源 IP 位址 | 如果是 AzurePublic 和 ExternalPublic 流程，將會空白 |
| DestIP_s | 目的地 IP 位址 | 如果是 AzurePublic 和 ExternalPublic 流程，將會空白 |
| VMIP_s | VM 的 IP | 用於 AzurePublic 和 ExternalPublic 流程 |
| PublicIP_s | 公用 IP 位址 | 用於 AzurePublic 和 ExternalPublic 流程 |
| DestPort_d | 目的地連接埠 | 流量傳入的埠 |
| L4Protocol_s  | * T <br> * U  | 傳輸通訊協定。 T = TCP <br> U = UDP |
| L7Protocol_s  | 通訊協定名稱 | 衍生自目的地埠 |
| FlowDirection_s | * I = 輸入<br> * O = 輸出 | 流程流入/傳出 NSG 的方向（依流程記錄） |
| FlowStatus_s  | * A = NSG 規則允許 <br> * D = NSG 規則拒絕  | 依流程記錄的 NSG 允許/nblocked 的流程狀態 |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | 與流程相關聯 (NSG) 的網路安全性群組 |
| NSGRules_s | \<Index value 0)>\|\<NSG_RULENAME>\|\<Flow Direction>\|\<Flow Status>\|\<FlowCount ProcessedByRule> |  允許或拒絕此流程的 NSG 規則 |
| NSGRule_s | NSG_RULENAME |  允許或拒絕此流程的 NSG 規則 |
| NSGRuleType_s | * 使用者定義 * 預設值 |   流程所使用的 NSG 規則類型 |
| MACAddress_s | MAC 位址 | 用來捕捉流程的 NIC 的 MAC 位址 |
| Subscription_s | 此欄位中已填入 Azure 虛擬網路/網路介面/虛擬機器的訂用帳戶 | 僅適用于 Flowtype] = S2S、P2S、AzurePublic、ExternalPublic、MaliciousFlow 和 UnknownPrivate 流量類型 (flow 類型，其中只有一個端是 azure)  |
| Subscription1_s | 訂用帳戶識別碼 | 流程中來源 IP 所屬虛擬網路/網路介面/虛擬機器的訂用帳戶識別碼 |
| Subscription2_s | 訂用帳戶識別碼 | 流程中目的地 IP 所屬虛擬網路/網路介面/虛擬機器的訂用帳戶識別碼 |
| Region_s | 流程中 IP 所屬虛擬網路/網路介面/虛擬機器的 Azure 區域 | 僅適用于 Flowtype] = S2S、P2S、AzurePublic、ExternalPublic、MaliciousFlow 和 UnknownPrivate 流量類型 (flow 類型，其中只有一個端是 azure)  |
| Region1_s | Azure 區域 | 流程中來源 IP 所屬的虛擬網路/網路介面/虛擬機器的 Azure 區域 |
| Region2_s | Azure 區域 | 流程中目的地 IP 所屬之虛擬網路的 Azure 區域 |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  與傳送或接收流量的 VM 相關聯的 NIC |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 與流程中來源 IP 相關聯的 NIC |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 與流程中的目的地 IP 相關聯的 NIC |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | 與網路介面 NIC_s 相關聯的虛擬機器 |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | 與流程中來源 IP 相關聯的虛擬機器 |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | 與流程中的目的地 IP 相關聯的虛擬機器 |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | 與 NIC_s 相關聯的子網 |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | 與流程中來源 IP 相關聯的子網 |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | 與流程中的目的地 IP 相關聯的子網 |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 與流程中來源 IP 相關聯的應用程式閘道 |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 與流程中的目的地 IP 相關聯的應用程式閘道 |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 與流程中來源 IP 相關聯的負載平衡器 |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 與流程中的目的地 IP 相關聯的負載平衡器 |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 與流程中來源 IP 相關聯的局域網路閘道 |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 與流程中目的地 IP 相關聯的局域網路閘道 |
| ConnectionType_s | 可能的值為 >vnetpeering、VpnGateway 和 ExpressRoute |    連接類型 |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | 連接名稱。 針對 flowtype] P2S，這會格式化為 <gateway name> _<VPN Client IP> |
| ConnectingVNets_s | 虛擬網路名稱的空格分隔清單 | 如果是中樞和輪輻拓撲，中樞虛擬網路將在此填入 |
| Country_s | 兩個字母的國家/地區代碼 (ISO 3166-1 Alpha-2)  | 針對 flow 類型 ExternalPublic 填入。 PublicIPs_s 欄位中的所有 IP 位址都將共用相同的國家/地區代碼 |
| AzureRegion_s | Azure 區域位置 | 針對 flow 類型 AzurePublic 填入。 PublicIPs_s 欄位中的所有 IP 位址都會共用 Azure 區域 |
| AllowedInFlows_d | | 允許的輸入流量計數。 這代表將相同四個元組輸入共用到流程被捕獲到網路介面的流程數目 |
| DeniedInFlows_d |  | 已拒絕的輸入流量計數。  (輸入至已捕捉流程的網路介面)  |
| AllowedOutFlows_d | | 輸出流量的計數，這些輸出流程可 (輸出至已捕捉流程的網路介面)  |
| DeniedOutFlows_d  | | 已拒絕的輸出流量計數 (輸出至已捕捉流程的網路介面)  |
| FlowCount_d | 已取代。 符合相同四個元組的流程總數。 在 flow 類型 ExternalPublic 和 AzurePublic 的情況下，計數也會包含來自不同 PublicIP 位址的流程。
| InboundPackets_d | 在套用 NSG 規則的網路介面上接收到的封包 | 這只會填入 NSG 流量記錄架構的第2版 |
| OutboundPackets_d  | 在套用 NSG 規則的網路介面上，以已攔截的形式傳送的封包 | 這只會填入 NSG 流量記錄架構的第2版 |
| InboundBytes_d |  在套用 NSG 規則的網路介面上接收的位元組數 | 這只會填入 NSG 流量記錄架構的第2版 |
| OutboundBytes_d | 在套用 NSG 規則的網路介面上，以已捕捉的位元組形式傳送 | 這只會填入 NSG 流量記錄架構的第2版 |
| CompletedFlows_d  |  | 只有第2版的 NSG 流量記錄架構才會填入非零值 |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 以橫條分隔的專案 |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 以橫條分隔的專案 |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 以橫條分隔的專案 |

### <a name="notes"></a>注意

1. 如果是 AzurePublic 和 ExternalPublic 流程，客戶擁有的 Azure VM IP 會填入 VMIP_s 欄位，而公用 IP 位址則會填入 PublicIPs_s 欄位。 針對這兩種流程類型，我們應該使用 VMIP_s 和 PublicIPs_s，而不是 SrcIP_s 和 DestIP_s 欄位。 針對 AzurePublic 和 ExternalPublicIP 位址，我們會進一步匯總，以便將內嵌至客戶 log analytics 工作區的記錄數目降至最少。 (此欄位即將淘汰，而且我們應該根據 azure VM 是否為流程中的來源或目的地，使用 SrcIP_ 和 DestIP_s) 
1. 流程類型的詳細資料：根據流程中涉及的 IP 位址，我們會將流程分類至下列流程類型：
1. IntraVNet –流程中的 IP 位址都位於相同的 Azure 虛擬網路中。
1. Vnet 之間-流程中的 IP 位址位於兩個不同的 Azure 虛擬網路中。
1. S2S – (站對站) 其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址屬於客戶網路 (網站) 透過 VPN 閘道或 Express Route 連線到 Azure 虛擬網路。
1. P2S- (點對站) 其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址屬於客戶網路 (網站) 透過 VPN 閘道連線至 Azure 虛擬網路。
1. AzurePublic-其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址屬於 Microsoft 所擁有的 Azure 內部公用 IP 位址。 客戶所擁有的公用 IP 位址不會是此流程類型的一部分。 例如，將流量傳送至 Azure 服務 (儲存體端點) 的任何客戶擁有 VM 都會分類為此流程類型。
1. ExternalPublic-其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址是不在 Azure 中的公用 IP，則在流量分析針對「FlowIntervalStartTime_t」和「FlowIntervalEndTime_t」之間的處理間隔所耗用的 ASC 摘要中不會回報為惡意。
1. MaliciousFlow-其中一個 IP 位址屬於 azure 虛擬網路，而另一個 IP 位址是不在 Azure 中的公用 IP 位址，而且在流量分析針對「FlowIntervalStartTime_t」和「FlowIntervalEndTime_t」之間的處理間隔所耗用的 ASC 摘要中回報為惡意。
1. UnknownPrivate-屬於 Azure 虛擬網路的其中一個 IP 位址，另一個 IP 位址屬於 RFC 1918 中定義的私人 IP 範圍，且無法由流量分析對應至客戶擁有的網站或 Azure 虛擬網路。
1. 未知-無法將流程中的其中一個 IP 位址對應至 Azure 中的客戶拓撲，以及內部部署 (網站) 。
1. 某些功能變數名稱會附加至 \_ s 或 \_ d。 這些不表示來源和目的地，但會分別指出資料類型的字串和小數位數。

### <a name="next-steps"></a>後續步驟
若要取得常見問題的解答，請參閱使用 [分析常見問題](traffic-analytics-faq.md) 以查看功能的詳細資料，請參閱使用 [分析檔](traffic-analytics.md)
