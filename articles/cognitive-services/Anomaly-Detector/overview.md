---
title: 什麼是異常偵測器 API？
titleSuffix: Azure Cognitive Services
description: 使用 Anomaly Detector API 的演算法，在時間序列資料上套用異常偵測。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 11/23/2020
ms.author: mbullwin
keywords: 異常偵測, 機器學習, 演算法
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 1a13b962a000e6e3b2143dc23f94b63c122bd963
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95999090"
---
# <a name="what-is-the-anomaly-detector-api"></a>什麼是異常偵測器 API？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Anomaly Detector API 可讓您監視和偵測時間序列資料中的異常狀況，而無需知道機器學習。 不論是什麼產業、案例或資料量，Anomaly Detector API 都會自動找出最適合的模型並套用到您的資料。 使用您的時間序列資料，API 可判斷異常偵測的界限、預期的值，以及哪些資料點異常。

![偵測服務要求中的模式變更](./media/anomaly_detection2.png)

使用異 Anomaly Detector 並不需要任何先前的機器學習體驗，而 RESTful API 可讓您輕鬆地將服務整合到您的應用程式和程序中。

## <a name="features"></a>特性

透過 Anomaly Detector，您可以自動偵測整個時間序列資料中的異常狀況，或在發生異常時即時偵測。

|功能  |描述  |
|---------|---------|
|即時異常偵測。 | 使用先前看到的資料點，偵測串流資料中的異常，以判斷您最新的資料點是否異常。 此業會使用您傳送的資料點來生模型，以判斷目標點是否異常。 透過您所產生的每個新資料點呼叫 API，即可在資料建立時加以監視。 |
|以批次方式偵測整個資料集的異常狀況。 | 使用時間序列來偵測您的資料中可能存在的任何異常狀況。 此作業會使用您的整個時間序列資料產生一個模型，以相同的模型分析每個資料點。         |
|以批次方式偵測整個資料集的變更點。 | 使用您的時間序列來偵測任何存在於資料中的趨勢變更點。 此作業會使用您的整個時間序列資料產生一個模型，以相同的模型分析每個資料點。    |
| 取得有關您資料的其他資訊。 | 取得您資料和任何觀察到異常狀況的詳細資料，包括預期的值、異常界限和位置。 |
| 調整異常偵測界限。 | Anomaly Detector API 會自動建立異常偵測的界限。 調整這些界限，以提高或降低 API 對於資料異常的敏感性，更能符合您的資料。 |

## <a name="demo"></a>示範

請查看此[互動式示範](https://aka.ms/adDemo)，以了解異常偵測器的運作方式。
若要執行示範，您必須建立異常偵測器資源，並取得 API 金鑰和端點。

## <a name="notebook"></a>Notebook

若要了解如何呼叫異常偵測器 API，請嘗試參考此 [Notebook](https://aka.ms/adNotebook)。 此 Jupyter Notebook 會說明如何傳送 API 要求並將結果視覺化。

若要執行 Notebook，請完成下列步驟：

1. 取得有效的 Anomaly Detector API 訂用帳戶金鑰和 API 端點。 下一節有註冊指示。
1. 登入，然後按一下右上角的 [複製]。
1. 在完成複製作業之前，請先取消核取對話方塊中的 [公用] 選項，否則您的 Notebook (包括任何訂用帳戶金鑰) 將會是公用的。
1. 按一下 [在免費 Compute 上執行]
1. 選取其中一個 Notebook。
1. 將有效的 Anomaly Detector API 訂用帳戶金鑰新增至 `subscription_key` 變數。
1. 將 `endpoint` 變數變更為您的端點。 例如： `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. 在頂端功能表列上，依序按一下 [儲存格] 和 [全部執行]。

## <a name="workflow"></a>工作流程

Anomaly Detector API 是一種 RESTful Web 服務，因此可輕易地從任何可發出 HTTP 要求及剖析 JSON 的程式設計語言呼叫。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

在註冊後：

1. 取用時間序列資料，並將它轉換成有效的 JSON 格式。 在準備您的資料時使用[最佳做法](concepts/anomaly-detection-best-practices.md)，以取得最佳的結果。
1. 將要求連同您的資料傳送給 Anomaly Detector API。
1. 剖析傳回的 JSON 訊息以處理 API 回應。

## <a name="algorithms"></a>演算法

* 如需所用演算法的相關資訊，請參閱下列技術部落格：
    * [介紹 Azure Anomaly Detector API](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Azure Anomaly Detector 中的 SR-CNN 演算法概觀](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

您可以閱讀 [Microsoft 的時間序列異常偵測服務](https://arxiv.org/abs/1906.03821)文件 (KDD 已於 2019 接受)，以深入了解由 Microsoft 開發的 SR-CNN 演算法。


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="deploy-on-premises-using-docker-containers"></a>使用 Docker 容器在內部部署環境進行部署

[使用異常偵測器容器](anomaly-detector-container-howto.md)在內部部署環境中部署 API 功能。 Docker 容器可讓服務更加契合您的資料，以實現合規性、安全性或其他操作性原因。

## <a name="join-the-anomaly-detector-community"></a>加入異常偵測器社群

* 加入 [Microsoft Teams 中 Anomaly Detector Advisor 群組](https://aka.ms/AdAdvisorsJoin)
* 請參閱所選[使用者產生的內容](user-generated-content.md)

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Anomaly Detector REST API 偵測時間序列資料中的異常狀況](quickstarts/detect-data-anomalies-csharp.md)
* Anomaly Detector API [線上示範](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Anomaly Detector [REST API 參考](https://aka.ms/anomaly-detector-rest-api-ref)
