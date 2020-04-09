---
title: 執行 Azure 容器實體的音效
titleSuffix: Azure Cognitive Services
description: 將語音服務容器部署到 Azure 容器實例,並在 Web 瀏覽器中測試它。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 610d5ce095097a31ee92c67f0112d1657424858e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878743"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>將語音服務容器部署到 Azure 容器實體

瞭解如何將認知服務[語音服務](speech-container-howto.md)容器部署到 Azure[容器實體 。](https://docs.microsoft.com/azure/container-instances/) 此過程演示了 Azure 語音轉換服務資源的創建。 然後,我們討論拉關聯的容器映射。 最後,我們強調從瀏覽器執行兩者業務流程的能力。 使用容器可以將開發人員的注意力從管理基礎結構轉移到專注於應用程式開發上。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

您必須首先填寫並提交[認知服務語音容器請求表單](https://aka.ms/speechcontainerspreview/),以請求訪問該容器。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
