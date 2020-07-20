---
title: Azure 流量分析架構 |Microsoft Docs
description: 瞭解流量分析的架構來分析 Azure 網路安全性群組流量記錄。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74666370"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>流量分析中的架構和資料匯總

流量分析是一個雲端式解決方案，可顯示雲端網路中的使用者和應用程式活動。 「流量分析」會分析網路監看員網路安全性群組 (NSG) 流量記錄，讓您深入了解 Azure 雲端中的流量。 透過流量分析，您可以：

- 視覺化您的 Azure 訂用帳戶之間的網路活動，並識別作用點。
- 透過開啟的連接埠、嘗試存取網際網路的應用程式，和連線至 Rogue 網路的虛擬機器 (VM) 等資訊，識別網路的安全性威脅並保護您的網路。
- 了解各個 Azure 區域間和網際網路的流量模式，使網路部署達到最理想的效能和容量。
- 找出導致網路連線失敗的網路設定錯誤。
- 瞭解網路使用量（位元組、封包或流量）。

### <a name="data-aggregation"></a>資料彙總

1. 在「FlowIntervalStartTime_t」和「FlowIntervalEndTime_t」之間的 NSG 中，所有流量記錄都會在儲存體帳戶中以一分鐘的間隔，以 blob 的形式在流量分析進行處理之前，以一分鐘為單位來捕捉。
2. 流量分析的預設處理間隔為60分鐘。 這表示每個60分鐘的流量分析都會從儲存體挑選 blob 以進行匯總。 如果選擇的處理間隔為10分鐘，則流量分析會在每隔10分鐘後從儲存體帳戶挑選 blob。
3. 具有相同來源 IP、目的地 IP、目的地埠、NSG 名稱、NSG 規則、流量方向和傳輸層通訊協定（TCP 或 UDP）的流程（注意：排除匯總的來源埠）會透過流量分析 clubbed 至單一流程
4. 這項單一記錄已裝飾（下一節中的詳細資料），並由流量分析在 Log Analytics 中內嵌。此程式最多可能需要1小時的上限。
5. FlowStartTime_t 欄位指出在「FlowIntervalStartTime_t」和「FlowIntervalEndTime_t」之間的流量記錄處理間隔中，第一次出現這類的匯總流程（相同的四個元組）。
6. 對於 TA 中的任何資源，UI 中指出的流程是 NSG 所看到的流程總數，但在 Log Analytics 中，使用者只會看到單一的已縮減記錄。 若要查看所有流程，請使用 [blob_id] 欄位，其可從儲存體參考。 該記錄的流程計數總計會符合 blob 中所見的個別流程。

下列查詢可協助您查看過去30天內來自內部部署的所有流量記錄。
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
若要在上述查詢中查看流程的 blob 路徑，請使用下列查詢：

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

上述查詢會建立直接存取 blob 的 URL。 具有預留位置的 URL 如下所示：

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>用於流量分析架構的欄位
  > [!IMPORTANT]
  > 已于2019年8月22日更新了流量分析架構。 新的架構提供來源和目的地 Ip，而不需要剖析 System.windows.frameworkelement.flowdirection 欄位，讓查詢更簡單。 </br>
  > FASchemaVersion_s 從1更新為2。 </br>
  > 已取代的欄位： VMIP_s、Subscription_s、Region_s、NSGRules_s、Subnet_s、VM_s、NIC_s、PublicIPs_s、FlowCount_d </br>
  > 新欄位： SrcPublicIPs_s、DestPublicIPs_s、NSGRule_s </br>
  > 2019年11月22日之前，已淘汰的欄位將可供使用。

流量分析是以 Log Analytics 為基礎，因此，您可以對由流量分析裝飾的資料執行自訂查詢，並在相同的上設定警示。

以下列出架構中的欄位，以及它們所代表的意義

