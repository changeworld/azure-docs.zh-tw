---
title: 如何從適用於 VM 的 Azure 監視器查詢記錄
description: 適用於 VM 的 Azure 監視器解決方案會將計量和記錄資料收集到，而本文會描述記錄並包含範例查詢。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 61a71539dc034a216689eafd8991df60db96d2a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80396916"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>如何從適用於 VM 的 Azure 監視器查詢記錄

適用於 VM 的 Azure 監視器會收集效能和連線計量、電腦和處理常式清查資料，以及健全狀況狀態資訊，並將其轉送至 Azure 監視器中的 Log Analytics 工作區。  這項資料可用於 Azure 監視器中的[查詢](../../azure-monitor/log-query/log-query-overview.md)。 您可以將此資料套用至各種案例，包括移轉規劃、容量分析、探索和隨選效能疑難排解。

## <a name="map-records"></a>對應記錄

除了當處理序或電腦啟動或是上線到適用於 VM 的 Azure 監視器對應功能時所產生的記錄外，每小時還會為每個唯一的電腦和處理序產生一筆記錄。 這些記錄具有下表中的屬性。 ServiceMapComputer_CL 事件中的欄位和值對應到 ServiceMap Azure Resource Manager API 中的機器資源欄位。 ServiceMapProcess_CL 事件中的欄位和值對應到 ServiceMap Azure Resource Manager API 中的處理序資源欄位。 ResourceName_s 欄位會符合對應 Resource Manager 資源中的名稱欄位。 

有可用來識別唯一處理程序和電腦的內部產生屬性︰

- 電腦：使用 *ResourceId* 或 *ResourceName_s* 來唯一識別 Log Analytics 工作區中的電腦。
- 處理序：使用 *ResourceId* 來唯一識別 Log Analytics 工作區中的處理序。 *ResourceName_s* 在執行處理序的機器 (MachineResourceName_s) 環境中是唯一的 

因為在指定時間範圍內可以有多筆指定處理序和電腦的記錄，針對相同電腦或處理序的查詢可能會傳回多筆記錄。 若只要包含最新的記錄，請`| summarize arg_max(TimeGenerated, *) by ResourceId`將加入至查詢。

### <a name="connections-and-ports"></a>連接和埠

連接計量功能會在 Azure 監視器記錄檔中引進兩個新的資料表-VMConnection 和 VMBoundPort。 這些表格提供電腦連線的相關資訊（輸入和輸出），以及在其上開啟/作用中的伺服器埠。 ConnectionMetrics 也會透過 Api 公開，以提供在時間範圍內取得特定度量的方法。 在接聽通訊端上*接受*的 TCP 連線是輸入的，而透過連線*到指定*IP 和埠所建立的連接則是輸出。 連線的方向會透過 Direction 屬性來表示，此屬性可設為 **inbound** 或 **outbound**。 

這些資料表中的記錄是從 Dependency Agent 所報告的資料產生。 每一筆記錄都代表1分鐘時間間隔內的觀察。 TimeGenerated 屬性表示時間間隔的開始時間。 每筆記錄均包含資訊來識別個別的實體 (也就是連線或連接埠)，以及與該實體相關聯的計量。 目前只會報告透過 IPv4 使用 TCP 而發生的網路活動。 

#### <a name="common-fields-and-conventions"></a>通用欄位和慣例 

下欄欄位和慣例適用于 VMConnection 和 VMBoundPort： 

- 電腦：報告電腦的完整功能變數名稱 
- AgentId：具有 Log Analytics 代理程式之電腦的唯一識別碼  
- 電腦： ServiceMap 所公開電腦的 Azure Resource Manager 資源名稱。 其格式為*m-{guid}*，其中*Guid*是與 AgentId 相同的 guid  
- 進程： ServiceMap 所公開之進程的 Azure Resource Manager 資源名稱。 其格式為*p-{hex string}*。 進程在電腦範圍內是唯一的，而且會在電腦之間產生唯一的處理序識別碼，並結合機器和處理欄位。 
- ProcessName：報告進程的可執行檔名稱。
- 所有 IP 位址都是 IPv4 標準格式的字串，例如*13.107.3.160* 

