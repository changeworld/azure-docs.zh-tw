---
title: 瞭解 Azure 監視器警示的移轉工具
description: 瞭解警報遷移工具的工作原理並排除問題。
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: d31c856e17348c23ad61130869af6ae440d3050d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114314"
---
# <a name="understand-how-the-migration-tool-works"></a>了解移轉工具的運作方式

如[前所述](monitoring-classic-retirement.md),Azure 監視器中的經典警報將停用 2019 年 8 月 31 日(最初為 2019 年 6 月 30 日)。 Azure 門戶中可以使用使用經典警報規則並希望自己觸發遷移的客戶使用遷移工具。

本文介紹了自願遷移工具的工作原理。 它還描述了一些常見問題的補救措施。

> [!NOTE]
> 由於延遲推出遷移工具,經典警報遷移的停用日期從最初宣佈的 2019 年 6 月[30 日延長至 2019 年 8 月 31](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)日。

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>不會遷移的傳統警示規則

> [!IMPORTANT]
> 活動日誌警報(包括服務運行狀況警報)和日誌警報不受遷移的影響。 遷移僅適用於[此處](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)描述的經典警報規則。

雖然該工具可以遷移幾乎所有的[經典警報規則](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform),但也有一些例外。 以下警示規則不會使用該工具(或在 2019 年 9 月開始的自動移移期間)進行遷移:

