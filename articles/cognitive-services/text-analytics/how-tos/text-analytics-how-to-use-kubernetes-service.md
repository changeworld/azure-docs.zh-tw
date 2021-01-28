---
title: 執行 Azure Kubernetes Service-文字分析
titleSuffix: Azure Cognitive Services
description: 將文字分析容器映射部署至 Azure Kubernetes Service，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fb33bd4fe70c51e1e2afffece10ba3b29dcb0450
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932429"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>將文字分析容器部署到 Azure Kubernetes Service

瞭解如何將 Azure 認知服務 [文字分析](./text-analytics-how-to-install-containers.md) 容器映射部署至 AZURE KUBERNETES SERVICE (AKS) 。 此程式示範如何建立文字分析資源、如何建立相關聯的情感分析影像，以及如何在瀏覽器中執行這兩個的協調流程。 使用容器可讓您的注意力遠離管理基礎結構，而改為專注于應用程式開發。

## <a name="prerequisites"></a>先決條件

此程序需要必須安裝並在本機執行的多個工具。 請勿使用 Azure Cloud Shell。 您需要下列項目：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services)。
* 文字編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/download)。
* 安裝的 [Azure CLI](/cli/azure/install-azure-cli) 。
* 已安裝 [KUBERNETES CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 。
* 具有正確定價層的 Azure 資源。 並非所有的定價層都會使用這個容器︰
    * 只有 F0 或標準定價層的 **Azure 文字分析** 資源。
    * 具有 S0 定價層的 **Azure 認知服務** 資源。

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

**_

## <a name="next-steps"></a>後續步驟

_ 使用更多 [認知服務容器](../../cognitive-services-container-support.md)
* 使用 [文字分析聯機服務](../index.yml)
