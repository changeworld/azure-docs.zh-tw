---
title: 更新邏輯應用程式 & runbook 以進行警示遷移
description: 瞭解如何修改您的 webhook、邏輯應用程式和 runbook，以準備進行自發的遷移。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 24897826c6a70d0810b239b9b8f639787c3dac2a
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843380"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>準備邏輯應用程式和 Runbook 以便移轉傳統警示規則

> [!NOTE]
> 如[先前所宣佈](monitoring-classic-retirement.md)，Azure 監視器中的傳統警示已淘汰，但仍受限於尚未支援新警示的資源。 這些警示的停用日期已進一步擴充。 新的日期即將宣佈。
>

如果您選擇主動將傳統警示規則遷移至新的警示規則，請注意這兩個系統之間有一些差異。 本文說明這些差異，以及您可以如何為變更做準備。

## <a name="api-changes"></a>API 變更

建立和管理傳統警示規則 () 的 Api 與 `microsoft.insights/alertrules` 建立和管理新計量警示 () 的 api 不同 `microsoft.insights/metricalerts` 。 如果您目前以程式設計方式建立和管理傳統警示規則，請更新部署腳本以使用新的 Api。

下表是傳統和新警示的程式設計介面參考：

| 部署腳本類型 | 傳統警示 | 新的計量警示 |
| ---------------------- | -------------- | ----------------- |
|REST API     | [microsoft insights/alertrules](/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor alert](/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor 計量警示](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [參考](/powershell/module/az.monitor/add-azmetricalertrule)       |  [參考](/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager 範本 | [針對傳統警示](./alerts-enable-template.md)|[針對新的計量警示](./alerts-metric-create-templates.md)|

## <a name="notification-payload-changes"></a>通知承載變更

在[傳統警示規則](alerts-webhooks.md)和新的計量[警示](alerts-metric-near-real-time.md#payload-schema)之間，通知承載格式稍有不同。 如果您有傳統警示規則所觸發的任何 webhook、邏輯應用程式或 runbook 動作，則必須更新這些通知端點，以接受新計量警示的承載格式。

使用下表將 webhook 承載欄位從傳統格式對應到新的格式：

| 通知端點類型 | 傳統警示 | 新的計量警示 |
| -------------------------- | -------------- | ----------------- |
|警示是否已啟用或已解決？    | **status**       | **資料。狀態** |
|警示的相關內容資訊     | **內容**        | **資料。內容**        |
|啟用或解決警示的時間戳記     | **coNtext。時間戳記**       | **資料. 內容. 時間戳記**        |
| 警示規則識別碼 | **coNtext.id** | **data.coNtext.id** |
| 警示規則名稱 | **coNtext.name** | **data.coNtext.name** |
| 警示規則的描述 | **coNtext。描述** | **資料. 內容。描述** |
| 警示規則條件 | **coNtext。條件** | **data. coNtext。條件** |
| 度量名稱 | **metricName** | **allOf [0]. metricName** |
| 時間匯總 (在評估視窗中匯總度量的方式) | **timeAggregation** | **timeAggregation** |
| 評估期間 | **windowSize** | **資料。 windowSize** |
| 運算子 (匯總計量值如何與閾值進行比較)  | **coNtext。條件. 運算子** | **data. condition. 運算子** |
| 閾值 | **內容。條件. 閾值** | **allOf [0]. 閾值** |
| 度量值 | **metricValue** | **allOf [0]. metricValue** |
| 訂用帳戶識別碼 | **內容。 subscriptionId** | **data. subscriptionId** |
| 受影響資源的資源群組 | **resourceGroup** | **資料. resourceGroup** |
| 受影響資源的名稱 | **CoNtext.resourcename** | **data. CoNtext.resourcename** |
| 受影響資源的類型 | **內容 resourceType** | **資料。 resourceType** |
| 受影響資源的資源識別碼 | **內容。 resourceId** | **資料。 resourceId** |
| 入口網站資源摘要頁面的直接連結 | **portalLink** | **portalLink** |
| 要傳遞至 webhook 或邏輯應用程式的自訂裝載欄位 | **properties** | **資料。屬性** |

如您所見，裝載很類似。 下一節提供：

- 有關修改邏輯應用程式以使用新格式的詳細資料。
- 針對新警示剖析通知承載的 runbook 範例。

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>修改邏輯應用程式以接收計量警示通知

如果您使用具有傳統警示的邏輯應用程式，您必須修改邏輯應用程式代碼來剖析新的計量警示承載。 遵循下列步驟：

1. 建立新的邏輯應用程式。

1. 使用「Azure 監視器計量警示處理常式」範本。 此範本具有已定義適當架構的**HTTP 要求**觸發程式。

    ![邏輯-應用程式範本](media/alerts-migration/logic-app-template.png "度量警示範本")

1. 新增動作來裝載您的處理邏輯。

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>使用可接收計量警示通知的自動化 runbook

下列範例會提供要在 runbook 中使用的 PowerShell 程式碼。 此程式碼可以剖析傳統計量警示規則和新計量警示規則的裝載。

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

如需在觸發警示時停止虛擬機器之 runbook 的完整範例，請參閱[Azure 自動化檔](../../automation/automation-create-alert-triggered-runbook.md)。

## <a name="partner-integration-via-webhooks"></a>透過 webhook 的合作夥伴整合

[我們與傳統警示整合的大部分合作夥伴，都](./partners.md)已經透過其整合來支援較新的計量警示。 已使用新計量警示的已知整合如下：

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

如果您使用此處未列出的合作夥伴整合，請向整合提供者確認整合與新的計量警示搭配運作。

## <a name="next-steps"></a>後續步驟

- [如何使用移轉工具](alerts-using-migration-tool.md)
- [了解移轉工具的運作方式](alerts-understand-migration.md)