為了管理成本和複雜度，連線記錄不代表個別的實體網路連線。 將多個實體網路連線群組為一個邏輯連線，其接著會反映於各自的資料表中。  這表示，*VMConnection* 資料表中的記錄代表一個邏輯群組，而非觀測到的個別實體連線。 在指定的一分鐘時間間隔內，共用下列屬性相同值的實體網路連線會彙總為 *VMConnection* 中的單一邏輯記錄。 

| 屬性 | 描述 |
|:--|:--|
|方向 |連線的方向，值為 *inbound* 或 *outbound* |
|電腦 |電腦 FQDN |
|Process |處理序或處理序群組的身分識別，會起始/接受連線 |
|SourceIp |來源的 IP 位址 |
|DestinationIp |目的地的 IP 位址 |
|DestinationPort |目的地的連接埠號碼 |
|通訊協定 |用於連線的通訊協定。  值為 *tcp*。 |

為了說明群組的影響，會在記錄的下列屬性中提供群組實體連線數目的相關資訊：

| 屬性 | 描述 |
|:--|:--|
|LinksEstablished |已在報告時間範圍內建立的實體網路連線數目 |
|LinksTerminated |已在報告時間範圍內終止的實體網路連線數目 |
|LinksFailed |在報告時間範圍內失敗的實體網路連線數目。 此資訊目前僅適用於輸出連線。 |
|LinksLive |已在報告時間範圍結束時開啟的實體網路連線數目|

#### <a name="metrics"></a>計量

除了連線計數計量，在指定邏輯連線或網路連接埠上傳送與接收的資料量相關資訊也會包含於記錄的下列屬性中：

| 屬性 | 描述 |
|:--|:--|
|BytesSent |已在報告時間範圍內傳送的位元組總數 |
|BytesReceived |已在報告時間範圍內接收的位元組總數 |
|回應 |已在報告時間範圍內觀測到的回應數目。 
|ResponseTimeMax |已在報告時間範圍內觀測到的最大回應時間 (毫秒)。 如果沒有值，則此屬性為空白。|
|ResponseTimeMin |已在報告時間範圍內觀測到的最小回應時間 (毫秒)。 如果沒有值，則此屬性為空白。|
|ResponseTimeSum |已在報告時間範圍內觀測到的所有回應時間總和 (毫秒)。 如果沒有值，則此屬性為空白。|

所報告的第三個資料類型是回應時間：呼叫端需要花費多久時間來等候透過連線傳送的要求，此要求會由遠端端點來處理及回應。 所報告的回應時間是基礎應用程式通訊協定的真正回應時間估計值。 它會使用啟發學習法，根據實體網路連線來源和目的端之間資料流程的觀測計算而來的。 概念上，它是要求的最後一個位元組離開傳送端的時間，以及回應的最後一個位元組傳回給它的到達時間之間的差異。 這兩個時間戳記可用來描述指定實體連線上的要求和回應事件。 它們之間的差異代表單一要求的回應時間。 

在此功能的第一個版本中，我們的演算法是一個近似值，根據針對指定網路連線所使用的實際應用程式通訊協定，成功的程度可能各有不同。 例如，目前的方法非常適用以要求-回應為基礎的通訊協定 (例如 HTTP (S))，但不適用單向或以訊息佇列為基礎的通訊協定。

此處有一些需要考慮的重要事項：

1. 如果處理序會在同一個 IP 位址但透過多個網路介面來接受連線，則將會報告每個介面的個別記錄。 
2. 使用萬用字元 IP 的記錄將不會包含任何活動。 它們會包含在其中，以代表會針對輸入流量開啟電腦上的連接埠。
3. 若要減少詳細資訊和資料量，如果有具特定 IP 位址的相符記錄 (適用於相同的處理序、連接埠和通訊協定)，將略過具有萬用字元 IP 的記錄。 略過萬用字元 IP 記錄時，具有特定 IP 位址的 IsWildcardBind 記錄屬性將設為 "True"，以代表連接埠會透過報告機器的每個介面來公開。
4. 只有在特定介面上系結的埠會將 IsWildcardBind 設定為*False*。

