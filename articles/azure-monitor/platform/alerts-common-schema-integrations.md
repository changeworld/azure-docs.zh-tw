---
title: 如何將通用警報架構與邏輯應用集成
description: 瞭解如何創建利用通用警報架構來處理所有警報的邏輯應用。
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 9042ed8ddbb698192e638fa7538f74561574c262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668225"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>如何將通用警報架構與邏輯應用集成

本文介紹如何創建利用通用警報架構來處理所有警報的邏輯應用。

## <a name="overview"></a>總覽

[通用警報架構](https://aka.ms/commonAlertSchemaDocs)在所有不同的警報類型中提供標準化且可擴展的 JSON 架構。 當以程式設計方式利用時，通用警報架構最有用- 通過 Webhook、Runbook 和邏輯應用。 在本文中，我們將演示如何創作單個邏輯應用來處理所有警報。 相同的原則可以應用於其他程式設計方法。 本文中描述的邏輯應用為["基本"欄位](alerts-common-schema-definitions.md#essentials)創建定義良好的變數，並介紹了如何處理[警報類型](alerts-common-schema-definitions.md#alert-context)特定邏輯。


## <a name="prerequisites"></a>Prerequisites 

本文假定讀者熟悉 
* 設置警報規則（[指標](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric)、[日誌](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)、[活動日誌](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)）
* 設置[操作組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* 從操作組內啟用[通用警報架構](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema)

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>利用通用警報架構創建邏輯應用

1. 按照[概述的步驟創建邏輯應用](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app)。 

1.  選取觸發程序：[收到 HTTP 要求時]****。

    ![邏輯應用程式觸發程序](media/action-groups-logic-app/logic-app-triggers.png "邏輯應用程式觸發程序")

1.  選取 [編輯]**** 以變更 HTTP 要求觸發程序。

    ![HTTP 要求觸發器](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP 要求觸發器")


1.  複製並粘貼以下架構：

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. 選擇**+****"新建步驟**"，然後選擇 **"添加操作**"。

    ![新增動作](media/action-groups-logic-app/add-action.png "新增動作")

1. 在此階段，您可以根據您的特定業務需求添加各種連接器（Microsoft 團隊、鬆弛、銷售隊伍等）。 您可以使用開箱即用的"基本欄位"。 

    ![基本欄位](media/alerts-common-schema-integrations/logic-app-essential-fields.png "基本欄位")
    
    或者，您可以使用"運算式"選項基於警報類型編寫條件邏輯。

    ![邏輯應用運算式](media/alerts-common-schema-integrations/logic-app-expressions.png "邏輯應用運算式")
    
     ["監視服務"欄位](alerts-common-schema-definitions.md#alert-context)允許您唯一標識警報類型，並基於該類型可以創建條件邏輯。

    
    例如，以下程式碼片段檢查警報是否基於應用程式見解的日誌警報，以及是否列印搜尋結果。 否則，它會列印"NA"。

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     瞭解有關[編寫邏輯應用運算式的更多詳細資訊](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions)。

    


## <a name="next-steps"></a>後續步驟

* [瞭解有關操作組 的更多。](../../azure-monitor/platform/action-groups.md)
* [瞭解有關常見警報架構的更多。](https://aka.ms/commonAlertSchemaDocs)