- 虛擬機器來賓指標(Windows和 Linux)的經典警報規則。 請參考本文後[新增指標警示中重新建立此類警報規則的指南](#guest-metrics-on-virtual-machines)。
- 經典存儲指標的經典警報規則。 請參考[有關監視經典儲存帳戶的指南](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)。
- 某些存儲帳戶指標的經典警報規則。 請參考本文的[詳細資訊](#storage-account-metrics)。
- 某些Cosmos DB指標的經典警報規則。 請參考本文的[詳細資訊](#cosmos-db-metrics)。
- 所有經典虛擬機和雲端服務指標(Microsoft.經典計算/虛擬機和微軟.經典計算/功能變數名稱/插槽/角色)的經典警報規則。 請參考本文的[詳細資訊](#classic-compute-metrics)。

如果您的訂閱具有任何此類經典規則,則必須手動遷移它們。 由於無法提供自動遷移,因此這些類型的任何現有經典指標警報將繼續工作到 2020 年 6 月。 此擴展為您提供了移動到新警報的時間。 您還可以繼續在上述異常上創建新的經典警報,直到 2020 年 6 月。 但是,對於其他所有內容,在 2019 年 8 月之後無法創建新的經典警報。

> [!NOTE]
> 除了上述例外情況外,如果您的經典警報規則無效,即它們位於已刪除[的棄用指標](#classic-alert-rules-on-deprecated-metrics)或資源上,則它們將不會遷移,並且在停用服務后將不可用。

### <a name="guest-metrics-on-virtual-machines"></a>虛擬機器上的來賓指標

在來賓指標上創建新指標警報之前,必須將來賓指標發送到 Azure 監視器自定義指標存儲。 依以下說明在診斷設定中開啟 Azure 監視器接收器:

- [為 Windows VM 啟用來賓指標](collect-custom-metrics-guestos-resource-manager-vm.md)
- [為 Linux VM 啟用來賓指標](collect-custom-metrics-linux-telegraf.md)

完成這些步驟后,您可以在來賓指標上創建新的指標警報。 創建新的指標警報後,可以刪除經典警報。

### <a name="storage-account-metrics"></a>儲存體帳戶計量

儲存帳戶上的所有經典警報都可以遷移,但以下指標上的警報除外:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

必須根據[新舊存儲指標之間的映射](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)遷移有關百分比指標的經典警報規則。 閾值需要適當地修改,因為可用的新指標是絕對的。

對匿名限制錯誤、SASRottling 錯誤和限制錯誤的經典警報規則必須拆分為兩個新警報,因為沒有提供相同功能的組合指標。 閾值需要適當調整。

### <a name="cosmos-db-metrics"></a>Cosmos DB 計量

除以下指標上的警報外,可以遷移 Cosmos DB 指標上的所有經典警報:

- 每秒平均要求數
- 一致性層級
- Http 2xx
- Http 3xx
- HTTP 400
- Http 401
- 內部伺服器錯誤
- 每分鐘消耗的最大 RUPM
- 每秒最大魯斯
- 蒙戈計數失敗請求
- 蒙戈刪除失敗的要求
- 蒙戈插入失敗的要求
- 蒙戈其他失敗請求
- 蒙戈其他請求費用
- 蒙戈其他請求率
- 蒙戈查詢失敗請求
- 蒙戈更新失敗請求
- 觀察到的讀取延遲
- 移至的寫入延遲
- 服務可用性
- 儲存體容量
- 限制要求
- 要求總數

每秒平均請求數、一致性級別、每分鐘消耗的最大 RUPM、每秒最大 RU、觀察到的讀取延遲、觀察到的寫入延遲、存儲容量當前在新[系統中](metrics-supported.md#microsoftdocumentdbdatabaseaccounts)不可用。

不會遷移請求指標(如 Http 2xx、H00、Http 3xx、HTTP 400、HTTP 400、HTTP 401、內部伺服器錯誤、服務可用性、限制請求和總請求)的警報,因為計算請求的方式在經典指標和新指標之間是不同的。 需要手動重新創建這些警報,並調整閾值。

Mongo 失敗請求指標上的警報必須拆分為多個警報,因為沒有提供相同功能的組合指標。 閾值需要適當調整。

### <a name="classic-compute-metrics"></a>傳統計算指標

經典計算指標上的任何警報將不會使用遷移工具遷移,因為新警報還不支援經典計算資源。 將來將添加對這些資源類型的新警報的支援。 一旦可用,客戶必須在 2020 年 6 月之前根據其經典警報規則重新創建新的等效警報規則。

### <a name="classic-alert-rules-on-deprecated-metrics"></a>棄用指標的經典警示規則

這些是以前支援但最終被棄用的指標的經典警報規則。 少數客戶可能對此類指標具有無效的經典警報規則。 由於這些警報規則無效,因此不會遷移它們。

| 資源類型| 已棄用的指標 |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_limitcompute_consumption_percent |
| Microsoft.DBforPostgreSQL/servers | compute_limitcompute_consumption_percent |
| Microsoft.Network/publicIPAddresses | 預設多觸發器率 |
| 微軟.SQL/伺服器/資料庫 | service_level_objective、storage_limit、storage_used、節流、dtu_consumption_percent、storage_used |
| 微軟.Web/託管環境/多角色池 | 平均記憶體工作集 |
| 微軟.Web/託管環境/工作池 | 接收的位元組,HTTPqueue 長度 |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>如何建立等效的新警示規則和操作群組

遷移工具將經典警報規則轉換為等效的新警報規則和操作組。 對於大多數經典警報規則,等效的新警報規則位於具有相同屬性(如和`windowSize``aggregationType`) 的相同指標上。 但是,在新系統中具有不同等效指標的指標上,有一些經典的警報規則。 以下原則適用於經典警報的遷移,除非在以下部分中指定:

- **頻率**:定義經典或新警報規則檢查條件的頻率。 `frequency`經典警報規則不可由使用者配置,所有資源類型始終為 5 分鐘,但應用程式見解元件為 1 分鐘。因此,等效規則的頻率也分別設置為 5 分鐘和 1 分鐘。
- **聚合類型**:定義指標在感興趣視窗的聚合方式。 在大多數`aggregationType`指標的經典警報和新警報之間也是如此。 在某些情況下,由於標準警報和新警報之間指標不同,因此使用等效`aggregationType`或為`primary Aggregation Type`指標 定義的指標。
- **單位**: 建立警報的指標的屬性。 某些等效指標具有不同的單位。 閾值根據需要適當調整。 例如,如果原始指標以單位為單位以秒為單位,但等效的新指標以毫秒為單位,則原始閾值乘以 1000 以確保相同的行為。
- **視窗大小**:定義聚合指標數據以與閾值進行比較的視窗。 對於標準`windowSize`值,如 5 分鐘、15 分鐘、30 分鐘、1 小時、3 小時、6 小時、12 小時、1 天等值,對等效的新警報規則沒有更改。 對於其他值,選擇最接近`windowSize`的值來使用。 對於大多數客戶,此更改沒有影響。 對於一小部分客戶,可能需要調整閾值以獲得完全相同的行為。

在以下各節中,我們將詳細介紹在新系統中具有不同等效指標的指標。 未列出經典和新的警報規則保持不變的任何指標。 您可以[在此處](metrics-supported.md)找到新系統中支援的指標清單。

### <a name="microsoftstorageaccountsservices"></a>微軟.存儲帳戶/服務

對於 Blob、表、檔和佇列等儲存帳戶服務,以下指標映射到等效指標,如下所示:

| 經典警示中的指標 | 新警示中的等效指標 | 註解|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| 具有維度為"回應類型"="授權錯誤"和"身份驗證"的交易記錄指標 ="匿名"| |
| AnonymousClientOtherError | 具有維度"回應類型"="用戶端其他錯誤"和"身份驗證"的交易記錄指標 ="匿名" | |
| 匿名客戶端逾時錯誤| 具有維度"回應類型"="用戶端時間超時"和"身份驗證"的交易記錄指標 ="匿名" | |
| AnonymousNetworkError | 具有維度"回應類型"="網络錯誤"和"身份驗證"的交易記錄指標 ="匿名" | |
| AnonymousServerOtherError | 具有維度"回應類型"="伺服器其他錯誤"和"身份驗證"的交易記錄指標 ="匿名" | |
| 匿名伺服器逾時錯誤 | 具有維度"回應類型"="伺服器超時錯誤"和"身份驗證"的交易記錄指標 ="匿名" | |
| AnonymousSuccess | 具有維度"回應類型"="成功"和"身份驗證"的交易記錄指標 ="匿名" | |
| AuthorizationError | 具有維度"回應類型"的交易記錄指標="授權錯誤" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacity | BlobCapacity | 使用`aggregationType`「平均值」而不是「最後」。 指標僅適用於 Blob 服務 |
| ClientOtherError | 具有維度"回應類型"的交易記錄指標="用戶端其他錯誤"  | |
| ClientTimeoutError | 具有維度的交易記錄指標"回應類型"="用戶端時間退出錯誤" | |
| ContainerCount | ContainerCount | 使用`aggregationType`「平均值」而不是「最後」。 指標僅適用於 Blob 服務 |
| NetworkError | 具有維度"回應類型"的交易記錄指標="網络錯誤" | |
| ObjectCount | BlobCount| 使用`aggregationType`「平均值」而不是「最後」。 指標僅適用於 Blob 服務 |
| SASAuthorizationError | 具有維度"回應類型"="授權錯誤"和"身份驗證"的交易記錄指標 ="SAS" | |
| SASClientOtherError | 具有維度"回應類型"="用戶端其他錯誤"和"身份驗證"的交易記錄指標 ="SAS" | |
| SASClient 超時錯誤 | 具有維度「回應類型」=「用戶端時間超時」和「身份驗證」和「SAS」的交易記錄指標 | |
| SASNetworkError | 具有維度"回應類型"="網络錯誤"和"身份驗證"的交易記錄指標 ="SAS" | |
| SASServerOtherError | 具有維度"回應類型"="伺服器其他錯誤"和"身份驗證"的交易記錄指標 ="SAS" | |
| SAS 伺服器逾時錯誤 | 具有維度"回應類型"="伺服器超時錯誤"和"身份驗證"的交易記錄指標 ="SAS" | |
| SASSuccess | 具有維度"回應類型"="成功"和"身份驗證"的交易記錄指標 ="SAS" | |
| ServerOtherError | 具有維度"回應類型"的交易記錄指標="伺服器其他錯誤" | |
| 伺服器逾時錯誤 | 具有維度的交易記錄指標"回應類型"="伺服器時間超時錯誤"  | |
| Success | 具有維度"回應類型"的交易記錄指標= "成功" | |
| TotalBillableRequests| 交易 | |
| TotalEgress | 輸出 | |
| TotalIngress | 輸入 | |
| TotalRequests | 交易 | |

### <a name="microsoftinsightscomponents"></a>微軟.insights/元件

對於應用程式見解,等效指標如下所示:

| 經典警示中的指標 | 新警示中的等效指標 | 註解|
|--------------------------|---------------------------------|---------|
| 可用性.可用性Metric.值 | 可用性 結果/可用性百分比|   |
| 可用性.持續時間Metric.值 | availabilityResults/duration| 將原始閾值乘以 1000,因為經典指標的單位以秒為單位,而對於新閾值,以毫秒為單位。  |
| 基本例外瀏覽器.計數 | exceptions/browser|  使用`aggregationType`「計數」而不是「總和」。 |
| 基本例外伺服器.計數 | exceptions/server| 使用`aggregationType`「計數」而不是「總和」。  |
| 用戶端效能.用戶端程序值 | browserTimings/processingDuration| 將原始閾值乘以 1000,因為經典指標的單位以秒為單位,而對於新閾值,以毫秒為單位。  |
| 用戶端效能.網路連線值 | browserTimings/networkDuration|  將原始閾值乘以 1000,因為經典指標的單位以秒為單位,而對於新閾值,以毫秒為單位。 |
| 用戶端效能.接收請求值 | browserTimings/receiveDuration| 將原始閾值乘以 1000,因為經典指標的單位以秒為單位,而對於新閾值,以毫秒為單位。  |
| 用戶端效能.傳送請求值 | browserTimings/sendDuration| 將原始閾值乘以 1000,因為經典指標的單位以秒為單位,而對於新閾值,以毫秒為單位。  |
| 用戶端效能.總計值 | browserTimings/totalDuration| 將原始閾值乘以 1000,因為經典指標的單位以秒為單位,而對於新閾值,以毫秒為單位。  |
| 效能計數器.available_bytes.值 | performanceCounters/memoryAvailableBytes|   |
| 效能計數器.io_data_bytes_per_sec.值 | performanceCounters/processIOBytesPerSecond|   |
| 效能計數器.number_of_exceps_thrown_per_sec.值 | performanceCounters/exceptionsPerSecond|   |
| 效能計數器.percentage_processor_time_normalized.值 | performanceCounters/processCpuPercentage|   |
| 效能計數器.percentage_processor_time.值 | performanceCounters/processCpuPercentage| 閾值需要適當修改,因為原始指標已跨所有內核,並且新指標已規範化為一個核心。 遷移工具不會更改閾值。  |
| 效能計數器.percentage_processor_total.值 | performanceCounters/processorCpuPercentage|   |
| 效能計數器.process_private_bytes.值 | performanceCounters/processPrivateBytes|   |
| 效能計數器.request_execution_time.值 | performanceCounters/requestExecutionTime|   |
| 效能計數器.requests_in_application_queue.值 | performanceCounters/requestsInQueue|   |
| 效能計數器.requests_per_sec.值 | performanceCounters/requestsPerSecond|   |
| 要求.持續時間 | requests/duration| 將原始閾值乘以 1000,因為經典指標的單位以秒為單位,而對於新閾值,以毫秒為單位。  |
| 要求.速率 | 要求/速率|   |
| 要求失敗.計數 | requests/failed| 使用`aggregationType`「計數」而不是「總和」。   |
| 檢視.計數 | pageViews/count| 使用`aggregationType`「計數」而不是「總和」。   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

對於 Cosmos DB,等效指標如下所示:

| 經典警示中的指標 | 新警示中的等效指標 | 註解|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| 資料大小 | DataUsage| |
| 文件計數 | DocumentCount||
| 索引大小 | IndexUsage||
| 蒙戈計數請求費用| 帶有維度「指令名稱」和「計數」的 Mongo 請求 Charge||
| 蒙戈計數請求率 | 具有維度「指令名稱」和「計數」的 Mongo 請求計數||
| 蒙戈刪除請求費用 | 帶有維度"命令名稱"的 Mongo 請求Charge ="刪除"||
| 蒙戈刪除要求率 | 具有維度「指令名稱」和「刪除」的 Mongo請求計數||
| 蒙戈插入請求費用 | 帶有維度"命令名稱"的 Mongo 請求Charge = "插入"||
| 蒙戈插入要求率 | 帶有維度「指令名稱」和「插入」的 Mongo 請求計數||
| 蒙戈查詢請求費用 | 帶有維度"命令名稱"的 Mongo 請求Charge = "查找"||
| 蒙戈查詢要求率 | 具有維度「指令名稱」和「尋找」的 Mongo 請求計數||
| 蒙戈更新請求費用 | 帶有維度"命令名稱"的 Mongo 請求Charge ="更新"||
| 服務無法使用| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>如何建立等效操作群組

經典警報規則包含與警報規則本身關聯的電子郵件、Webhook、邏輯應用和 Runbook 操作。 新的警報規則使用可在多個警報規則中重用的操作組。 遷移工具為相同的操作創建單個操作組,而不考慮使用該操作的警報規則的數量。 遷移工具創建的操作組使用命名格式"Migrated_AG*"。

> [!NOTE]
> 經典警報在用於通知經典管理員角色時,根據經典管理員的區位設置發送本地化電子郵件。 新的警報電子郵件通過行動組發送,並且僅以英語發送。

## <a name="rollout-phases"></a>首展階段

遷移工具分階段向使用經典警報規則的客戶推出。 使用該工具準備遷移訂閱時,訂閱擁有者將收到一封電子郵件。

> [!NOTE]
> 由於該工具正在分階段推出,您可能會看到某些訂閱尚未準備好在早期階段進行遷移。

大多數訂閱當前都標記為已準備好遷移。 只有具有以下資源類型經典警報的訂閱仍未準備好進行遷移。

- 微軟.經典計算/域名/插槽/角色
- 微軟.insights/元件

## <a name="who-can-trigger-the-migration"></a>誰可以觸發遷移?

在訂閱級別具有監視參與者的內建角色的任何使用者都可以觸發遷移。 具有以下權限的自訂角色的使用者也可以觸發遷移:

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- 微軟.見解/指標警報/*
- 微軟.警報管理/智慧探測器警報規則/*

> [!NOTE]
> 除了具有上述許可權外,您的訂閱還應另外在 Microsoft 註冊。 這是成功遷移應用程式見解上的故障異常警報所必需的。 

## <a name="common-problems-and-remedies"></a>常見問題和補救措施

[觸發移移](alerts-using-migration-tool.md)後,您將在提供的位址接收電子郵件,通知您遷移已完成或是否需要從您那裡獲得任何操作。 本節介紹一些常見問題以及如何處理它們。

### <a name="validation-failed"></a>驗證失敗

由於訂閱中經典警報規則的最近進行了一些更改,因此無法遷移訂閱。 此問題是暫時的。 在遷移狀態在幾天內移回 **「準備遷移」** 後,可以重新啟動遷移。

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>範圍鎖定我們移動您的規則

作為遷移的一部分,將創建新的指標警報和新的操作組,然後刪除經典警報規則。 但是,作用域鎖可能會阻止我們創建或刪除資源。 根據範圍鎖,無法遷移部分或全部規則。 您可以通過刪除[在遷移工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)中列出的訂閱、資源組或資源的範圍鎖定來解決此問題,然後再次觸發遷移。 無法禁用範圍鎖,必須在遷移過程中刪除。 [瞭解此管理作用的功能的詳細資訊](../../azure-resource-manager/management/lock-resources.md#portal)。

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>具有「拒絕」效果的政策阻止我們遷移您的規則

作為遷移的一部分,將創建新的指標警報和新的操作組,然後刪除經典警報規則。 但是,策略可能會阻止我們創建資源。 根據策略,無法遷移部分或全部規則。 阻止進程的策略列在[遷移工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)中。 通過:

- 從策略分配中排除遷移過程中的訂閱或資源組。 [詳細瞭解如何管理原則排除範圍](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion)。
- 刪除或更改效果為"審核"或"追加"(例如,可以解決與缺少標記相關的問題)。 [詳細瞭解管理原則效果](../../governance/policy/concepts/definition-structure.md#policy-rule)。

## <a name="next-steps"></a>後續步驟

- [如何使用移轉工具](alerts-using-migration-tool.md)
- [移轉準備工作](alerts-prepare-migration.md)
