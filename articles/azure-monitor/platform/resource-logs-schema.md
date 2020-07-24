---
title: Azure Resource 記錄支援的服務和結構描述
description: 瞭解 Azure 資源記錄的支援服務和事件架構。
ms.subservice: logs
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: 7a97afa3f960393637b8af63c56fba419f853465
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077051"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>適用于 Azure 資源記錄的通用和服務特定架構

> [!NOTE]
> 資源記錄先前稱為診斷記錄。 名稱在2019年10月已變更，因為所收集的記錄類型 Azure 監視器轉移，而不只包含 Azure 資源。 此外，您可以收集的資源記錄類別清單也會在本文中列出。 它們已移至[資源記錄類別](resource-logs-categories.md)。 

[Azure 監視器資源記錄](../../azure-monitor/platform/platform-logs-overview.md)是由 Azure 服務發出的記錄，可描述這些服務或資源的操作。 透過 Azure 監視器所提供的所有資源記錄都會共用通用的最上層架構，而且每個服務都可以彈性地為自己的事件發出唯一的屬性。

資源類型 (適用於 `resourceId` 屬性) 與 `category` 的組合可唯一識別結構描述。 本文說明資源記錄的最上層架構，以及每個服務的架構連結。


## <a name="top-level-common-schema"></a>最上層通用架構

