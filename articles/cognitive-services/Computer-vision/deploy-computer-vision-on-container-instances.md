---
title: 在 Azure 容器實體執行電腦視覺容器
titleSuffix: Azure Cognitive Services
description: 將電腦視覺容器部署到 Azure 容器實例,並在 Web 瀏覽器中測試它。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: ecb0b6a6577161a5349d9d701dd6c116f69411f0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876066"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>將電腦視覺容器部署到 Azure 容器實體

瞭解如何將認知服務[電腦視覺](computer-vision-how-to-install-containers.md)容器部署到 Azure[容器實體 。](https://docs.microsoft.com/azure/container-instances/) 此過程演示了計算機視覺資源的創建。 然後,我們討論拉關聯的容器映射。 最後,我們強調從瀏覽器執行兩者業務流程的能力。 使用容器可以將開發人員的注意力從管理基礎結構轉移到專注於應用程式開發上。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]