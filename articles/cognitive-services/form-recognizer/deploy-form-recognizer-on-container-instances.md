---
title: 在 Azure 容器實例中執行表單辨識器容器
titleSuffix: Azure Cognitive Services
description: 將表單辨識器容器部署至 Azure 容器實例，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: acba4d9fab784181fda5728c30831c8c1838b91f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80879525"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>將表單辨識器容器部署至 Azure 容器實例

瞭解如何將認知服務[表單辨識器](form-recognizer-container-howto.md)容器部署至 Azure[容器實例](https://docs.microsoft.com/azure/container-instances/)。 此程式示範如何建立 Azure 表單辨識器資源。 然後，我們會討論如何提取相關聯的容器映射。 最後，我們強調了從瀏覽器執行這兩個協調流程的能力。 使用容器可以將開發人員的注意力轉移到管理基礎結構之外，改為專注于應用程式開發。

> [!IMPORTANT]
> 表單辨識器容器目前使用1.0 版的表單辨識器 API。 您可以改為使用受控服務來存取 API 的最新版本。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

您必須先完成並提交[認知服務表單辨識器容器存取要求表單](https://aka.ms/FormRecognizerContainerRequestAccess)，以要求容器的存取權。 這麼做也會讓您登入電腦視覺。 您不需要另外註冊電腦視覺要求表單。 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
