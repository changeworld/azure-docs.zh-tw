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
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: de81fcb5ee62f9b295c93132b271507c040af46a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86512749"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>將表單辨識器容器部署至 Azure 容器實例

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

瞭解如何將認知服務 [表單辨識器](form-recognizer-container-howto.md) 容器部署至 Azure [容器實例](https://docs.microsoft.com/azure/container-instances/)。 此程式示範如何建立 Azure 表單辨識器資源。 然後我們會討論如何提取相關聯的容器映射。 最後，我們強調了從瀏覽器執行兩者的協調流程的能力。 使用容器可以將開發人員的注意力轉移到管理基礎結構之外，改為專注于應用程式開發。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
