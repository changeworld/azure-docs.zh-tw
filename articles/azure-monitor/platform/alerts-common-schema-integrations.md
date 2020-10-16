---
title: 如何整合常見的警示架構與 Logic Apps
description: 瞭解如何建立邏輯應用程式，以利用一般警示架構來處理您所有的警示。
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 1eb96248f68923da5ff5223f57fac1bffaf4ed04
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108876"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>如何整合常見的警示架構與 Logic Apps

本文說明如何建立邏輯應用程式，以利用一般警示架構來處理您所有的警示。

## <a name="overview"></a>概觀

[一般警示架構](./alerts-common-schema.md)會在所有不同的警示類型之間提供標準化且可擴充的 JSON 架構。 當以程式設計方式（透過 webhook、runbook 和邏輯應用程式）運用時，常見的警示架構最有用。 在本文中，我們會示範如何撰寫單一邏輯應用程式來處理您的所有警示。 相同的原則也可以套用至其他程式設計方法。 本文中所述的邏輯應用程式會針對「 [基本」欄位](alerts-common-schema-definitions.md#essentials)建立定義完善的變數，也會說明如何處理 [警示類型](alerts-common-schema-definitions.md#alert-context) 的特定邏輯。


## <a name="prerequisites"></a>必要條件 

本文假設讀者已熟悉 
* 設定警示規則 ([度量](./alerts-metric.md)、 [記錄](./alerts-log.md)、 [活動記錄](./alerts-activity-log.md)) 
* 設定 [動作群組](./action-groups.md)
* 從動作群組內啟用[一般警示架構](./alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>利用一般警示架構建立邏輯應用程式

1. 遵循 [概述的步驟來建立邏輯應用程式](./action-groups-logic-app.md)。 

1.  選取觸發程序：[收到 HTTP 要求時]****。

    ![邏輯應用程式觸發程序](media/action-groups-logic-app/logic-app-triggers.png "邏輯應用程式觸發程序")

1.  選取 [編輯]**** 以變更 HTTP 要求觸發程序。

    ![HTTP 要求觸發程式](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP 要求觸發程式")


1.  複製並貼上下列架構：

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

1. 選取 **+** [ **新增步驟** ]，然後選擇 [新增 **動作**]。

    ![新增動作](media/action-groups-logic-app/add-action.png "新增動作")

1. 在這個階段中，您可以根據特定的業務需求，將各種不同的連接器 (Microsoft 小組、時差、Salesforce 等 ) 。 您可以使用現成可用的「基本欄位」。 

    ![基本欄位](media/alerts-common-schema-integrations/logic-app-essential-fields.png "基本欄位")
    
    或者，您也可以使用 ' Expression ' 選項，根據警示類型撰寫條件式邏輯。

    ![邏輯應用程式運算式](media/alerts-common-schema-integrations/logic-app-expressions.png "邏輯應用程式運算式")
    
     [ [MonitoringService] 欄位](alerts-common-schema-definitions.md#alert-context) 可讓您根據可以建立條件式邏輯的方式，唯一識別警示類型。

    
    例如，下列程式碼片段會檢查警示是否為以 Application Insights 為基礎的記錄警示，如果是，則會列印搜尋結果。 否則，它會列印「NA」。

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     深入瞭解如何 [撰寫邏輯應用程式運算式](../../logic-apps/workflow-definition-language-functions-reference.md#logical-comparison-functions)。

    


## <a name="next-steps"></a>後續步驟

* [深入了解動作群組](./action-groups.md)。
* [深入瞭解常見的警示架構](./alerts-common-schema.md)。