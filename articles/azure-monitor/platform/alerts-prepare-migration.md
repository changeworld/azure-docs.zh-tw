---
title: '& runbook 更新邏輯應用程式以進行警示遷移'
description: 瞭解如何修改您的 webhook、邏輯應用程式和 runbook，以準備進行自發的遷移。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 60a721af9acf980e88ad60504e75d2488c8a4d81
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944479"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>準備邏輯應用程式和 Runbook 以便移轉傳統警示規則

> [!NOTE]
> 如 [先前所宣佈](monitoring-classic-retirement.md)，Azure 監視器中的傳統警示已淘汰，但仍對尚未支援新警示的資源提供有限的使用。 這些警示的停用日期已進一步延伸。 新的日期即將宣佈。
>

如果您選擇主動將傳統警示規則遷移至新的警示規則，請注意這兩個系統之間有一些差異。 本文說明這些差異，以及您可以如何準備變更。

## <a name="api-changes"></a>API 變更

建立及管理傳統警示規則 () 的 Api 與 `microsoft.insights/alertrules` 建立和管理新的計量警示 () 的 api 不同 `microsoft.insights/metricalerts` 。 如果您目前以程式設計方式建立和管理傳統警示規則，請更新您的部署腳本以使用新的 Api。

下表是傳統和新警示的程式設計介面參考：

| 部署腳本類型 | 傳統警示 | 新增度量警示 |
| ---------------------- | -------------- | ----------------- |
|REST API     | [microsoft insights/alertrules](/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor alert](/cli/azure/monitor/alert)        | [az 監視器計量警示](/cli/azure/monitor/metrics/alert)        |
|PowerShell      | [參考](/powershell/module/az.monitor/add-azmetricalertrule)       |  [參考](/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager 範本 | [傳統警示](./alerts-enable-template.md)|[針對新的計量警示](./alerts-metric-create-templates.md)|

## <a name="notification-payload-changes"></a>通知承載變更

[傳統警示規則](alerts-webhooks.md)和新的計量[警示](alerts-metric-near-real-time.md#payload-schema)之間的通知承載格式稍有不同。 如果您有由傳統警示規則引發的任何 webhook、邏輯應用程式或 runbook 動作，您必須更新這些通知端點，以接受新計量警示的承載格式。

使用下表，將 webhook 裝載欄位從傳統格式對應至新的格式：

| 通知端點類型 | 傳統警示 | 新增度量警示 |
| -------------------------- | -------------- | ----------------- |
|警示已啟用或已解決？    | **status**       | **資料。狀態** |
|警示的內容相關資訊     | **內容**        | **資料。內容**        |
|啟用或解決警示的時間戳記     | **coNtext. timestamp**       | **data. coNtext. timestamp**        |
| 警示規則識別碼 | **coNtext.id** | **data.coNtext.id** |
| 警示規則名稱 | **coNtext.name** | **data.coNtext.name** |
| 警示規則的描述 | **內容。描述** | **data. coNtext. description** |
| 警示規則條件 | **coNtext。 condition** | **data. coNtext. condition** |
| 度量名稱 | **metricName** | **>allof [0]. metricName** |
| 時間匯總 (度量在評估視窗上的匯總方式) | **timeAggregation** | **timeAggregation** |
| 評估期 | **windowSize** | **windowSize。** |
| 操作員 (如何將匯總的計量值與閾值進行比較)  | **condition. operator** | **data. condition. operator** |
| 臨界值 | **coNtext。條件。閾值** | **>allof [0]. 閾值** |
| 度量值 | **metricValue** | **>allof [0]. metricValue** |
| 訂用帳戶識別碼 | **subscriptionId** | **資料。 subscriptionId** |
| 受影響資源的資源群組 | **內容 resourceGroup** | **資料. 內容 resourceGroup** |
| 受影響資源的名稱 | **CoNtext.resourcename** | **data. CoNtext.resourcename** |
| 受影響資源的類型 | **內容 resourceType** | **data. coNtext** |
| 受影響資源的資源識別碼 | **內容 resourceId** | **資料。 resourceId** |
| 入口網站資源摘要頁面的直接連結 | **portalLink** | **portalLink** |
| 要傳遞至 webhook 或邏輯應用程式的自訂裝載欄位 | **properties** | **資料。屬性** |

如您所見，承載很類似。 下一節提供：

- 有關修改邏輯應用程式以使用新格式的詳細資料。
- 針對新警示剖析通知承載的 runbook 範例。

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>修改邏輯應用程式以接收度量警示通知

如果您要使用邏輯應用程式搭配傳統警示，您必須修改邏輯應用程式程式碼，以剖析新的計量警示承載。 請遵循下列步驟：

1. 建立新的邏輯應用程式。

1. 使用「Azure 監視器計量警示處理常式」範本。 此範本具有已定義適當架構的 **HTTP 要求** 觸發程式。

    ![螢幕擷取畫面顯示兩個按鈕：空白邏輯應用程式和 Azure 監視器–計量警示處理常式。](media/alerts-migration/logic-app-template.png "計量警示範本")

1. 新增動作來裝載您的處理邏輯。

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>使用接收計量警示通知的自動化 runbook

下列範例會提供要在 runbook 中使用的 PowerShell 程式碼。 此程式碼可以剖析傳統計量警示規則和新計量警示規則的承載。

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

如需觸發警示時，停止虛擬機器之 runbook 的完整範例，請參閱 [Azure 自動化檔](../../automation/automation-create-alert-triggered-runbook.md)。

## <a name="partner-integration-via-webhooks"></a>透過 webhook 的合作夥伴整合

大部分 [與傳統警示整合的合作夥伴，都](./partners.md) 已透過其整合支援較新的計量警示。 已使用新計量警示的已知整合如下：

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

如果您使用此處未列出的合作夥伴整合，請向整合提供者確認整合適用于新的計量警示。

## <a name="next-steps"></a>後續步驟

- [如何使用移轉工具](alerts-using-migration-tool.md)
- [了解移轉工具的運作方式](alerts-understand-migration.md)