#### <a name="naming-and-classification"></a>命名和分類

為了方便起見，RemoteIp 屬性中會包含連線遠端的 IP 位址。 如果是輸入連線，RemoteIp 相當於 SourceIp，如果是連出連線，則它相當於 DestinationIp。 RemoteDnsCanonicalNames 屬性代表適用於 RemoteIp 的機器所報告的 DNS 標準名稱。 RemoteDnsQuestions 和 RemoteClassification 屬性均會保留，以供日後使用。 

#### <a name="geolocation"></a>地理位置

*VMConnection* 也會在記錄的下列屬性中，包含每個連線記錄遠端的地理位置資訊： 

| 屬性 | 描述 |
|:--|:--|
|RemoteCountry |主控 RemoteIp 的國家/地區名稱。  例如，*美國* |
|RemoteLatitude |地理位置緯度。 例如，*47.68* |
|RemoteLongitude |地理位置經度。 例如：*-122.12* |

#### <a name="malicious-ip"></a>惡意 IP

*VMConnection* 資料表中的每個 RemoteIp 屬性均會根據一組具有已知惡意活動的 IP 進行檢查。 如果 RemoteIp 被識別為惡意的，將在記錄的下列屬性中填入下列屬性 (如果 IP 被視為不是惡意的，則它們是空的)：

| 屬性 | 描述 |
|:--|:--|
|MaliciousIP |RemoteIp 位址 |
|IndicatorThreadType |偵測到的威脅指標是下列值之一：*殭屍網路*、*C2*、*CryptoMining*、*Darknet*、*DDos*、*MaliciousUrl*、*惡意程式碼*、*網路釣魚*、*Proxy*、*PUA*、*關注清單*。   |
|描述 |觀察到的威脅的說明。 |
|TLPLevel |號誌燈通訊協定 (TLP) 層級是已定義的值 (*白色*、*綠色*、*琥珀色*、*紅色*) 之一。 |
|信賴度 |值為 *0 – 100*。 |
|Severity |值為 *0 – 5*，其中 *5* 為最嚴重，*0* 為根本不嚴重。 預設值為 *3*。  |
|FirstReportedDateTime |提供者第一次回報指標。 |
|LastReportedDateTime |Interflow 最後一次看到指標。 |
|IsActive |使用 *True* 或 *False* 值表示指標停用。 |
|ReportReferenceLink |與指定的可預見值相關之報告的連結。 |
|AdditionalInformation |提供有關觀察到的威脅的其他資訊 (如果適用的話)。 |

### <a name="ports"></a>連接埠 

電腦上的埠會主動接受連入流量，或可能接受流量，但是在報告時間範圍內閒置，則會寫入至 VMBoundPort 資料表。  

VMBoundPort 中的每筆記錄都是由下欄欄位所識別： 

| 屬性 | 描述 |
|:--|:--|
|Process | 與埠相關聯的進程（或進程群組）的身分識別。|
|Ip | 埠 IP 位址（可以是萬用字元 IP， *0.0.0.0*） |
|連接埠 |埠號碼 |
|通訊協定 | 通訊協定。  範例： *tcp*或*udp* （目前僅支援*tcp* ）。|
 
識別埠衍生自上述五個欄位，並儲存在 PortId 屬性中。 這個屬性可用來快速尋找特定埠在一段時間內的記錄。 

#### <a name="metrics"></a>計量 

埠記錄包含代表與它們相關聯之連線的計量。 目前會報告下列計量（每個度量的詳細資料會在上一節中說明）： 

- BytesSent 和 BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

此處有一些需要考慮的重要事項：

