---
title: 更新邏輯應用&警報遷移的 Runbook
description: 瞭解如何修改 Webhook、邏輯應用和 Runbook,以便為自願遷移做好準備。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: f31fcc07bed0287c2f86ca4fe52bf02a2a1d2a71
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114414"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>準備邏輯應用程式和 Runbook 以便移轉傳統警示規則

正如[先前宣佈的](monitoring-classic-retirement.md),Azure 監視器中的經典警報將於 2019 年 9 月停用(最初為 2019 年 7 月)。 Azure 門戶中可以使用使用經典警報規則並希望自己觸發遷移的客戶使用遷移工具。

> [!NOTE]
> 由於延遲推出遷移工具,經典警報遷移的停用日期從最初宣佈的 2019 年 6 月 30 日延長至 2019 年 8 月 31 日。

如果您選擇自願將經典警報規則遷移到新的警報規則,請注意兩個系統之間存在一些差異。 本文介紹了這些差異以及如何為更改做好準備。

## <a name="api-changes"></a>API 變更

建立與管理經典警示規則的 API`microsoft.insights/alertrules`( ) 不同於建立與管理新指標警報 (`microsoft.insights/metricalerts` API 。 如果現在以程式設計方式創建和管理經典警報規則,請更新部署文本以使用新的 API。

下表是經典警報和新警報的程式設計介面的引用:

|         |傳統警示  |新指標警報 |
|---------|---------|---------|
|REST API     | [微軟.insights/警報規則](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [微軟.insights/指標警報](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az 監視器警示](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az 監視器指標警示](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [參考](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [參考](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager 範本 | [對於經典警報](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[對於新指標警報](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>通知有效負載變更

通知有效負載格式在[經典警報規則](alerts-webhooks.md)和新[指標警報](alerts-metric-near-real-time.md#payload-schema)之間略有不同。 如果您有由經典警報規則觸發的任何 Webhook、邏輯應用或 Runbook 操作,則必須更新這些通知終結點以接受新指標警報的有效負載格式。

使用表格將 Webhook 有效負載位數段從傳統格式映射到新格式:

|  |傳統警示  |新指標警報 |
|---------|---------|---------|
|警報是否啟動或已解決?    | **狀態**       | **資料.狀態** |
|有關警示的內容資訊     | **內容**        | **資料.內容**        |
|啟動或解決警示的時間戳     | **內容標記**       | **資料.內容**        |
| 警示規則識別碼 | **context.id** | **data.context.id** |
| 警示規則名稱 | **context.name** | **data.context.name** |
| 警示規則的說明 | **內容描述** | **資料.內容名稱描述** |
| 警示規則條件 | **內容** | **資料.內容** |
| 度量名稱 | **內容.metricName** | **資料.上下文.條件.allof\0_metricname** |
| 時間集合(指標在評估視窗上的集合模式)| **內容彙總** | **內容彙總** |
| 評估期 | **內容.視窗大小** | **資料.上下文.條件.視窗大小** |
| 運算子(如何比較聚合指標值與閾值) | **內容.運算子** | **資料.內容.運算子** |
| 閾值 | **上下文.條件.閾值** | **數據.上下文.條件.allof{0}"閾值** |
| 指標值 | **內容.metricValue** | **資料.上下文.條件.allof\0_metricValue** |
| 訂用帳戶識別碼 | **內容內文.訂閱 Id** | **資料.內容內文.訂閱 Id** |
| 受影響資源的資源群組 | **內容組別組** | **資料.內容組** |
| 受影響資源的名稱 | **內容名稱** | **資料.內容名稱** |
| 受影響資源的類型 | **內容類型** | **資料.內容類型** |
| 受影響資源的資源識別碼 | **內容Id** | **資料.內容內容Id** |
| 直接連結到門戶資源摘要頁面 | **上下文.門戶連結** | **資料.上下文.門戶連結** |
| 要傳遞給 Webhook 或邏輯應用的自訂有效負載欄位 | **效能** | **資料.屬性** |

如您所見,有效負載類似。 以下部分提供:

- 有關修改邏輯應用以使用新格式的詳細資訊。
- 分析新警報的通知負載的 Runbook 範例。

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>修改邏輯應用以接收指標警報通知

如果使用具有經典警報的邏輯應用,則必須修改邏輯應用代碼以分析新的指標警報負載。 請遵循下列步驟：

1. 創建新的邏輯應用。

1. 使用範本「Azure 監視器 - 指標警報處理程式」。。 此範本具有**HTTP 請求**觸發器,定義了相應的架構。

    ![邏輯應用範本](media/alerts-migration/logic-app-template.png "指標警示範本")

1. 添加操作以承載處理邏輯。

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>使用接收指標警示通知的自動化執行簿

下面的範例提供 PowerShell 代碼,用於 runbook。 此代碼可以分析經典指標警報規則和新的指標警報規則的有效負載。

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

有關觸發警報時停止虛擬機器的執行手冊的完整範例,請參閱[Azure 自動化文件](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)。

## <a name="partner-integration-via-webhooks"></a>透過網路鉤子進行合作夥伴整合

我們大多數[與經典警報集成的合作夥伴](https://docs.microsoft.com/azure/azure-monitor/platform/partners)已經通過其整合支援較新的指標警報。 已用於新指標警報的已知整合的包括:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

如果您使用的是此處未列出的合作夥伴整合,請與整合提供者確認該整合適用於新的指標警報。

## <a name="next-steps"></a>後續步驟

- [如何使用移轉工具](alerts-using-migration-tool.md)
- [了解移轉工具的運作方式](alerts-understand-migration.md)
