---
title: 執行 Azure 函式庫伯奈斯服務 - 文字分析
titleSuffix: Azure Cognitive Services
description: 將文字分析容器映射部署到 Azure 庫伯奈斯服務,並在 Web 瀏覽器中測試它。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 155f32ee76f69fe0f16e7698123381fdc12efd0e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877803"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>將文字分析容器部署到 Azure 庫伯奈斯服務

瞭解如何將 Azure 認知服務[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)容器映射部署到 Azure 庫伯奈斯服務 (AKS)。 此過程演示如何創建文本分析資源、如何創建關聯的情緒分析圖像以及如何從瀏覽器執行這兩個業務流程。 使用容器可以將注意力從管理基礎結構轉移到專注於應用程式開發上。

## <a name="prerequisites"></a>Prerequisites

此程序需要必須安裝並在本機執行的多個工具。 不要使用 Azure 雲外殼。 您需要下列項目：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* 文字編輯器,例如[,視覺化工作室代碼](https://code.visualstudio.com/download)。
* 已安裝[Azure CLI。](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [庫伯內斯 CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)已安裝。
* 具有正確定價層的 Azure 資源。 並非所有的定價層都會使用這個容器︰
    * **Azure 文本分析**資源,僅具有 F0 或標準定價層。
    * 具有 S0 定價層的**Azure 認知服務**資源。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[關鍵片語擷取](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[語言偵測](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[情感分析](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>後續步驟

* 使用更多[認知服務容器](../../cognitive-services-container-support.md)
* 使用[文字分析連線服務](../vs-text-connected-service.md)
