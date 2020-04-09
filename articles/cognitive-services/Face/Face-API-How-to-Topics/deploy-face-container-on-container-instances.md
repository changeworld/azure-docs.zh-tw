---
title: 在 Azure 容器實體中執行面容器
titleSuffix: Azure Cognitive Services
description: 將面容器部署到 Azure 容器實例,並在 Web 瀏覽器中測試它。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 4df5949e24e3fa59d37379c058a777c93dda2c44
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878354"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>將容器部署到 Azure 容器實體

瞭解如何將認知服務[的容器](../face-how-to-install-containers.md)部署到 Azure[容器實體 。](https://docs.microsoft.com/azure/container-instances/) 此過程演示了創建 Azure 面資源。 然後,我們討論拉關聯的容器映射。 最後,我們強調從瀏覽器執行兩者業務流程的能力。 使用容器可以將開發人員的注意力從管理基礎結構轉移到專注於應用程式開發上。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]