| 欄位 | 格式 | 註解 |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | 適用于流量分析資料的資料表
| SubType_s | FlowLog | 流量記錄的子類型。 僅使用 "FlowLog"，其他 SubType_s 值適用于產品的內部運作 |
| FASchemaVersion_s |   2   | 架構版本。 不會反映 NSG 流量記錄版本 |
| TimeProcessed_t   | 日期和時間（UTC）  | 流量分析從儲存體帳戶處理原始流量記錄的時間 |
| FlowIntervalStartTime_t | 日期和時間（UTC） |  流量記錄處理間隔的開始時間。 這是測量流程間隔的時間 |
| FlowIntervalEndTime_t | 日期和時間（UTC） | 流量記錄處理間隔的結束時間 |
| FlowStartTime_t | 日期和時間（UTC） |  在「FlowIntervalStartTime_t」和「FlowIntervalEndTime_t」之間的流量記錄處理間隔中，第一次出現的流程（將會匯總）。 此流程會根據匯總邏輯進行匯總 |
| FlowEndTime_t | 日期和時間（UTC） | 在「FlowIntervalStartTime_t」和「FlowIntervalEndTime_t」之間的流量記錄處理間隔中，最後一次出現的流程（將會匯總）。 就流量記錄 v2 而言，此欄位包含已啟動相同四個元組之最後一個流程的時間（在原始流程記錄中標示為 "B"） |
| FlowType_s |  * IntraVNet <br> * Vnet 之間 <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * 未知的私用 <br> * 未知 | 資料表下方附注中的定義 |
| SrcIP_s | 來源 IP 位址 | 會在 AzurePublic 和 ExternalPublic 流程的情況下為空白 |
| DestIP_s | 目的地 IP 位址 | 會在 AzurePublic 和 ExternalPublic 流程的情況下為空白 |
| VMIP_s | VM 的 IP | 用於 AzurePublic 和 ExternalPublic 流程 |
| PublicIP_s | 公用 IP 位址 | 用於 AzurePublic 和 ExternalPublic 流程 |
| DestPort_d | 目的地連接埠 | 傳入流量的埠 |
| L4Protocol_s  | * T <br> * U  | 傳輸通訊協定。 T = TCP <br> U = UDP |
| L7Protocol_s  | 通訊協定名稱 | 衍生自目的地埠 |
| FlowDirection_s | * I = 輸入<br> * O = 輸出 | 流程流入/流出 NSG 的方向（依流量記錄） |
| FlowStatus_s  | * A = NSG 規則允許的 <br> * D = 被 NSG 規則拒絕  | 依流量記錄 NSG 允許/nblocked 的流程狀態 |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | 與流程相關聯的網路安全性群組（NSG） |
| NSGRules_s | \<Index value 0)>\|\<NSG_RULENAME>\|\<Flow Direction>\|\<Flow Status>\|\<FlowCount ProcessedByRule> |  允許或拒絕此流程的 NSG 規則 |
| NSGRule_s | NSG_RULENAME |  允許或拒絕此流程的 NSG 規則 |
| NSGRuleType_s | * 使用者定義 * 預設值 |   流程所使用的 NSG 規則類型 |
| MACAddress_s | MAC 位址 | 已在其上捕獲流程之 NIC 的 MAC 位址 |
| Subscription_s | Azure 虛擬網路/網路介面/虛擬機器的訂用帳戶已填入此欄位 | 僅適用于 FlowType = S2S、P2S、AzurePublic、ExternalPublic、MaliciousFlow 和 UnknownPrivate 流程類型（只有一個端為 azure 的流程類型） |
| Subscription1_s | 訂用帳戶識別碼 | 流程中來源 IP 所屬之虛擬網路/網路介面/虛擬機器的訂用帳戶識別碼 |
| Subscription2_s | 訂用帳戶識別碼 | 流程中目的地 IP 所屬之虛擬網路/網路介面/虛擬機器的訂用帳戶識別碼 |
| Region_s | 流程中的 IP 所屬虛擬網路/網路介面/虛擬機器的 Azure 區域 | 僅適用于 FlowType = S2S、P2S、AzurePublic、ExternalPublic、MaliciousFlow 和 UnknownPrivate 流程類型（只有一個端為 azure 的流程類型） |
| Region1_s | Azure 區域 | 流程中的來源 IP 所屬之虛擬網路/網路介面/虛擬機器的 Azure 區域 |
| Region2_s | Azure 區域 | 流程中目的地 IP 所屬之虛擬網路的 Azure 區域 |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  與傳送或接收流量的 VM 相關聯的 NIC |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 與流程中的來源 IP 相關聯的 NIC |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 與流程中目的地 IP 相關聯的 NIC |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | 與網路介面相關聯的虛擬機器 NIC_s |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | 與流程中的來源 IP 相關聯的虛擬機器 |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | 與流程中的目的地 IP 相關聯的虛擬機器 |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | 與 NIC_s 相關聯的子網 |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | 與流程中的來源 IP 相關聯的子網 |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | 與流程中目的地 IP 相關聯的子網 |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 與流程中的來源 IP 相關聯的應用程式閘道 |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 與流程中的目的地 IP 相關聯的應用程式閘道 |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 與流程中的來源 IP 相關聯的負載平衡器 |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 與流程中的目的地 IP 相關聯的負載平衡器 |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 與流程中的來源 IP 相關聯的局域網路閘道 |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 與流程中目的地 IP 相關聯的局域網路閘道 |
| ConnectionType_s | 可能的值為 VNetPeering、VpnGateway 和 ExpressRoute |    連接類型 |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | 連接名稱。 針對 flowtype P2S，此格式會格式化為 <gateway name> _<VPN Client IP> |
| ConnectingVNets_s | 以空格分隔的虛擬網路名稱清單 | 如果是中樞和輪輻拓撲，則會在此填入中樞虛擬網路 |
| Country_s | 兩個字母的國家/地區代碼（ISO 3166-1 Alpha-2） | 已填入流程類型 ExternalPublic。 PublicIPs_s 欄位中的所有 IP 位址都會共用相同的國家/地區代碼 |
| AzureRegion_s | Azure 區域位置 | 已填入流程類型 AzurePublic。 PublicIPs_s 欄位中的所有 IP 位址都會共用 Azure 區域 |
| AllowedInFlows_d | | 允許的輸入流程計數。 這代表將相同的四個元組輸入至已在其上捕獲流程之網路介面的流程數目 |
| DeniedInFlows_d |  | 已拒絕的傳入流量計數。 （輸入至已捕捉流程的網路介面） |
| AllowedOutFlows_d | | 允許的輸出流程計數（輸出至已攔截流程的網路介面） |
| DeniedOutFlows_d  | | 已拒絕的輸出流量計數（輸出到已攔截流程的網路介面） |
| FlowCount_d | 已被取代。 符合相同四個元組的流程總數。 在 ExternalPublic 和 AzurePublic 流程類型的情況下，count 會包含來自各種 PublicIP 位址的流程。
| InboundPackets_d | 在套用 NSG 規則的網路介面上接收為已捕捉的封包 | 這只會針對第2版的 NSG 流量記錄架構而填入。 |
| OutboundPackets_d  | 在套用 NSG 規則的網路介面上以捕捉方式傳送的封包 | 這只會針對第2版的 NSG 流量記錄架構而填入。 |
| InboundBytes_d |  在套用 NSG 規則的網路介面上收到的位元組已被捕獲 | 這只會針對第2版的 NSG 流量記錄架構而填入。 |
| OutboundBytes_d | 在套用 NSG 規則的網路介面上，以捕捉的形式傳送的位元組 | 這只會針對第2版的 NSG 流量記錄架構而填入。 |
| CompletedFlows_d  |  | 這只會針對 NSG 流量記錄架構的第2版填入非零值 |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 以橫條分隔的專案 |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 以橫條分隔的專案 |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 以橫條分隔的專案 |

