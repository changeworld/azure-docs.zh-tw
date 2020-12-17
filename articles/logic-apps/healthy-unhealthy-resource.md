---
title: 設定記錄以監視 Azure 資訊安全中心中的邏輯應用程式
description: 藉由設定診斷記錄，在 Azure 資訊安全中心中監視 Logic Apps 資源的健康情況。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 7d43c128f0f587a32cc4b8673727579043f268eb
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629590"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>設定記錄以監視 Azure 資訊安全中心中的邏輯應用程式

當您在 [Microsoft Azure Security Center](../security-center/security-center-introduction.md)中監視 Logic Apps 資源時，可以 [檢查您的邏輯應用程式是否遵循預設原則](#view-logic-apps-health-status)。 Azure 會在您啟用記錄並正確設定記錄檔的目的地之後，顯示 Logic Apps 資源的健康情況狀態。 本文說明如何設定診斷記錄，並確保所有邏輯應用程式都是狀況良好的資源。

> [!TIP]
> 若要尋找 Logic Apps 服務的目前狀態，請參閱 [Azure 狀態頁面](https://status.azure.com/)，其中會列出每個可用區域中不同產品和服務的狀態。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請在開始前 [建立免費的 Azure 帳戶](https://azure.microsoft.com/free/) 。
* [已啟用診斷記錄](#enable-diagnostic-logging)的現有邏輯應用程式。
* 為您的邏輯應用程式啟用記錄所需的 Log Analytics 工作區。 如果您沒有工作區，請先 [建立您的工作區](/azure/azure-monitor/learn/quick-create-workspace)。

## <a name="enable-diagnostic-logging"></a>啟用診斷記錄

您必須先 [設定診斷記錄](monitor-logic-apps-log-analytics.md)，才可以查看邏輯應用程式的資源健康狀態。 如果您已經有 Log Analytics 工作區，您可以在建立邏輯應用程式或現有的邏輯應用程式時啟用記錄。

> [!TIP]
> 預設的建議是啟用 Logic Apps 的診斷記錄。 不過，您可以針對邏輯應用程式控制此設定。 當您為邏輯應用程式啟用診斷記錄時，您可以使用此資訊來協助分析安全性事件。

### <a name="check-diagnostic-logging-setting"></a>檢查診斷記錄設定

如果您不確定您的邏輯應用程式是否已啟用診斷記錄，您可以在 [安全性中心] 中簽入：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在搜尋列中，輸入並選取 [ **Security Center**]。
1. 在 [資訊安全中心] 儀表板功能表上，選取 **[一般**] 下方的 [ **建議**]。
1. 在安全性建議的表格中，您應該在安全性控制表中啟用 Logic Apps 中的 [ **啟用審核及記錄** &gt; **診斷記錄** ]。
1. 在 [建議] 頁面上，展開 [ **補救步驟** ] 區段，並檢查選項。 您可以選取 [**快速修正**] 來啟用 Logic Apps 診斷！ 按鈕，或遵循手動補救指示。

## <a name="view-logic-apps-health-status"></a>查看邏輯應用程式的健全狀況狀態

[啟用診斷記錄](#enable-diagnostic-logging)之後，您可以在 [安全性中心] 中看到邏輯應用程式的健全狀況狀態。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在搜尋列中，輸入並選取 [ **Security Center**]。
1. 在 [資訊安全中心] 儀表板功能表上，選取 **[一般**] 底下的 [ **清查**]。
1. 在 [清查] 頁面上，篩選您的資產清單，只顯示 Logic Apps 資源。 在 [頁面] 功能表中，選取 [ **資源類型** &gt; **邏輯應用程式**]。

   **狀況不良的資源** 計數器會顯示「安全性中心」視為狀況不良的邏輯應用程式數目。
1.  在邏輯應用程式資源的清單中，查看 [ **建議** ] 資料行。 若要檢查特定邏輯應用程式的健全狀況詳細資料，請選取資源名稱，或選取省略號按鈕 (**...**) &gt; **View 資源**。
1.  若要補救任何可能的資源健康狀態問題，請遵循您的邏輯應用程式所列的步驟。

如果已啟用診斷記錄，則可能是記錄檔的目的地有問題。 請參閱 [如何修正不同診斷記錄目的地的問題](#fix-diagnostic-logging-for-logic-apps)。

## <a name="fix-diagnostic-logging-for-logic-apps"></a>修正邏輯應用程式的診斷記錄

如果您的 [邏輯應用程式在 [安全中心] 中列為狀況不良](#view-logic-apps-health-status)，請在 Azure 入口網站中的程式碼查看中開啟邏輯應用程式，或透過 Azure CLI。 然後，檢查診斷記錄的目的地設定： [Azure Log Analytics](#log-analytics-and-event-hubs-destinations)、 [Azure 事件中樞](#log-analytics-and-event-hubs-destinations)或 [Azure 儲存體帳戶](#storage-account-destination)。

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics 和事件中樞目的地

如果您使用 Log Analytics 或事件中樞作為 Logic Apps 診斷記錄的目的地，請檢查下列設定。 

1. 若要確認您已啟用診斷記錄，請檢查 [診斷設定] `logs.enabled` 欄位是否設定為 `true` 。 
1. 若要確認您沒有將儲存體帳戶設定為目的地，請檢查 `storageAccountId` 欄位是否設定為 `false` 。

例如：

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>儲存體帳戶目的地

如果您使用儲存體帳戶做為 Logic Apps 診斷記錄的目的地，請檢查下列設定。

1. 若要確認您已啟用診斷記錄，請檢查 [診斷設定] `logs.enabled` 欄位是否設定為 `true` 。
1. 若要確認您已啟用診斷記錄的保留原則，請檢查 `retentionPolicy.enabled` 欄位是否設定為 `true` 。
1. 若要確認您設定的保留時間為0-365 天，請檢查 `retentionPolicy.days` 欄位是否設定為介於0和365之間的數位。

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```
