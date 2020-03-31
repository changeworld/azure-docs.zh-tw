---
title: 通過更新邏輯應用和 Runbook 為 Azure 監視器經典警報遷移做好準備
description: 瞭解如何修改 Webhook、邏輯應用和 Runbook，以便為自願遷移做好準備。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 9219e105acb98424939030af76b526d475585619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665587"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>準備邏輯應用程式和 Runbook 以便移轉傳統警示規則

正如[先前宣佈的](monitoring-classic-retirement.md)，Azure 監視器中的經典警報將于 2019 年 9 月停用（最初為 2019 年 7 月）。 Azure 門戶中可以使用使用經典警報規則並希望自己觸發遷移的客戶使用遷移工具。

> [!NOTE]
> 由於延遲推出遷移工具，經典警報遷移的停用日期從最初宣佈的 2019 年 6 月 30 日延長至 2019 年 8 月 31 日。

如果您選擇自願將經典警報規則遷移到新的警報規則，請注意兩個系統之間存在一些差異。 本文介紹了這些差異以及如何為更改做好準備。

## <a name="api-changes"></a>API 變更

創建和管理經典警報規則的 API （`microsoft.insights/alertrules`） 不同于創建和管理新指標警報 （ 的 API 。`microsoft.insights/metricalerts` 如果現在以程式設計方式創建和管理經典警報規則，請更新部署腳本以使用新的 API。

下表是經典警報和新警報的程式設計介面的引用：

|         |傳統警示  |新指標警報 |
|---------|---------|---------|
|REST API     | [微軟.insights/警報規則](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [微軟.insights/指標警報](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az 監視器警報](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az 監視器指標警報](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [參考](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [參考](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager 範本 | [對於經典警報](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[對於新指標警報](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>通知有效負載更改

通知有效負載格式在[經典警報規則](alerts-webhooks.md)和新[指標警報](alerts-metric-near-real-time.md#payload-schema)之間略有不同。 如果您有由經典警報規則引發的任何 Webhook、邏輯應用或 Runbook 操作，則必須更新這些通知終結點以接受新指標警報的有效負載格式。

使用下表將 Webhook 有效負載欄位從經典格式映射到新格式：

|  |傳統警示  |新指標警報 |
|---------|---------|---------|
|警報是否啟動或已解決？    | **狀態**       | **資料.狀態** |
|有關警報的上下文資訊     | **內容**        | **資料.上下文**        |
|啟動或解決警報的時間戳記     | **上下文.時間戳記**       | **資料.上下文.時間戳記**        |
| 警報規則 ID | **coNtext.id** | **data.coNtext.id** |
| 警示規則名稱 | **coNtext.name** | **data.coNtext.name** |
| 警報規則的說明 | **上下文描述** | **資料.上下文.描述** |
| 警報規則條件 | **上下文.條件** | **資料.上下文.條件** |
| 度量名稱 | **上下文.條件.metricName** | **資料.上下文.條件.allof\0_metricname** |
| 時間聚合（指標在評估視窗上的聚合方式）| **上下文.條件.時間聚合** | **上下文.條件.時間聚合** |
| 評估期 | **上下文.條件.視窗大小** | **資料.上下文.條件.視窗大小** |
| 運算子（如何比較聚合指標值與閾值） | **上下文.條件.運算子** | **資料.上下文.條件.運算子** |
| 閾值 | **上下文.條件.閾值** | **資料.上下文.條件.allof{0}"閾值** |
| 指標值 | **上下文.條件.metricValue** | **資料.上下文.條件.allof\0_metricValue** |
| 訂用帳戶識別碼 | **上下文.訂閱 Id** | **資料.上下文.訂閱 Id** |
| 受影響資源的資源組 | **上下文.資源組** | **資料.上下文.資源組** |
| 受影響資源的名稱 | **上下文.資源名稱** | **資料.上下文.資源名稱** |
| 受影響資源的類型 | **上下文.資源類型** | **資料.上下文.資源類型** |
| 受影響資源的資源識別碼 | **上下文.資源 Id** | **資料.上下文.資源 Id** |
| 直接連結到門戶資源摘要頁面 | **上下文.門戶連結** | **資料.上下文.門戶連結** |
| 要傳遞給 Webhook 或邏輯應用的自訂有效負載欄位 | **性能** | **資料.屬性** |

如您所見，有效負載類似。 以下部分提供：

- 有關修改邏輯應用以使用新格式的詳細資訊。
- 分析新警報的通知負載的 Runbook 示例。

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>修改邏輯應用以接收指標警報通知

如果使用具有經典警報的邏輯應用，則必須修改邏輯應用代碼以分析新的指標警報負載。 請遵循下列步驟：

1. 創建新的邏輯應用。

1. 使用範本"Azure 監視器 - 指標警報處理常式"。 此範本具有**HTTP 要求**觸發器，定義了相應的架構。

    ![邏輯應用範本](media/alerts-migration/logic-app-template.png "指標警報範本")

1. 添加操作以承載處理邏輯。

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>使用接收指標警報通知的自動化運行簿

下面的示例提供 PowerShell 代碼，用於 runbook。 此代碼可以分析經典指標警報規則和新的指標警報規則的有效負載。

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

有關觸發警報時停止虛擬機器的運行手冊的完整示例，請參閱[Azure 自動化文檔](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)。

## <a name="partner-integration-via-webhooks"></a>通過網路鉤子進行合作夥伴集成

我們大多數[與經典警報集成的合作夥伴](https://docs.microsoft.com/azure/azure-monitor/platform/partners)已經通過其集成支援較新的指標警報。 已用於新指標警報的已知集成包括：

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

如果您使用的是此處未列出的合作夥伴集成，請與集成供應商確認該集成適用于新的指標警報。

## <a name="next-steps"></a>後續步驟

- [如何使用移轉工具](alerts-using-migration-tool.md)
- [了解移轉工具的運作方式](alerts-understand-migration.md)