### <a name="notes"></a>備註

1. 在 AzurePublic 和 ExternalPublic 流程的情況下，客戶擁有的 Azure VM IP 會填入 VMIP_s] 欄位中，而公用 IP 位址則會填入 [PublicIPs_s] 欄位中。 針對這兩種流程類型，我們應該使用 VMIP_s 和 PublicIPs_s，而不是 SrcIP_s 和 DestIP_s 欄位。 針對 AzurePublic 和 ExternalPublicIP 位址，我們會進一步匯總，使內嵌至客戶 log analytics 工作區的記錄數目最少。（此欄位即將淘汰，我們應該使用 SrcIP_ 和 DestIP_s，視 azure VM 是否為流程中的來源或目的地而定）
1. 流量類型的詳細資料：根據流程中涉及的 IP 位址，我們會將流程分類為下列流程類型：
1. IntraVNet –流程中的兩個 IP 位址都位於相同的 Azure 虛擬網路。
1. Vnet 之間-流程中的 IP 位址位於兩個不同的 Azure 虛擬網路中。
1. S2S –（站對站）其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址則屬於透過 VPN 閘道或 Express Route 連線到 Azure 虛擬網路的客戶網路（Site）。
1. P2S-（點對站）其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址則屬於透過 VPN 閘道連線至 Azure 虛擬網路的客戶網路（網站）。
1. AzurePublic-其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址屬於 Microsoft 所擁有的 Azure 內部公用 IP 位址。 客戶擁有的公用 IP 位址不是此流程類型的一部分。 例如，將流量傳送至 Azure 服務（儲存體端點）的任何客戶擁有的 VM，都會以此流程類型分類。
1. ExternalPublic-其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址是不在 Azure 中的公用 IP 時，在流量分析針對「FlowIntervalStartTime_t」和「FlowIntervalEndTime_t」之間的處理間隔，不會回報為在 ASC 摘要中的惡意。
1. MaliciousFlow-其中一個 IP 位址屬於 azure 虛擬網路，而另一個 IP 位址是不在 Azure 中的公用 IP，而且在 ASC 摘要中回報為惡意，而流量分析會在「FlowIntervalStartTime_t」和「FlowIntervalEndTime_t」之間處理間隔。
1. UnknownPrivate-其中一個 IP 位址屬於 Azure 虛擬網路，而另一個 IP 位址屬於私人 IP 範圍（如 RFC 1918 中所定義），而且無法由流量分析對應至客戶擁有的網站或 Azure 虛擬網路。
1. 不明–無法將流程中的其中一個 IP 位址與 Azure 中的客戶拓撲以及內部部署（網站）對應。
1. 某些功能變數名稱會附加 \_ s 或 \_ d。 這些不表示來源和目的地，但會分別表示資料類型字串和十進位數。

### <a name="next-steps"></a>後續步驟
若要取得常見問題的解答，請參閱使用[分析常見問題](traffic-analytics-faq.md)以查看功能的詳細資料，請參閱使用[分析檔](traffic-analytics.md)