- 如果處理序會在同一個 IP 位址但透過多個網路介面來接受連線，則將會報告每個介面的個別記錄。  
- 使用萬用字元 IP 的記錄將不會包含任何活動。 它們會包含在其中，以代表會針對輸入流量開啟電腦上的連接埠。 
- 若要減少詳細資訊和資料量，如果有具特定 IP 位址的相符記錄 (適用於相同的處理序、連接埠和通訊協定)，將略過具有萬用字元 IP 的記錄。 省略萬用字元 IP 記錄時，具有特定 IP 位址之記錄的*IsWildcardBind*屬性將會設定為*True*。  這表示埠會透過報告機器的每個介面來公開。 
- 只有在特定介面上系結的埠會將 IsWildcardBind 設定為*False*。 

### <a name="vmcomputer-records"></a>VMComputer 記錄

類型為*VMComputer*的記錄具有具有 Dependency 代理程式之伺服器的清查資料。 這些記錄具有下表中的屬性：

| 屬性 | 描述 |
|:--|:--|
|TenantId | 工作區的唯一識別碼 |
|SourceSystem | *深入資訊* | 
|TimeGenerated | 記錄的時間戳記（UTC） |
|電腦 | 電腦 FQDN | 
|AgentId | Log Analytics 代理程式的唯一識別碼 |
|電腦 | ServiceMap 所公開電腦的 Azure Resource Manager 資源名稱。 其格式為*m-{guid}*，其中*guid*與 AgentId 的 guid 相同。 | 
|DisplayName | 顯示名稱 | 
|FullDisplayName | 完整顯示名稱 | 
|HostName | 不含功能變數名稱的電腦名稱稱 |
|BootTime | 電腦開機時間（UTC） |
|TimeZone | 標準化的時區 |
|VirtualizationState | *虛擬*、程式*管理*、*實體* |
|Ipv4Addresses | IPv4 位址的陣列 | 
|Ipv4SubnetMasks | IPv4 子網路遮罩的陣列（順序與 Ipv4Addresses 相同）。 |
|Ipv4DefaultGateways | IPv4 閘道的陣列 | 
|Ipv6Addresses | IPv6 位址的陣列 | 
|MacAddresses | MAC 位址陣列 | 
|DnsNames | 與機器相關聯的 DNS 名稱陣列。 |
|DependencyAgentVersion | 在電腦上執行的相依性代理程式版本。 | 
|OperatingSystemFamily | *Linux*、 *Windows* |
|OperatingSystemFullName | 作業系統的完整名稱 | 
|PhysicalMemoryMB | 實體記憶體（以 mb 為單位） | 
|數時 | 處理器數目 | 
|CpuSpeed | CPU 速度 (MHz) | 
|VirtualMachineType | *hyperv*、 *vmware*、 *xen* |
|VirtualMachineNativeId | VM 識別碼 (由其 Hypervisor 指派) | 
|VirtualMachineNativeName | VM 的名稱 |
|VirtualMachineHypervisorId | 裝載 VM 之虛擬機器的唯一識別碼 |
|HypervisorType | *hyperv* |
|HypervisorId | 虛擬程式的唯一識別碼 | 
|HostingProvider | *azure* |
|_ResourceId | Azure 資源的唯一識別碼 |
|AzureSubscriptionId | 識別訂用帳戶的全域唯一識別碼 | 
|New-azureresourcegroup | 電腦所屬的 Azure 資源組名。 |
|AzureResourceName | Azure 資源的名稱 |
|Get-azurelocation | Azure 資源的位置 |
|AzureUpdateDomain | Azure 更新網域的名稱 |
|AzureFaultDomain | Azure 容錯網域的名稱 |
|AzureVmId | Azure 虛擬機器的唯一識別碼 |
|AzureSize | Azure VM 的大小 |
|AzureImagePublisher | Azure VM 發行者的名稱 |
|AzureImageOffering | Azure VM 供應專案類型的名稱 | 
|AzureImageSku | Azure VM 映射的 SKU | 
|AzureImageVersion | Azure VM 映射的版本 | 
|AzureCloudServiceName | Azure 雲端服務的名稱 |
|AzureCloudServiceDeployment | 雲端服務的部署識別碼 |
|AzureCloudServiceRoleName | 雲端服務角色名稱 |
|AzureCloudServiceRoleType | 雲端服務角色類型：背景*工作*或*web* |
|AzureCloudServiceInstanceId | 雲端服務角色實例識別碼 |
|AzureVmScaleSetName | 虛擬機器擴展集的名稱 |
|AzureVmScaleSetDeployment | 虛擬機器擴展集部署識別碼 |
|AzureVmScaleSetResourceId | 虛擬機器擴展集資源的唯一識別碼。|
|AzureVmScaleSetInstanceId | 虛擬機器擴展集的唯一識別碼 |
|AzureServiceFabricClusterId | Azure Service Fabric 叢集的唯一識別碼 | 
|AzureServiceFabricClusterName | Azure Service Fabric 叢集的名稱 |