| 名稱 | 必要/選用 | 說明 |
|---|---|---|
| time | 必要 | 事件的時間戳記 (UTC)。 |
| resourceId | 必要 | 發出事件之資源的資源識別碼。 對於租用戶服務，這是 /tenants/tenant-id/providers/provider-name 的格式。 |
| tenantId | 租用戶記錄所需的 | 此事件所繫結 Active Directory 租用戶的租用戶識別碼。 這個屬性只能用於租用戶層級記錄，並不會出現在資源層級記錄中。 |
| operationName | 必要 | 此事件所代表的作業名稱。 如果事件代表 RBAC 作業，這就是 RBAC 作業名稱（例如，Microsoft. Storage/storageAccounts/blobServices/blob/Read）。 通常以 Resource Manager 作業形式建模，即使它們不是實際記載的 Resource Manager 作業也是一樣 (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | 選擇性 | 如果 operationName 是使用 API （例如）執行，則為與作業相關聯的 api 版本 `http://myservice.windowsazure.net/object?api-version=2016-06-01` 。 如果沒有對應至此作業的 API，則版本代表該作業的版本，以防與作業建立關聯的屬性在未來變更。 |
| category | 必要 | 事件的記錄類別。 類別是您可在特定資源上啟用或停用記錄的資料粒度。 事件之屬性 Blob 內顯示的屬性，會與特定記錄類別和資源類型內的屬性相同。 一般記錄類別是「Audit」「作業」「執行」和「要求」。 |
| resultType | 選擇性 | 事件的狀態。 一般值包括︰「已啟動」、「進行中」、「成功」、「失敗」、「作用中」和「已解決」。 |
| resultSignature | 選擇性 | 事件的子狀態。 如果這項作業對應至 REST API 呼叫，此欄位就是對應 REST 呼叫的 HTTP 狀態碼。 |
| resultDescription | 選擇性 | 這項作業的靜態文字描述，例如「取得儲存體檔案」。 |
| durationMs | 選擇性 | 作業的持續時間 (以毫秒為單位)。 |
| callerIpAddress | 選擇性 | 呼叫端 IP 位址，如果作業對應至來自具有公開可用 IP 位址之實體的 API 呼叫。 |
| correlationId | 選擇性 | 用來將一組相關事件群組在一起的 GUID。 一般而言，如果兩個事件具有相同的 operationName，但有兩個不同的狀態（例如「已啟動」和「成功」），它們就會共用相同的相互關聯識別碼。 這也可能代表事件之間的其他關聯性。 |
| 身分識別 | 選擇性 | JSON Blob，描述已執行作業之使用者或應用程式的身分識別。 此欄位通常會包含來自 active directory 的授權和宣告/JWT 權杖。 |
| 層級 | 選擇性 | 事件的嚴重性層級。 必須是「資訊」、「警告」、「錯誤」或「嚴重」中的其中一個。 |
| location | 選擇性 | 發出事件之資源的區域，例如「美國東部」或「法國南部」 |
| properties | 選擇性 | 任何與此特定事件類別相關的擴充屬性。 所有自訂/唯一屬性都必須放在架構的此「部分 B」內。 |

## <a name="service-specific-schemas"></a>服務特定的架構

資源記錄的架構會根據資源和記錄類別而有所不同。 這份清單會顯示可提供可用資源記錄檔的服務，以及可使用之服務和類別特定架構的連結。 這份清單會隨著新服務的新增而變更，因此如果您沒有看到所需的內容，請使用搜尋引擎來探索其他檔。 歡迎您在這篇文章上開啟 GitHub 問題，讓我們可以更新它。

| 服務 | 結構描述與文件 |
| --- | --- |
| Azure Active Directory | [總覽](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)， [Audit 記錄架構和登](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md)[入架構](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://docs.microsoft.com/azure/analysis-services/analysis-services-logging |
| API 管理 | [API 管理資源記錄](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| 應用程式閘道 |[應用程式閘道的記錄](../../application-gateway/application-gateway-diagnostics.md) |
| Azure 自動化 |[Azure 自動化的 Log analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 記錄](../../batch/batch-diagnostics.md) |
| 適用於 MySQL 的 Azure 資料庫 | [適用於 MySQL 的 Azure 資料庫診斷記錄](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| 適用於 PostgreSQL 的 Azure 資料庫 | [適用於 PostgreSQL 的 Azure 資料庫記錄](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure 資料總管 | [Azure 資料總管記錄](/azure/data-explorer/using-diagnostic-logs) |
| 認知服務 | [Azure 認知服務的記錄](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Azure Container Registry 的記錄](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| 內容傳遞網路 | [適用于 CDN 的 Azure 記錄](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB 記錄](../../cosmos-db/monitor-cosmos-db.md) |
| Data Factory | [使用 Azure 監視器監視 Data factory](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[存取 Azure Data Lake Analytics 的記錄](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[存取 Azure Data Lake 存放區的記錄](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 事件中樞 |[Azure 事件中樞記錄](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | 無法使用結構描述。 |
| Azure 防火牆 | 無法使用結構描述。 |
| IoT 中樞 | [IoT 中樞作業](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure 金鑰保存庫記錄](../../key-vault/general/logging.md) |
| Kubernetes Service |[Azure Kubernetes 記錄](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Azure 負載平衡器的記錄檔分析](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B 自訂追蹤結構描述](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 網路安全性群組 |[網路安全性群組 (NSG) 的記錄檔分析](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS 保護 | [管理 Azure DDoS Protection Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI 專用 | [Azure 中的 Power BI Embedded 記錄](/power-bi/developer/azure-pbie-diag-logs) |
| [復原服務] | [Azure 備份的資料模型](../../backup/backup-azure-reports-data-model.md)|
| 搜尋 |[啟用和使用搜尋流量分析](../../search/search-traffic-analytics.md) |
| 服務匯流排 |[Azure 服務匯流排記錄](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database 記錄](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| 串流分析 |[作業記錄](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| 流量管理員 | [流量管理員記錄架構](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| 虛擬網路 | 無法使用結構描述。 |
| 虛擬網路閘道 | 無法使用結構描述。 |


## <a name="next-steps"></a>後續步驟

* [查看您可以收集的資源記錄類別](resource-logs-categories.md)
* [深入瞭解資源記錄](../../azure-monitor/platform/platform-logs-overview.md)
* [將資源資源記錄串流至**事件中樞**](./resource-logs.md#send-to-azure-event-hubs)
* [使用 Azure 監視器 REST API 變更資源記錄診斷設定](/rest/api/monitor/diagnosticsettings)
* [使用 Log Analytics 分析來自 Azure 儲存體的記錄](./resource-logs.md#send-to-log-analytics-workspace)
