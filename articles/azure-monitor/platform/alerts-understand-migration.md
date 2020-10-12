---
title: 瞭解 Azure 監視器警示的遷移
description: 瞭解警示遷移的運作方式，以及針對問題進行疑難排解。
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 52a74593fcfbdc2c1e464077e4ae460f6a5a9c39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852390"
---
# <a name="understand-migration-options-to-newer-alerts"></a>瞭解新警示的遷移選項

傳統警示已 [淘汰](./monitoring-classic-retirement.md)，但仍對尚未支援新警示的資源使用限制。 即將針對剩餘的警示遷移、 [Azure Government 雲端](../../azure-government/documentation-government-welcome.md)和 [Azure 中國的世紀](https://docs.azure.cn/)宣佈新的日期。

本文說明手動遷移和自願遷移工具的運作方式，將會用來遷移剩餘的警示規則。 此外，也會說明一些常見問題的補救方式。

> [!IMPORTANT]
> 活動記錄警示 (包括服務健康狀態警示) 和記錄警示不受遷移影響。 此遷移僅適用于 [此處](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)所述的傳統警示規則。

> [!NOTE]
> 如果您的傳統警示規則無效，亦即它們是在已被 [取代的計量](#classic-alert-rules-on-deprecated-metrics) 或已刪除的資源上，將不會進行遷移，而且在服務淘汰之後將無法使用。

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>手動將傳統警示遷移至較新的警示

有興趣手動遷移其剩餘警示的客戶，可以使用以下各節來完成。 這些區段也會定義資源提供者所淘汰的計量，且目前無法直接遷移。

### <a name="guest-metrics-on-virtual-machines"></a>虛擬機器上的來賓計量

您必須先將來賓計量傳送至 Azure 監視器自訂計量存放區，才能在來賓計量上建立新的計量警示。 請遵循下列指示，在診斷設定中啟用 Azure 監視器接收：

- [啟用 Windows Vm 的來賓計量](collect-custom-metrics-guestos-resource-manager-vm.md)
- [啟用 Linux Vm 的來賓計量](collect-custom-metrics-linux-telegraf.md)

完成這些步驟之後，您就可以在來賓計量上建立新的計量警示。 當您建立新的計量警示之後，就可以刪除傳統警示。

### <a name="storage-account-metrics"></a>儲存體帳戶計量

儲存體帳戶上的所有傳統警示都可以遷移，但這些計量的警示除外：

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

關於百分比計量的傳統警示規則，必須根據 [舊的和新儲存體計量之間的對應](../../storage/common/storage-metrics-migration.md#metrics-mapping-between-old-metrics-and-new-metrics)進行遷移。 因為可用的新計量是絕對的，所以必須適當地修改臨界值。

AnonymousThrottlingError、SASThrottlingError 和 ThrottlingError 上的傳統警示規則必須分割成兩個新的警示，因為沒有任何結合的度量可提供相同的功能。 閾值將需要適當調整。

### <a name="cosmos-db-metrics"></a>Cosmos DB 計量

除了下列計量的警示之外，您可以遷移 Cosmos DB 計量上的所有傳統警示：

- 每秒平均要求數
- 一致性層級
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- 內部伺服器錯誤
- 每分鐘耗用的 RUPM 上限
- 每秒最大 ru 數
- Mongo 計數失敗的要求數
- Mongo 刪除失敗的要求
- Mongo 插入失敗的要求
- Mongo 其他失敗的要求
- Mongo 其他要求費用
- Mongo 其他要求率
- Mongo 查詢失敗的要求
- Mongo 更新失敗的要求
- 觀察到的讀取延遲
- 觀察到的寫入延遲
- 服務可用性
- 儲存體容量
- 節流的要求
- 要求總數

每秒平均要求數、一致性層級、每分鐘耗用的最大 RUPM 數、每秒最大 ru 數、觀察到的讀取延遲、觀察到的寫入延遲、儲存容量目前無法在 [新的系統](metrics-supported.md#microsoftdocumentdbdatabaseaccounts)中使用。

要求計量（例如 Http 2xx、Http 3xx、Http 400、Http 401、內部伺服器錯誤、服務可用性、節流要求和要求總數）的警示不會遷移，因為在傳統計量和新計量之間計算要求的方式不同。 這些警示將需要以手動方式重新建立，並調整閾值。

Mongo 失敗要求度量的警示必須分割成多個警示，因為沒有任何結合的計量可提供相同的功能。 閾值將需要適當調整。

### <a name="classic-compute-metrics"></a>傳統計算計量

任何傳統計算計量的警示都不會使用遷移工具來遷移，因為新的警示尚未支援傳統計算資源。 對這些資源類型的新警示支援目前處於公開預覽狀態，客戶可以根據傳統警示規則來重新建立新的對等警示規則。

### <a name="classic-alert-rules-on-deprecated-metrics"></a>傳統警示規則已被取代的計量

這些是先前支援但最終被取代的計量的傳統警示規則。 一小部分的客戶對於這類計量可能會有不正確傳統警示規則。 因為這些警示規則無效，所以不會遷移。

| 資源類型| 已淘汰的度量 (s)  |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent，compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent，compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective、storage_limit、storage_used、節流、dtu_consumption_percent storage_used |
| Microsoft. Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft. Web/hostingEnvironments/workerpools | bytesreceived、HTTPqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>如何建立對等的新警示規則和動作群組

遷移工具會將傳統警示規則轉換為對等的新警示規則和動作群組。 針對大部分的傳統警示規則，對等的新警示規則會在具有相同屬性的相同計量上，例如 `windowSize` 和 `aggregationType` 。 不過，有一些傳統的警示規則是針對在新系統中具有不同同等標準的計量。 下列準則適用于傳統警示的遷移，除非在下一節中指定：

- **Frequency**：定義傳統或新的警示規則檢查條件的頻率。 `frequency`傳統警示規則中的無法由使用者設定，而且對於所有資源類型而言一律為5分鐘，但 Application Insights 元件為1分鐘。因此，對等規則的頻率也會分別設定為5分鐘和1分鐘。
- **匯總類型**：定義如何在感興趣的視窗上匯總度量。 `aggregationType`傳統警示和大部分計量的新警示之間的也相同。 在某些情況下，由於傳統警示和新警示之間的計量不同，因此 `aggregationType` 會使用對等的或為 `primary Aggregation Type` 度量定義。
- **單位**：用來建立警示的度量屬性。 某些相等的計量具有不同的單位。 閾值會視需要適當地調整。 例如，如果原始計量的秒數為單位，但對等的新計量以毫秒為單位，則原始閾值會乘以1000以確保相同的行為。
- **視窗大小**：定義用來匯總度量資料的視窗，以根據閾值進行比較。 針對標準 `windowSize` 值（例如5mins、15mins、先30分鐘、1hour、3hours、6小時、12小時、1天），對等的新警示規則不會進行任何變更。 若為其他值，則會選擇最接近的值 `windowSize` 。 對於大部分的客戶而言，這項變更不會有任何影響。 對於少數的客戶而言，可能需要調整臨界值以取得完全相同的行為。

在下列各節中，我們會詳細說明新系統中具有不同的相等計量的度量。 針對傳統和新警示規則保持不變的任何計量都不會列出。 您可以在 [這裡](metrics-supported.md)找到新系統所支援的計量清單。

### <a name="microsoftstorageaccountsservices"></a>StorageAccounts/服務

針對 blob、資料表、檔案和佇列等儲存體帳戶服務，下列計量會對應至對等的計量，如下所示：

| 傳統警示中的計量 | 新警示中的對等度量 | 註解|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| 維度為 ">responsetype" = "AuthorizationError" 和 "Authentication" = "Anonymous" 的交易度量| |
| AnonymousClientOtherError | 維度為 ">responsetype" = "ClientOtherError" 和 "Authentication" = "Anonymous" 的交易度量 | |
| >anonymousclienttimeouterror| 維度為 ">responsetype" = "ClientTimeOutError" 和 "Authentication" = "Anonymous" 的交易度量 | |
| AnonymousNetworkError | 維度為 ">responsetype" = "NetworkError" 和 "Authentication" = "Anonymous" 的交易度量 | |
| AnonymousServerOtherError | 維度為 ">responsetype" = "ServerOtherError" 和 "Authentication" = "Anonymous" 的交易度量 | |
| >anonymousservertimeouterror | 維度為 ">responsetype" = "ServerTimeOutError" 和 "Authentication" = "Anonymous" 的交易度量 | |
| AnonymousSuccess | 維度為 ">responsetype" = "Success" 和 "Authentication" = "Anonymous" 的交易度量 | |
| AuthorizationError | 維度為 ">responsetype" = "AuthorizationError" 的交易度量 | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacity | BlobCapacity | 使用「 `aggregationType` 平均」而非「最後」。 計量僅適用于 Blob 服務 |
| ClientOtherError | 維度為 ">responsetype" = "ClientOtherError" 的交易度量  | |
| ClientTimeoutError | 維度為 ">responsetype" = "ClientTimeOutError" 的交易度量 | |
| ContainerCount | ContainerCount | 使用「 `aggregationType` 平均」而非「最後」。 計量僅適用于 Blob 服務 |
| NetworkError | 維度為 ">responsetype" = "NetworkError" 的交易度量 | |
| ObjectCount | BlobCount| 使用「 `aggregationType` 平均」而非「最後」。 計量僅適用于 Blob 服務 |
| SASAuthorizationError | 維度為 ">responsetype" = "AuthorizationError" 和 "Authentication" = "SAS" 的交易度量 | |
| SASClientOtherError | 維度為 ">responsetype" = "ClientOtherError" 和 "Authentication" = "SAS" 的交易度量 | |
| >sasclienttimeouterror | 維度為 ">responsetype" = "ClientTimeOutError" 和 "Authentication" = "SAS" 的交易度量 | |
| SASNetworkError | 維度為 ">responsetype" = "NetworkError" 和 "Authentication" = "SAS" 的交易度量 | |
| SASServerOtherError | 維度為 ">responsetype" = "ServerOtherError" 和 "Authentication" = "SAS" 的交易度量 | |
| >sasservertimeouterror | 維度為 ">responsetype" = "ServerTimeOutError" 和 "Authentication" = "SAS" 的交易度量 | |
| SASSuccess | 維度為 ">responsetype" = "Success" 和 "Authentication" = "SAS" 的交易度量 | |
| ServerOtherError | 維度為 ">responsetype" = "ServerOtherError" 的交易度量 | |
| ServerTimeOutError | 維度為 ">responsetype" = "ServerTimeOutError" 的交易度量  | |
| 成功 | 維度為 ">responsetype" = "Success" 的交易度量 | |
| TotalBillableRequests| 交易 | |
| TotalEgress | 輸出 | |
| TotalIngress | 輸入 | |
| TotalRequests | 交易 | |

### <a name="microsoftinsightscomponents"></a>Microsoft insights/元件

針對 Application Insights，對等的計量如下所示：

| 傳統警示中的計量 | 新警示中的對等度量 | 註解|
|--------------------------|---------------------------------|---------|
| availabilityMetric。值 | availabilityResults/availabilityPercentage|   |
| Durationmetric.value。值 | availabilityResults/duration| 將原始閾值乘以1000作為傳統計量的單位，以秒為單位，而新的閾值則以毫秒為單位。  |
| basicExceptionBrowser 計數 | exceptions/browser|  請使用 `aggregationType` ' count ' 而非 ' sum '。 |
| basicExceptionServer 計數 | exceptions/server| 請使用 `aggregationType` ' count ' 而非 ' sum '。  |
| clientPerformance. clientProcess. value | browserTimings/processingDuration| 將原始閾值乘以1000作為傳統計量的單位，以秒為單位，而新的閾值則以毫秒為單位。  |
| clientPerformance. networkConnection. value | browserTimings/networkDuration|  將原始閾值乘以1000作為傳統計量的單位，以秒為單位，而新的閾值則以毫秒為單位。 |
| clientPerformance. receiveRequest. value | browserTimings/receiveDuration| 將原始閾值乘以1000作為傳統計量的單位，以秒為單位，而新的閾值則以毫秒為單位。  |
| clientPerformance. sendRequest. value | browserTimings/sendDuration| 將原始閾值乘以1000作為傳統計量的單位，以秒為單位，而新的閾值則以毫秒為單位。  |
| clientPerformance 總計值 | browserTimings/totalDuration| 將原始閾值乘以1000作為傳統計量的單位，以秒為單位，而新的閾值則以毫秒為單位。  |
| performanceCounter.available_bytes。值 | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec。值 | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec。值 | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized。值 | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time。值 | performanceCounters/processCpuPercentage| 閾值需要經過適當修改，因為原始計量是在所有核心上，而新的計量則正規化為一個核心。 遷移工具不會變更臨界值。  |
| performanceCounter.percentage_processor_total。值 | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes。值 | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time。值 | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue。值 | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec。值 | performanceCounters/requestsPerSecond|   |
| 要求。持續時間 | requests/duration| 將原始閾值乘以1000作為傳統計量的單位，以秒為單位，而新的閾值則以毫秒為單位。  |
| 要求。費率 | 要求/速率|   |
| requestFailed 計數 | requests/failed| 請使用 `aggregationType` ' count ' 而非 ' sum '。   |
| view. count | pageViews/count| 請使用 `aggregationType` ' count ' 而非 ' sum '。   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

針對 Cosmos DB，對等的計量如下所示：

| 傳統警示中的計量 | 新警示中的對等度量 | 註解|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| 資料大小 | DataUsage| |
| 文件計數 | DocumentCount||
| 索引大小 | IndexUsage||
| Mongo 計數要求費用| 具有 dimension "CommandName" = "count" 的 MongoRequestCharge||
| Mongo 計數要求速率 | 具有 dimension "CommandName" = "count" 的 MongoRequestsCount||
| Mongo 刪除要求費用 | 具有 dimension "CommandName" = "delete" 的 MongoRequestCharge||
| Mongo 刪除要求率 | 具有 dimension "CommandName" = "delete" 的 MongoRequestsCount||
| Mongo 插入要求費用 | 具有 dimension "CommandName" = "insert" 的 MongoRequestCharge||
| Mongo 插入要求率 | 具有 dimension "CommandName" = "insert" 的 MongoRequestsCount||
| Mongo 查詢要求費用 | 具有 dimension "CommandName" = "find" 的 MongoRequestCharge||
| Mongo 查詢要求率 | 具有 dimension "CommandName" = "find" 的 MongoRequestsCount||
| Mongo 更新要求費用 | 具有 dimension "CommandName" = "update" 的 MongoRequestCharge||
| 服務無法使用| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>如何建立對等的動作群組

傳統警示規則具有與警示規則本身系結的電子郵件、webhook、邏輯應用程式和 runbook 動作。 新的警示規則會使用可跨多個警示規則重複使用的動作群組。 無論有多少個警示規則使用動作，遷移工具都會針對相同的動作建立單一動作群組。 由遷移工具所建立的動作群組會使用命名格式 ' Migrated_AG * '。

> [!NOTE]
> 傳統警示會在用來通知傳統系統管理員角色時，根據傳統系統管理員的地區設定傳送當地語系化的電子郵件。 新的警示電子郵件是透過動作群組傳送，且僅限英文。

## <a name="rollout-phases"></a>首展階段

使用傳統警示規則的客戶階段會推出遷移工具。 訂用帳戶擁有者可以使用工具來遷移訂用帳戶時，訂用帳戶擁有者會收到一封電子郵件。

> [!NOTE]
> 因為此工具會在階段推出，您可能會看到部分訂用帳戶尚未準備好在早期階段進行遷移。

大部分的訂用帳戶目前已標示為準備好進行遷移。 只有在下列資源類型上具有傳統警示的訂用帳戶仍未準備好進行遷移。

- Microsoft.classiccompute/domainNames/插槽/角色
- Microsoft insights/元件

## <a name="who-can-trigger-the-migration"></a>誰可以觸發遷移？

任何在訂用帳戶層級具有監視參與者內建角色的使用者，都可以觸發遷移。 具有下列許可權的自訂角色的使用者也可以觸發遷移：

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> 除了具有上述許可權之外，您的訂用帳戶還應該向 Microsoft.alertsmanagement 資源提供者註冊。 這是在 Application Insights 上成功遷移失敗異常警示的必要項。 

## <a name="common-problems-and-remedies"></a>常見問題和解決辦法

[觸發遷移](alerts-using-migration-tool.md)之後，您將會在所提供的位址收到電子郵件，通知您已完成遷移，或您需要任何動作。 本節說明一些常見問題，以及如何處理這些問題。

### <a name="validation-failed"></a>驗證失敗

由於您的訂用帳戶中的傳統警示規則有一些最近的變更，因此無法遷移訂用帳戶。 這個問題是暫時性的。 您可以在幾天後，將遷移狀態移回 **以供遷移** 。

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>範圍鎖定讓我們無法遷移您的規則

在遷移過程中，將會建立新的計量警示和新的動作群組，然後將會刪除傳統警示規則。 不過，範圍鎖定可以防止我們建立或刪除資源。 根據範圍鎖定，無法遷移部分或所有規則。 若要解決此問題，您可以移除訂用帳戶、資源群組或資源的範圍鎖定（列在 [遷移工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)中），然後再次觸發遷移。 無法停用範圍鎖定，而且必須在遷移程式期間移除。 [深入瞭解如何管理範圍鎖定](../../azure-resource-manager/management/lock-resources.md#portal)。

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>具有「拒絕」效果的原則，讓我們無法遷移您的規則

在遷移過程中，將會建立新的計量警示和新的動作群組，然後將會刪除傳統警示規則。 不過，原則可能會讓我們無法建立資源。 根據原則而定，部分或所有規則無法遷移。 封鎖進程的原則會列在 [遷移工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)中。 解決此問題的方法是：

- 將訂用帳戶或資源群組從原則指派中排除在遷移程式的持續時間內。 [深入瞭解如何管理原則排除範圍](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion)。
- 例如，移除或變更 [audit] 或 [append] (的效果，可解決與遺漏標記) 相關的問題。 [深入瞭解如何管理原則效果](../../governance/policy/concepts/definition-structure.md#policy-rule)。

## <a name="next-steps"></a>後續步驟

- [如何使用移轉工具](alerts-using-migration-tool.md)
- [準備移轉](alerts-prepare-migration.md)