### <a name="vmprocess-records"></a>VMProcess 記錄

類型為*VMProcess*的記錄具有相依性代理程式之伺服器上 TCP 連線處理的清查資料。 這些記錄具有下表中的屬性：

| 屬性 | 描述 |
|:--|:--|
|TenantId | 工作區的唯一識別碼 |
|SourceSystem | *深入資訊* | 
|TimeGenerated | 記錄的時間戳記（UTC） |
|電腦 | 電腦 FQDN | 
|AgentId | Log Analytics 代理程式的唯一識別碼 |
|電腦 | ServiceMap 所公開電腦的 Azure Resource Manager 資源名稱。 其格式為*m-{guid}*，其中*guid*與 AgentId 的 guid 相同。 | 
|Process | 服務對應進程的唯一識別碼。 其格式為*p-{GUID}*。 
|ExecutableName | 處理序可執行檔的名稱 | 
|DisplayName | 進程顯示名稱 |
|[角色] | 進程角色： *web*伺服器、 *appServer*、 *databaseServer*、 *ldapServer*、 *smbServer* |
|群組 | 進程組名。 相同群組中的進程會以邏輯方式相互關聯，例如，屬於相同產品或系統元件的一部分。 |
|StartTime | 處理序集區的開始時間 |
|FirstPid | 處理序集區中的第一個 PID |
|描述 | 處理序的描述 |
|CompanyName | 公司的名稱 |
|InternalName | 內部名稱 |
|ProductName | 產品的名稱 |
|ProductVersion | 產品的版本 |
|FileVersion | 檔案的版本 |
|ExecutablePath |可執行檔的路徑 |
|CommandLine | 命令列 |
|WorkingDirectory | 工作目錄 |
|服務 | 執行進程的服務陣列 |
|UserName | 執行處理序的帳戶 |
|UserDomain | 執行處理序的網域 |
|_ResourceId | 工作區中處理序的唯一識別碼 |


## <a name="sample-map-queries"></a>範例地圖查詢

### <a name="list-all-known-machines"></a>列出所有已知的機器

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>VM 上次啟動時

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Azure VM 依映像、位置和 SKU 區分的摘要

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>列出所有受管理電腦的實體記憶體容量

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>列出電腦名稱稱、DNS、IP 和 OS

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>在命令列中尋找具有「sql」的所有處理程序

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>以資源名稱尋找機器 (最新的記錄)

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>以 IP 位址尋找機器 (最新的記錄)

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>列出指定機器上的所有已知處理序

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>列出所有執行 SQL 伺服器的電腦

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>列出資料中心內所有唯一 curl 產品版本

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>為所有執行 CentOS 的電腦建立電腦群組

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>位元組傳送及接收趨勢

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>哪個 Azure VM 傳輸大部分的位元組

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>連結狀態趨勢

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>連線失敗趨勢

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>系結埠

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>電腦之間的開啟埠數目

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>根據已開啟的埠數目，將工作區中的處理常式評分

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>每個埠的合計列為

然後，您可以使用此查詢來根據活動對埠進行評分，例如，具有最多輸入/輸出流量的埠、具有最多連線的埠
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>摘要說明來自機器群組的輸出連線

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="performance-records"></a>效能記錄
類型為*InsightsMetrics*的記錄具有虛擬機器之客體作業系統的效能資料。 這些記錄具有下表中的屬性：


