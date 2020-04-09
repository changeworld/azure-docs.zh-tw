---
title: 在 Azure 容器實體執行表單表單
titleSuffix: Azure Cognitive Services
description: 將表單辨識器容器部署到 Azure 容器實例,並在 Web 瀏覽器中測試它。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: acba4d9fab784181fda5728c30831c8c1838b91f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879525"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>將表單辨識器容器部署到 Azure 容器實體

瞭解如何將認知服務[表單辨識器](form-recognizer-container-howto.md)容器部署到 Azure[容器實體 。](https://docs.microsoft.com/azure/container-instances/) 此過程演示了創建 Azure 窗體識別器資源。 然後,我們討論拉關聯的容器映射。 最後,我們強調從瀏覽器執行兩者業務流程的能力。 使用容器可以將開發人員的注意力從管理基礎結構轉移到專注於應用程式開發上。

> [!IMPORTANT]
> 表單辨識器容器目前使用表單辨識器 API 的版本 1.0。 您可以使用託管服務來存取最新版本的 API。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

您必須首先填寫並提交[認知服務表單識別器容器訪問請求表單](https://aka.ms/FormRecognizerContainerRequestAccess),以請求訪問容器。 這樣做也會讓您註冊電腦視覺。 您無需單獨註冊電腦視覺請求表單。 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
