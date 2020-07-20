---
title: 瞭解 Azure 監視器警示的遷移工具
description: 瞭解警示遷移工具的運作方式和疑難排解問題。
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: d31c856e17348c23ad61130869af6ae440d3050d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81114314"
---
# <a name="understand-how-the-migration-tool-works"></a>了解移轉工具的運作方式

如[先前所宣佈](monitoring-classic-retirement.md)，Azure 監視器中的傳統警示將于2019年8月31日淘汰（原本為2019年6月30日）。 Azure 入口網站提供遷移工具給使用傳統警示規則的客戶，以及想要自行觸發遷移的使用者。

本文說明自發遷移工具的運作方式。 它也會說明一些常見問題的解決辦法。

> [!NOTE]
> 由於遷移工具的推出延遲，傳統警示遷移的淘汰日期已從2019年6月30日最初宣佈的日期[延長到2019年8月31日](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)。

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>不會遷移的傳統警示規則

> [!IMPORTANT]
> 活動記錄警示（包括服務健康情況警示）和記錄警示不會受到遷移的影響。 遷移僅適用于[這裡](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)所述的傳統警示規則。

雖然此工具幾乎可以遷移所有[傳統的警示規則](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)，但還是有一些例外狀況。 下列警示規則不會使用工具（或從2019年9月開始自動遷移期間）進行遷移：