| 屬性 | 描述 |
|:--|:--|
|TenantId | 工作區的唯一識別碼 |
|SourceSystem | *深入資訊* | 
|TimeGenerated | 收集值的時間（UTC） |
|電腦 | 電腦 FQDN | 
|來源 | *vm.azm.ms* |
|命名空間 | 效能計數器的類別 | 
|Name | 效能計數器的名稱 |
|Val | 收集的值 | 
|Tags | 記錄的相關詳細資料。 請參閱下表，以瞭解用於不同記錄類型的標記。  |
|AgentId | 每部電腦代理程式的唯一識別碼 |
|類型 | *InsightsMetrics* |
|_ResourceId_ | 虛擬機器的資源識別碼 |

下表列出目前收集到*InsightsMetrics*資料表中的效能計數器：

| 命名空間 | Name | 描述 | 單位 | Tags |
|:---|:---|:---|:---|:---|
| 電腦    | Heartbeat             | 電腦的心跳                        | | |
| 記憶體      | AvailableMB           | 記憶體可用位元組數                    | 位元組          | memorySizeMB-總記憶體大小|
| 網路     | WriteBytesPerSecond   | 每秒的網路寫入位元組數            | 每秒位元組 | NetworkDeviceId-裝置的識別碼<br>位元組-傳送的位元組總數 |
| 網路     | ReadBytesPerSecond    | 網路讀取位元組/秒             | 每秒位元組 | networkDeviceId-裝置的識別碼<br>位元組-接收的位元組總數 |
| 處理器   | UtilizationPercentage | 處理器使用率百分比          | 百分比        | totalCpus-Cpu 總計 |
| LogicalDisk | WritesPerSecond       | 每秒邏輯磁片寫入數            | 每秒計數 | mountId-裝置的掛接識別碼 |
| LogicalDisk | WriteLatencyMs        | 邏輯磁片寫入延遲毫秒    | 毫秒   | mountId-裝置的掛接識別碼 |
| LogicalDisk | WriteBytesPerSecond   | 每秒邏輯磁片寫入位元組數       | 每秒位元組 | mountId-裝置的掛接識別碼 |
| LogicalDisk | TransfersPerSecond    | 每秒邏輯磁片傳輸數         | 每秒計數 | mountId-裝置的掛接識別碼 |
| LogicalDisk | TransferLatencyMs     | 邏輯磁片傳輸延遲毫秒數 | 毫秒   | mountId-裝置的掛接識別碼 |
| LogicalDisk | ReadsPerSecond        | 邏輯磁片每秒讀取次數             | 每秒計數 | mountId-裝置的掛接識別碼 |
| LogicalDisk | ReadLatencyMs         | 邏輯磁片讀取延遲毫秒     | 毫秒   | mountId-裝置的掛接識別碼 |
| LogicalDisk | ReadBytesPerSecond    | 每秒邏輯磁片讀取位元組數        | 每秒位元組 | mountId-裝置的掛接識別碼 |
| LogicalDisk | FreeSpacePercentage   | 邏輯磁片可用空間百分比        | 百分比        | mountId-裝置的掛接識別碼 |
| LogicalDisk | FreeSpaceMB           | 邏輯磁片可用空間位元組             | 位元組          | mountId-裝置的掛接識別碼<br>diskSizeMB-磁片大小總計 |
| LogicalDisk | 每秒位元組        | 邏輯磁片每秒位元組數             | 每秒位元組 | mountId-裝置的掛接識別碼 |


## <a name="next-steps"></a>後續步驟

* 如果您不熟悉在 Azure 監視器中撰寫記錄查詢，請參閱如何使用 Azure 入口網站中的[Log Analytics](../../azure-monitor/log-query/get-started-portal.md)來撰寫記錄查詢。

* 瞭解如何[撰寫搜尋查詢](../../azure-monitor/log-query/search-queries.md)。
