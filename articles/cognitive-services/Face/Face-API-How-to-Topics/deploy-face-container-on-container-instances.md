---
title: 在 Azure 容器實例中執行臉部容器
titleSuffix: Azure Cognitive Services
description: 將臉部容器部署至 Azure 容器實例，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 5fbe316580cfa2ca280a2587536df037146e8d02
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538117"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>將臉部容器部署至 Azure 容器實例

> [!IMPORTANT]
> 已達到臉部容器使用者的限制。 我們目前不接受臉部容器的新應用程式。

瞭解如何將認知服務[臉部](../face-how-to-install-containers.md)容器部署至 Azure[容器實例](https://docs.microsoft.com/azure/container-instances/)。 此程式示範如何建立 Azure 臉部資源。 然後，我們會討論如何提取相關聯的容器映射。 最後，我們強調了從瀏覽器執行這兩個協調流程的能力。 使用容器可以將開發人員的注意力轉移到管理基礎結構之外，改為專注于應用程式開發。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]