- 虛擬機器來賓計量（Windows 和 Linux）的傳統警示規則。 請參閱本文稍後的在新的計量[警示中重新建立這類警示規則的指引](#guest-metrics-on-virtual-machines)。
- 傳統的儲存體計量的傳統警示規則。 請參閱[監視傳統儲存體帳戶的指導](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)方針。
- 某些儲存體帳戶計量的傳統警示規則。 請參閱本文稍後的[詳細資料](#storage-account-metrics)。
- 某些 Cosmos DB 計量的傳統警示規則。 請參閱本文稍後的[詳細資料](#cosmos-db-metrics)。
- 傳統的警示規則會用於所有傳統虛擬機器和雲端服務計量（Microsoft.classiccompute/virtualMachines 和 Microsoft.classiccompute/domainNames/個位置/角色）。 請參閱本文稍後的[詳細資料](#classic-compute-metrics)。

如果您的訂用帳戶有任何這類傳統規則，您必須手動進行遷移。 因為我們無法提供自動遷移，所以這些類型的任何現有、傳統計量警示都將繼續工作，直到2020年6月為止。 此延伸模組可讓您將時間移至新的警示。 您也可以在2020年6月之前，繼續針對上述例外狀況建立新的傳統警示。 不過，針對其他所有專案，2019年8月之後就無法建立新的傳統警示。

> [!NOTE]
> 除了上述的例外狀況之外，如果您的傳統警示規則無效，亦即其已被[取代的計量](#classic-alert-rules-on-deprecated-metrics)或已刪除的資源，則不會進行遷移，而且在服務淘汰之後將無法使用。

### <a name="guest-metrics-on-virtual-machines"></a>虛擬機器上的來賓計量

您必須先將來賓計量傳送至 Azure 監視器的自訂計量存放區，才可以建立新的計量警示。 請依照下列指示，在 [診斷設定] 中啟用 Azure 監視器接收：

- [啟用 Windows Vm 的來賓計量](collect-custom-metrics-guestos-resource-manager-vm.md)
- [啟用 Linux Vm 的來賓計量](collect-custom-metrics-linux-telegraf.md)

完成這些步驟之後，您就可以建立新的計量警示。 當您建立新的計量警示之後，您可以刪除傳統警示。

### <a name="storage-account-metrics"></a>儲存體帳戶計量

除了這些計量的警示以外，您可以遷移儲存體帳戶上的所有傳統警示：

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

百分比計量的傳統警示規則必須根據[新舊儲存體計量之間的對應](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)進行遷移。 臨界值必須適當地修改，因為可用的新計量是絕對的。

AnonymousThrottlingError、SASThrottlingError 和 ThrottlingError 上的傳統警示規則必須分割成兩個新的警示，因為沒有提供相同功能的結合計量。 閾值將必須適當地調整。

### <a name="cosmos-db-metrics"></a>Cosmos DB 計量

除了這些計量的警示以外，您可以遷移 Cosmos DB 計量的所有傳統警示：

- 每秒平均要求數
- 一致性層級
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- 內部伺服器錯誤
- 每分鐘耗用的 RUPM 上限
- 每秒最大 ru
- Mongo 計數失敗的要求數
- Mongo 刪除失敗的要求
- Mongo 插入失敗的要求
- Mongo 其他失敗的要求
- Mongo 其他要求費用
- Mongo 其他要求速率
- Mongo 查詢失敗的要求
- Mongo 更新失敗的要求
- 觀察到的讀取延遲
- 觀察到的寫入延遲
- 服務可用性
- 儲存體容量
- 節流的要求
- 要求總數

平均每秒要求數、一致性層級、每分鐘取用的最大 RUPM、每秒最大 ru、觀察到的讀取延遲、觀察到的寫入延遲、儲存容量目前無法在[新系統](metrics-supported.md#microsoftdocumentdbdatabaseaccounts)中使用。

要求計量（例如 Http 2xx、Http 3xx、Http 400、Http 401、內部伺服器錯誤、服務可用性、節流要求和總要求數）的警示不會遷移，因為傳統計量和新計量之間的要求計數方式不同。 您必須手動重新建立這些警示，並調整臨界值。

Mongo 失敗要求的警示必須分割成多個警示，因為沒有提供相同功能的結合計量。 閾值將必須適當地調整。

### <a name="classic-compute-metrics"></a>傳統計算計量

傳統計算計量上的任何警示都不會使用遷移工具來遷移，因為新的警示尚未支援傳統計算資源。 未來將會新增對這些資源類型的新警示的支援。 一旦可供使用之後，客戶必須根據其傳統警示規則，在2020年6月前重新建立新的對等警示規則。

### <a name="classic-alert-rules-on-deprecated-metrics"></a>已淘汰計量的傳統警示規則

這些是先前支援但最終已淘汰之計量的傳統警示規則。 一小部分的客戶可能會對這類計量具有不正確傳統警示規則。 由於這些警示規則無效，因此不會進行遷移。

| 資源類型| 已淘汰的度量 |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent，compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent，compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective、storage_limit、storage_used、節流、dtu_consumption_percent、storage_used |
| Microsoft Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft Web/hostingEnvironments/workerpools | bytesreceived、HTTPqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>如何建立對等的新警示規則和動作群組

遷移工具會將您的傳統警示規則轉換為對等的新警示規則和動作群組。 對於大部分傳統的警示規則而言，對等的新警示規則會在相同的計量上具有相同的屬性，例如 `windowSize` 和 `aggregationType` 。 不過，有一些傳統警示規則是針對在新系統中具有不同、對等計量的度量。 除非在下一節中指定，否則下列原則適用于傳統警示的遷移：

- **頻率**：定義傳統或新的警示規則檢查條件的頻率。 `frequency`傳統警示規則中的不是由使用者設定，而且對於其為1分鐘的 Application Insights 元件除外，所有資源類型的一律為5分鐘。因此，對等規則的頻率也會分別設定為5分鐘和1分鐘。
- **匯總類型**：定義度量在感對的時間範圍內的匯總方式。 在 `aggregationType` 傳統警示和大部分計量的新警示之間，也是相同的。 在某些情況下，由於傳統警示和新警示之間的計量不同，因此 `aggregationType` `primary Aggregation Type` 會使用對等的或針對度量所定義的。
- **單位**：要在其上建立警示之度量的屬性。 某些對等的計量有不同的單位。 臨界值會視需要適當地調整。 例如，如果原始計量的秒數為單位，但對等的新計量以毫秒為單位，則原始閾值會乘以1000，以確保相同的行為。
- **視窗大小**：定義用於匯總計量資料以與臨界值比較的視窗。 針對標準 `windowSize` 值（例如5分鐘、15mins、30mins、1hour、3hours、6小時、12小時、1天），對等的新警示規則不會進行任何變更。 若為其他值，則 `windowSize` 會選擇要使用的最接近。 對於大部分的客戶而言，這種變更不會有任何影響。 對於少數的客戶而言，可能需要調整閾值，才能取得完全相同的行為。

在下列各節中，我們會詳細說明在新系統中具有不同、對等度量的計量。 傳統和新警示規則維持不變的任何計量都不會列出。 您可以在[這裡](metrics-supported.md)找到新系統中支援的計量清單。

### <a name="microsoftstorageaccountsservices"></a>StorageAccounts/服務

對於儲存體帳戶服務（例如 blob、資料表、檔案和佇列），下列計量會對應至對等的計量，如下所示：

| 傳統警示中的計量 | 新警示中的對等度量 | 註解|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| 維度為 "ResponseType" = "AuthorizationError" 和 "Authentication" = "Anonymous" 的交易度量| |
| AnonymousClientOtherError | 維度為 "ResponseType" = "ClientOtherError" 和 "Authentication" = "Anonymous" 的交易度量 | |
| AnonymousClientTimeOutError| 維度為 "ResponseType" = "ClientTimeOutError" 和 "Authentication" = "Anonymous" 的交易度量 | |
| AnonymousNetworkError | 維度為 "ResponseType" = "NetworkError" 和 "Authentication" = "Anonymous" 的交易度量 | |
| AnonymousServerOtherError | 維度為 "ResponseType" = "ServerOtherError" 和 "Authentication" = "Anonymous" 的交易度量 | |
| AnonymousServerTimeOutError | 維度為 "ResponseType" = "ServerTimeOutError" 和 "Authentication" = "Anonymous" 的交易度量 | |
| AnonymousSuccess | 維度為 "ResponseType" = "Success" 且 "Authentication" = "Anonymous" 的交易度量 | |
| AuthorizationError | 維度為 "ResponseType" = "AuthorizationError" 的交易度量 | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacity | BlobCapacity | 使用 `aggregationType` ' average '，而不是 ' last '。 度量僅適用于 Blob 服務 |
| ClientOtherError | 維度為 "ResponseType" = "ClientOtherError" 的交易度量  | |
| ClientTimeoutError | 維度為 "ResponseType" = "ClientTimeOutError" 的交易度量 | |
| ContainerCount | ContainerCount | 使用 `aggregationType` ' average '，而不是 ' last '。 度量僅適用于 Blob 服務 |
| NetworkError | 維度為 "ResponseType" = "NetworkError" 的交易度量 | |
| ObjectCount | BlobCount| 使用 `aggregationType` ' average '，而不是 ' last '。 度量僅適用于 Blob 服務 |
| SASAuthorizationError | 維度為 "ResponseType" = "AuthorizationError" 和 "Authentication" = "SAS" 的交易度量 | |
| SASClientOtherError | 維度為 "ResponseType" = "ClientOtherError" 和 "Authentication" = "SAS" 的交易度量 | |
| SASClientTimeOutError | 維度為 "ResponseType" = "ClientTimeOutError" 和 "Authentication" = "SAS" 的交易度量 | |
| SASNetworkError | 維度為 "ResponseType" = "NetworkError" 和 "Authentication" = "SAS" 的交易度量 | |
| SASServerOtherError | 維度為 "ResponseType" = "ServerOtherError" 和 "Authentication" = "SAS" 的交易度量 | |
| SASServerTimeOutError | 維度為 "ResponseType" = "ServerTimeOutError" 和 "Authentication" = "SAS" 的交易度量 | |
| SASSuccess | 維度為 "ResponseType" = "Success" 和 "Authentication" = "SAS" 的交易度量 | |
| ServerOtherError | 維度為 "ResponseType" = "ServerOtherError" 的交易度量 | |
| ServerTimeOutError | 維度為 "ResponseType" = "ServerTimeOutError" 的交易度量  | |
| 成功 | 維度為 "ResponseType" = "Success" 的交易度量 | |
| TotalBillableRequests| 交易 | |
| TotalEgress | 輸出 | |
| TotalIngress | 輸入 | |
| TotalRequests | 交易 | |

### <a name="microsoftinsightscomponents"></a>Microsoft insights/元件

針對 Application Insights，對等的計量如下所示：

| 傳統警示中的計量 | 新警示中的對等度量 | 註解|
|--------------------------|---------------------------------|---------|
| availabilityMetric。值 | availabilityResults/availabilityPercentage|   |
| Durationmetric.value。值 | availabilityResults/duration| 將原始閾值乘以1000，表示傳統計量的單位為秒，而新的計量則以毫秒為單位。  |
| basicExceptionBrowser。計數 | exceptions/browser|  請使用 `aggregationType` ' count '，而非 ' sum '。 |
| basicExceptionServer。計數 | exceptions/server| 請使用 `aggregationType` ' count '，而非 ' sum '。  |
| clientPerformance. clientProcess. value | browserTimings/processingDuration| 將原始閾值乘以1000，表示傳統計量的單位為秒，而新的計量則以毫秒為單位。  |
| clientPerformance. networkConnection. value | browserTimings/networkDuration|  將原始閾值乘以1000，表示傳統計量的單位為秒，而新的計量則以毫秒為單位。 |
| clientPerformance. receiveRequest. value | browserTimings/receiveDuration| 將原始閾值乘以1000，表示傳統計量的單位為秒，而新的計量則以毫秒為單位。  |
| clientPerformance. sendRequest. value | browserTimings/sendDuration| 將原始閾值乘以1000，表示傳統計量的單位為秒，而新的計量則以毫秒為單位。  |
| clientPerformance。值總計 | browserTimings/totalDuration| 將原始閾值乘以1000，表示傳統計量的單位為秒，而新的計量則以毫秒為單位。  |
| performanceCounter. available_bytes 值 | performanceCounters/memoryAvailableBytes|   |
| performanceCounter. io_data_bytes_per_sec 值 | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter. number_of_exceps_thrown_per_sec 值 | performanceCounters/exceptionsPerSecond|   |
| performanceCounter. percentage_processor_time_normalized 值 | performanceCounters/processCpuPercentage|   |
| performanceCounter. percentage_processor_time 值 | performanceCounters/processCpuPercentage| 臨界值必須適當地修改，因為原始計量是在所有核心之間，而新計量則正規化為一個核心。 遷移工具不會變更臨界值。  |
| performanceCounter. percentage_processor_total 值 | performanceCounters/processorCpuPercentage|   |
| performanceCounter. process_private_bytes 值 | performanceCounters/processPrivateBytes|   |
| performanceCounter. request_execution_time 值 | performanceCounters/requestExecutionTime|   |
| performanceCounter. requests_in_application_queue 值 | performanceCounters/requestsInQueue|   |
| performanceCounter. requests_per_sec 值 | performanceCounters/requestsPerSecond|   |
| 要求。持續時間 | requests/duration| 將原始閾值乘以1000，表示傳統計量的單位為秒，而新的計量則以毫秒為單位。  |
| 要求。速率 | 要求/速率|   |
| requestFailed。計數 | requests/failed| 請使用 `aggregationType` ' count '，而非 ' sum '。   |
| view. count | pageViews/count| 請使用 `aggregationType` ' count '，而非 ' sum '。   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

針對 Cosmos DB，對等的計量如下所示：

| 傳統警示中的計量 | 新警示中的對等度量 | 註解|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| 資料大小 | DataUsage| |
| 文件計數 | DocumentCount||
| 索引大小 | IndexUsage||
| Mongo 計數要求費用| 具有維度 "CommandName" = "count" 的 MongoRequestCharge||
| Mongo 計數要求速率 | 具有維度 "CommandName" = "count" 的 MongoRequestsCount||
| Mongo 刪除要求費用 | 具有維度 "CommandName" = "delete" 的 MongoRequestCharge||
| Mongo 刪除要求率 | 具有維度 "CommandName" = "delete" 的 MongoRequestsCount||
| Mongo 插入要求費用 | 具有維度 "CommandName" = "insert" 的 MongoRequestCharge||
| Mongo 插入要求率 | 具有維度 "CommandName" = "insert" 的 MongoRequestsCount||
| Mongo 查詢要求費用 | 具有維度 "CommandName" = "find" 的 MongoRequestCharge||
| Mongo 查詢要求率 | 具有維度 "CommandName" = "find" 的 MongoRequestsCount||
| Mongo 更新要求費用 | 具有維度 "CommandName" = "update" 的 MongoRequestCharge||
| 服務無法使用| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>如何建立對等的動作群組

傳統警示規則具有與警示規則本身相關的電子郵件、webhook、邏輯應用程式和 runbook 動作。 新的警示規則會使用可跨多個警示規則重複使用的動作群組。 不論使用動作的警示規則有多少，遷移工具都會針對相同的動作建立單一動作群組。 遷移工具所建立的動作群組會使用命名格式 ' Migrated_AG * '。

> [!NOTE]
> 傳統警示會根據傳統系統管理員的地區設定，在用來通知傳統系統管理員角色時，傳送當地語系化的電子郵件。 新的警示電子郵件是透過動作群組傳送，而且僅以英文撰寫。

## <a name="rollout-phases"></a>首展階段

遷移工具的推出階段是使用傳統警示規則的客戶。 當訂閱準備好使用工具進行遷移時，訂用帳戶擁有者將會收到一封電子郵件。

> [!NOTE]
> 由於此工具會在階段中推出，您可能會發現某些訂閱尚未準備好在早期階段進行遷移。

大部分的訂用帳戶目前標示為準備好進行遷移。 只有在下列資源類型上具有傳統警示的訂用帳戶仍未準備好進行遷移。

- Microsoft.classiccompute/domainNames/位置/角色
- Microsoft insights/元件

## <a name="who-can-trigger-the-migration"></a>誰可以觸發遷移？

在訂用帳戶層級上具有監視參與者的內建角色的任何使用者，都可以觸發遷移。 具有下列許可權的自訂角色的使用者也可以觸發遷移：

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> 除了擁有上述許可權之外，您的訂用帳戶還應該向 Microsoft.alertsmanagement 資源提供者註冊。 這是在 Application Insights 上成功遷移失敗異常警示的必要參數。 

## <a name="common-problems-and-remedies"></a>常見問題與解決辦法

[觸發遷移](alerts-using-migration-tool.md)之後，您會收到電子郵件，網址為您所提供的位址，通知您遷移已完成，或是您需要採取任何動作。 本節說明一些常見問題，以及如何處理這些問題。

### <a name="validation-failed"></a>驗證失敗

因為您訂用帳戶中的傳統警示規則有一些最近的變更，所以無法遷移訂用帳戶。 這個問題是暫時性的。 在數天后，您可以在遷移狀態恢復**準備進行遷移**之後重新開機遷移。

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>範圍鎖定導致我們無法遷移您的規則

在遷移過程中，將會建立新的計量警示和新的動作群組，然後將會刪除傳統警示規則。 不過，範圍鎖定可能會讓我們無法建立或刪除資源。 根據範圍鎖定，部分或所有規則無法遷移。 若要解決這個問題，您可以移除訂用帳戶、資源群組或資源的範圍鎖定，這會列在[遷移工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)中，然後再次觸發遷移。 範圍鎖定無法停用，而且必須在遷移程式期間移除。 [深入瞭解管理範圍鎖定](../../azure-resource-manager/management/lock-resources.md#portal)。

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>具有「拒絕」效果的原則導致我們無法遷移您的規則

在遷移過程中，將會建立新的計量警示和新的動作群組，然後將會刪除傳統警示規則。 不過，原則可能會讓我們無法建立資源。 根據原則，部分或所有規則無法遷移。 封鎖進程的原則會列在 [[遷移] 工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)中。 解決此問題的方法是：

- 從原則指派中排除訂用帳戶或資源群組，以進行遷移程式的持續時間。 [深入瞭解管理原則排除範圍](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion)。
- 移除或變更「audit」或「append」的效果（例如，可以解決與遺漏標記相關的問題）。 [深入瞭解管理原則效果](../../governance/policy/concepts/definition-structure.md#policy-rule)。

## <a name="next-steps"></a>後續步驟

- [如何使用移轉工具](alerts-using-migration-tool.md)
- [準備移轉](alerts-prepare-migration.md)
