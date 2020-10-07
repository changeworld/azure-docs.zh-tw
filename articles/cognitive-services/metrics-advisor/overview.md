---
title: 什麼是 Metrics Advisor 服務？
titleSuffix: Azure Cognitive Services
description: Metrics Advisor 是什麼？
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 408bdd948977218d9b39a39bf97391a4141e545c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943718"
---
# <a name="what-is-metrics-advisor-preview"></a>Metrics Advisor (預覽) 是什麼？ 

Metrics Advisor 是 Azure 認知服務的一部分，使用 AI 在時間序列資料中執行資料監視和異常偵測。 服務會自動化將模型套用至資料的程序，並提供一組 Web 工作區的 API 來進行資料內嵌、異常偵測和診斷，您無需具備機器學習服務相關知識。 使用 Metrics Advisor 可：

* 分析來自多個資料來源的多維度資料 
* 識別異常並使其相互關聯
* 設定及微調資料所使用的異常偵測模型
* 診斷異常，並協助進行根本原因分析。 

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Metrics Advisor 概觀":::

## <a name="connect-to-a-variety-of-data-sources"></a>連線至各種資料來源

Metrics Advisor 可以連線到許多資料存放區，並從這些資料存放區[內嵌多維度計量](how-tos/onboard-your-data.md)資料，包括：SQL Server、Azure Blob 儲存體、MongoDB 等。 

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>容易使用且可自訂的異常偵測

* Metrics Advisor 會自動為您的資料選取最佳模型，您無需具備機器學習服務相關知識。 
* 自動監視[多維度計量](glossary.md#multi-dimensional-metric)中的每個時間序列。
* 使用[參數微調](how-tos/configure-metrics.md)和[互動式意見反應](how-tos/anomaly-feedback.md)，以自訂您的資料所套用的模型，以及未來的異常偵測結果。


## <a name="real-time-alerts-through-multiple-channels"></a>透過多個通道的即時警示

偵測到異常時，Metrics Advisor 可以使用勾點透過多個通道[傳送即時警示](how-tos/alerts.md) (例如：電子郵件勾點、Web 勾點和 Azure DevOps 勾點)。 彈性的警示規則可讓您自訂要傳送的警示和傳送位置。

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>藉由分析異常取得智慧型診斷見解

分析在多維度計量上偵測到的異常狀況，並產生[智慧型診斷深入解析](how-tos/diagnose-incident.md)，包括最可能的根本原因、診斷樹狀結構、計量深入解析等等。 藉由設定[計量圖形](how-tos/metrics-graph.md)，系統可以啟用交叉計量分析協助您將事件視覺化。


## <a name="typical-workflow"></a>一般工作流程

工作流程很簡單：在資料上線之後，您可以微調異常偵測，並建立符合您案例的組態。

1. [建立供 Metrics Advisor 使用的 Azure 資源](../cognitive-services-apis-create-account.md)。 
2. 嘗試示範網站，查看具有預先設定之範例資料的 Metrics Advisor 執行個體。 
3. 使用入口網站建置您的第一個監視。
    1. 將資料上線
    2. 異常偵測微調
    3. 訂閱警示
    4. 檢視診斷見解
1. 使用 REST API 自訂執行個體。

## <a name="next-steps"></a>後續步驟

* 試用[示範網站](quickstarts/explore-demo.md)。
* 探索快速入門：[在 Web 上監視您的第一個計量](quickstarts/web-portal.md)。
* 探索快速入門：[使用 REST API 來自訂解決方案](quickstarts/rest-api.md)。
