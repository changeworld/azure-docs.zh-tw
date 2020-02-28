---
title: 智慧偵測規則設定-Azure 應用程式深入解析
description: 使用 Azure Resource Manager 範本來自動管理和設定 Azure Application Insights 智慧偵測規則
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 3c028a97c2fb554b13035026025437d5331104c2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669704"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本來管理 Application Insights 智慧偵測規則

您可以使用 [Azure Resource Manager 範本](../../azure-resource-manager/templates/template-syntax.md)來管理和設定 Application Insights 中的智慧偵測規則。
在使用 Azure Resource Manager 自動化來部署新的 Application Insights 資源時，便可使用此方法，而且此方法也可用於修改現有資源的設定。

## <a name="smart-detection-rule-configuration"></a>智慧偵測規則設定

您可以設定智慧偵測規則的下列設定：
- 是否啟用規則 (預設值是 **true**)。
- 如果在找到偵測時，應該將電子郵件傳送給與訂用帳戶的[監視讀取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)和[監視參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)角色相關聯的使用者（預設值為**true**）。
- 應該在找到偵測時收到通知的任何其他電子郵件收件者。
    -  電子郵件設定不適用於標示為_預覽_的智慧偵測規則。

為了允許透過 Azure Resource Manager 設定規則設定，智慧偵測規則設定現在可作為 Application Insights 資源 (名為 **ProactiveDetectionConfigs**) 中的內部資源。
為獲得最大彈性，您可以使用唯一的通知設定來設定每個智慧偵測規則。

## 

## <a name="examples"></a>範例

以下幾個範例會說明如何使用 Azure Resource Manager 範本來設定智慧偵測規則的設定。
所有範例都參考名為 myApplication 的 Application Insights 資源以及「相依性持續時間較長智慧偵測規則」(這在內部稱為 longdependencyduration)。
請務必取代 Application Insights 資源名稱，並指定相關的智慧偵測規則內部名稱。 請查閱下表中每個智慧偵測規則對應的內部 Azure Resource Manager 名稱清單。

### <a name="disable-a-smart-detection-rule"></a>停用智慧偵測規則

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>停用為智慧偵測規則傳送電子郵件通知

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>新增智慧偵測規則的其他電子郵件收件者

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

### <a name="failure-anomalies-alert-rule"></a>失敗異常警示規則

此 Azure Resource Manager 範本示範如何設定嚴重性為2的失敗異常警示規則。 這個新版本的失敗異常警示規則是新的 Azure 警示平臺的一部分，並取代[傳統警示淘汰](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/)程式中淘汰的傳統版本。

> [!NOTE]
> 失敗異常是全域服務，因此規則位置會在全域位置上建立。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> 此 Azure Resource Manager 範本對於失敗異常警示規則而言是唯一的，而且不同于本文中所述的其他傳統智慧偵測規則。

## <a name="smart-detection-rule-names"></a>智慧偵測規則名稱

下表有智慧偵測規則在入口網站中的顯示名稱，以及其應該在 Azure Resource Manager 範本中使用的內部名稱。

> [!NOTE]
> 標示為_預覽_的智慧偵測規則不支援電子郵件通知。 因此，您只能為這些規則設定_enabled_屬性。 

| Azure 入口網站規則名稱 | 內部名稱
|:---|:---|
| 頁面載入時間緩慢 | slowpageloadtime |
| 伺服器回應時間緩慢 | slowserverresponsetime |
| 相依性持續時間較長 | longdependencyduration |
| 降低伺服器回應時間 | degradationinserverresponsetime |
| 相依性持續時間降低 | degradationindependencyduration |
| 追蹤嚴重性比率降低 (預覽) | extension_traceseveritydetector |
| 磁碟區例外狀況異常升高 (預覽) | extension_exceptionchangeextension |
| 偵測到潛在記憶體流失 (預覽) | extension_memoryleakextension |
| 偵測到潛在安全性問題 (預覽) | extension_securityextensionspackage |
| 每日資料量的異常上升（預覽） | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>後續步驟

深入了解自動偵測：

- [失敗的異常](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [記憶體流失](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [效能異常](../../azure-monitor/app/proactive-performance-diagnostics.md)
