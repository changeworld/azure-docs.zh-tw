---
title: 運行 Azure 容器實例 - 文本分析
titleSuffix: Azure Cognitive Services
description: 將文本分析容器部署到 Azure 容器實例，並在 Web 瀏覽器中測試它。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 7ae8fbd404c9c2b650e4eed30c219e8ffafe55f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383540"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>將文本分析容器部署到 Azure 容器實例

瞭解如何將認知服務[文本分析][install-and-run-containers]容器部署到 Azure[容器實例][container-instances]。 此過程舉例說明了文本分析資源的創建、關聯的情緒分析映射的創建以及從瀏覽器執行兩者此業務流程的能力。 使用容器可以將開發人員的注意力從管理基礎結構轉移到專注于應用程式開發上。

## <a name="prerequisites"></a>Prerequisites

* 使用 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[關鍵短語提取](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[語言檢測](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[情緒分析](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>後續步驟 

* 使用更多[認知服務容器](../../cognitive-services-container-support.md)
* 使用[文本分析連接服務](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances